---
layout: post
title: TFS Grunt custom policy
date: '2015-09-22 03:11:00'
tags:
- tfs
- grunt
- policy
- server
- c
- net
- windows
---

On the last few weeks i was working in a new project and everything was awesome...  but we only were 4 guys coding. On the last week more people start coding over our code and we need to put a bunch of rules and validations to keep the project on rails.

The project it's a SPA made it with angular, all our code rules validations are made with JSHint, JSCS, HTMLHint, etc and we have test made with jasmin. We run all this stuff with grunt's tasks... so far still all was great, the build fails when any validation fails. So i decide to not have failed builds because of that. So i put my mind in custom policies research and end up with a policy that run a grunt task and reject the check-in if grunt fails.

Because of the custom polices runs locally, we don't have a performance issue in the TFS build controller.


## Policy class structure
The policy class must inherit from ```PolicyBase``` and have the ```Serializable``` attribute. This force you to overwrite some methods.

```language-csharp
[Serializable]
public class GruntRunnerPolicy: PolicyBase {

  public override string Description {
    get {
      //Description of the policy
    }
  }

  public override string InstallationInstructions {
    get {
      //Text to display to the user when he doesn't have the policy in his system.
    }
  }

  public override string Type {
    get {
      //Type of policy, this string will be displayed in the policies combo
    }
  }

  public override string TypeDescription {
    get {
      //Description of the type of policy
    }
  }

  public override bool Edit(IPolicyEditArgs args) {
    return true;
  }

  public override PolicyFailure[] Evaluate() {
    //This method performance the check-in evaluation
  }

  public override void Activate(PolicyFailure failure) {
    //This method is executed when the user double-clicks the UI of the policy
  }

  public override void DisplayHelp(PolicyFailure failure) {
    //This method is executed when the user ask for the policy help (F1)
  }
}
```

## Policy evaluation
The idea is to run the task and evaluate the exit code to evaluate the result of the execution. To accomplish this the policy needs to:

* Found the Grunt's executable (maybe it's not in the PATH)
* Found the Gruntfile.js that affects the check-in files
* Detect the task to run
* Run it and evaluate the result

With this method we can find for the *Grunt executable*

```language-csharp
private string FindGruntDirectory() {
  Process whereProcess = new Process();
  whereProcess.StartInfo.FileName = "where.exe";
  whereProcess.StartInfo.Arguments = "grunt";
  whereProcess.StartInfo.UseShellExecute = false;
  whereProcess.StartInfo.CreateNoWindow = true;
  whereProcess.StartInfo.RedirectStandardOutput = true;
  whereProcess.StartInfo.WorkingDirectory = System.Environment.SystemDirectory;
  whereProcess.Start();

  var path = string.Empty;
  while (!whereProcess.StandardOutput.EndOfStream) {
    String s = whereProcess.StandardOutput.ReadLine();
    if (s != "") {
      if (path == string.Empty) {
        path = s;
      }
      if (s.EndsWith(".cmd")) {
        path = s;
      }
    }
  }
  whereProcess.WaitForExit();
  return path;
}
```

To find the *Gruntfile.js* that contains the task, the idea is put a *.json* file at the same level of the *Gruntfile.js*, let say... *GruntRunnerPolicy.json*.

That said, the policy needs to search up to the root of the file system for each check-in file, searching for the *.json* file and at the same level can find the *Gruntfile.js*.

```language-csharp
public static string SearchGruntParentDirectory(string path) {
  if (path == Path.GetFullPath(path + "\\..")) {
    return null;
  }

  var files = Directory.GetFiles(path, "GruntRunnerPolicy.json");
  if (files.Length == 0) {
    return SearchGruntParentDirectory(Path.GetFullPath(path + "\\.."));
  } else {
    return path;
  }
}
```

My idea was to put the name of the task in the *GruntRunnerPolicy.json*, maybe was a justification for put this files in the TFS hahaha.
Something simple like this:
```language-javascript
{
  "GruntTask":"validateMyCheckIn"
}
```

The last thing to do is run the task and expect for the result:

```language-csharp
public int Run(string GruntDirectory, string WorkingDirectory, string Task, List<string> Parameters) {
  var log = String.Empty;
  var errLog = String.Empty;
  
  var processInfo = new ProcessStartInfo("cmd.exe", "/c " + GruntDirectory + " --no-color " + Task + " " + String.Join(" ", Parameters));
  processInfo.CreateNoWindow = true;
  processInfo.UseShellExecute = false;
  processInfo.RedirectStandardError = true;
  processInfo.RedirectStandardOutput = true;
  processInfo.WorkingDirectory = WorkingDirectory;

  var process = Process.Start(processInfo);
  process.OutputDataReceived += (object sender, DataReceivedEventArgs e) =>
    log = log + e.Data + System.Environment.NewLine;

  process.BeginOutputReadLine();
  process.ErrorDataReceived += (object sender, DataReceivedEventArgs e) =>
    errLog = errLog + (String.IsNullOrEmpty(e.Data) ? string.Empty : e.Data + System.Environment.NewLine);

  process.BeginErrorReadLine();
  process.WaitForExit();

  var success = process.ExitCode;
  process.Close();
  return success;
}
```

This method returns the exit code to analyze it, saves the console output and error output.
The console log can be use to return to the user the error generated by the task.

I leave the source of this package in this repository: [GruntRunnerPolicy](https://github.com/ManRueda/GruntRunnerPolicy)