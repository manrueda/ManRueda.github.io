---
layout: post
title: Agile Keychain decryption with JavaScript
date: '2015-10-19 00:23:54'
tags:
- nodejs
- buffer
- agilebit
- 1password
- security
- crypto
---

On this publication i'm going to talk about the [Agile Keychain Design](https://support.1password.com/agile-keychain-design/). The structure, the decryption flow and implementation.

###### A little bit of history
This sensitive data storage desing was created by [AgileBits](https://agilebits.com/) on 2008 to replace the OS X Keychain in the [1Password](https://agilebits.com/onepassword) application.
The main benefits of replace the OS X Keychain was:

* Multi OS support
* Change DES to AES as encryption standard
* Performance improvement compared to OS X KeyChain.

## The structure
The storage is based on a zip-like file with the ```.agilekeychain_zip``` extension. And have an internal folder structure like this:

```language-ruby
myStorage.agilekeychain_zip
+-- config
    +-- domains
    +-- use-thumbnails
    +-- version
+-- data
    +-- default
        +-- 1password.keys
        +-- *.1password
        +-- content.js
        +-- encryptionKeys.js
    +-- <more profiles>
```
### encryptionKeys.js
Take a look to the ```encryptionKeys.js```:
```language-javascript
{
  "list": [{
    "data": "U2FsdGVkX19cm85vzXmRCZyaVYd...",
    "validation": "U2FsdGVkX1/YdmmuN6l2tJ6...",
    "identifier": "30737A49AF124394B4CF97F65761769D",
    "level": "SL5",
    "iterations": 10000
  }, {
    "data": "U2FsdGVkX19s+dZpjkrrUeUsNWO...",
    "validation": "U2FsdGVkX1/8LVqKXktw5MULM1...",
    "identifier": "A866C664DD424046A26487AAB677308F",
    "level": "SL3",
    "iterations": 10000
  }],
  "SL3": "A866C664DD424046A26487AAB677308F",
  "SL5": "30737A49AF124394B4CF97F65761769D"
}
```
The ```list``` property lists all the key to decrypt the passwords. 
The ```level``` represent the security level related to the password content. The ```data``` contains a BASE-64 representation of the content key encrypted with the master key with [PBKDF2](http://en.wikipedia.org/wiki/PBKDF2) the number of iterations defined in ```iterations```.
The ```validation``` is the decrypted key encrypted with it self, to validate the decryption process.

*The ```1password.keys``` contains the same information but in a XML format*
### content.js
This JSON lists all the content available in the keychain, this file only give you the ```UUID```, ```Type``` and ```Name```.
### *.1password
All the files with this extension are *content keys* and are a JSON with this properties:

* keyID: ```UUID``` of the content key to decrypt the password.
* uuid: ```UUID``` of the password.
* securityLevel: Define with *content key* need to decrypt.
* encrypted: BASE-64 data of the password to decrypt.

There are other properties of 1Password, like types, names, URLs, etc. But there are not critical to the decrypt process.

```language-javascript
{
  "keyID": "30737A49AF124394B4CF97F65761769D",
  "locationKey": "someSite.com",
  "typeName": "webforms.WebForm",
  "location": "https://someSite.com/Account/Login",
  "uuid": "CD4D7DE21F414354A01A6ABA626CA2AB",
  "createdAt": 1429537038,
  "title": "someSite.com",
  "securityLevel": "SL5",
  "openContents": {
    "autosubmit": "default"
  },
  "updatedAt": 1429537040,
  "txTimestamp": 1429537040,
  "contentsHash": "1836a185",
  "encrypted": "U2FsdGVkX1/rlHC..."
}
```
## Decryption process
In the short way to decrypt a content you need to follow this steps.

* Convert the master password, content key and content data in buffers.
* Divide the content key in the salt and data.
* Generate the derived key with *PBKDF2*, using the *iterations* and the master key.
* Divide the derived key in the AES key and the AES IV.
* Decrypt the content key data with *AES-128-CBC* using the AES Key and AES Iv and get the Raw Key.
* Divide the content data in the salt and the data.
* Derive the Raw Key with *MD5* using the content data salt, and get the key and iv.
* Decrypt the 'content data' data with *AES-128-CBC* using the key and iv just created.
* Parse the result data as JSON.

This explanation could be a little bit hard to understand, because of that... here is this implemented in JS with Node JS.

```language-javascript
var crypto = require('crypto');

var content = 'U2FsdGVkX1/rlHCO4K1g3M...';
var contentKey = 'U2FsdGVkX19s+dZpjkrrUeU...';
var masterKey = 'MyMasterKey';
var iterations = 10000;
var result;

var masterKeyBuffer = new Buffer(masterKey);
var contentKeyBuffer = new Buffer(contentKey, 'base64');
var contentBuffer = new Buffer(content, 'base64');

var derivedKey = crypto.pbkdf2Sync(masterKeyBuffer, contentKeyBuffer.slice(8, 16), iterations, 32);

var decryptedContentKey = decrypt(contentKeyBuffer.slice(16),  derivedKey.slice(0, 16), derivedKey.slice(16, 32));

var passwordAux = deriveKey(decryptedContentKey, contentBuffer.slice(8, 16));

result = JSON.parse(decrypt(contentBuffer.slice(16), passwordAux.key, passwordAux.iv));

console.log(result);

function decrypt(data, key, iv){
  var cipher = crypto.createDecipheriv('aes-128-cbc', key, iv);

  var upBuf = cipher.update(data);
  var finBuf = cipher.final();

  return Buffer.concat([upBuf, finBuf]);
}

function deriveKey(key, salt){
  var rounds = 2;
  var data = Buffer.concat([key, salt]);
  var md5Hashes = [[],[]];
  var md5sum = crypto.createHash('md5');
  md5sum.update(data);
  var sum = md5sum.digest();
  md5Hashes[0] = sum;
  for(var i = 1; i < rounds; i++){
    md5sum = crypto.createHash('md5');
    md5sum.update(Buffer.concat([md5Hashes[i - 1], data]));
    sum = md5sum.digest();
    md5Hashes[i] = sum;
  }
  return {
    key:md5Hashes[0],
    iv: md5Hashes[1]
  };

}
```
The result logged in the console will be something like this, the result can be different for different types of passwords.
```language-javascript
{
  htmlAction: 'https://mysite.com/',
  htmlID: 'passwordExpired',
  htmlName: 'passwordExpired',
  htmlMethod: 'POST',
  passwordHistory: [{
    value: '4FgrFhedhewgM8k78gd8G_',
    time: 1435664836
  }, {
    value: 'MyRealPassword',
    time: 1443537200
  }],
  URLs: [{
    url: 'https://mysite.com/'
  }],
  fields: [{
    type: 'P',
    name: 'stateBean.nuPassword1',
    value: 'MyRealPassword',
    designation: 'password'
  }]
}
```

In this GitHub repo is an real implementation of this in a basic Key Chain manager.
https://github.com/ManRueda/1password-manager