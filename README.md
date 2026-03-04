# Unity Starter Kit
This repository provides essential base files for starting a Unity project, including Git configurations and a list of must-have features.
# Must-have features
To learn how to install UPM packages from a Git URL, read [this](https://docs.unity3d.com/6000.3/Documentation/Manual/upm-ui-giturl.html).
### • TMPro Dynamic Data Cleaner
```
https://github.com/STARasGAMES/tmpro-dynamic-data-cleaner.git#upm
```
### • SmartMergeRegistrator
<details>
  <summary>View</summary>
  
UnityYAMLMerge – tool to merge scene and prefab files in a semantically correct way. Read more [here](https://docs.unity3d.com/6000.3/Documentation/Manual/SmartMerge.html). Just add this script to the "Editor" folder of your "Scripts" folder.
```C#
using System;
using UnityEditor;
using UnityEngine;

[InitializeOnLoad]
public class SmartMergeRegistrator
{
    private const string SmartMergeRegistratorEditorPrefsKey = "smart_merge_installed";
    private const int Version = 1;
    private static readonly string VersionKey = $"{Version.ToString()}_{Application.unityVersion}";

    [MenuItem("Tools/Git/SmartMerge registration")]
    private static void SmartMergeRegister()
    {
        try
        {
            var unityYamlMergePath = EditorApplication.applicationContentsPath + "/Tools" + "/UnityYAMLMerge.exe";
            ExecuteGitWithParams("config merge.unityyamlmerge.name \"Unity SmartMerge (UnityYamlMerge)\"");
            ExecuteGitWithParams($"config merge.unityyamlmerge.driver \"\\\"{unityYamlMergePath}\\\" " +
                                 $"merge -h -p --force --fallback none %O %B %A %A\"");
            ExecuteGitWithParams("config merge.unityyamlmerge.recursive binary");
            EditorPrefs.SetString(SmartMergeRegistratorEditorPrefsKey, VersionKey);
            Debug.Log($"Successfully registered UnityYAMLMerge with path {unityYamlMergePath}");
        }
        catch (Exception e)
        {
            Debug.Log($"Fail to register UnityYAMLMerge with error: {e}");
        }
    }

    [MenuItem("Tools/Git/SmartMerge unregistration")]
    private static void SmartMergeUnRegister()
    {
        ExecuteGitWithParams("config --remove-section merge.unityyamlmerge");
        Debug.Log($"Successfully unregistered UnityYAMLMerge");
    }

    static SmartMergeRegistrator()
    {
        var installedVersionKey = EditorPrefs.GetString(SmartMergeRegistratorEditorPrefsKey);
        if (installedVersionKey != VersionKey)
        {
            SmartMergeRegister();
        }
    }

    private static void ExecuteGitWithParams(string param)
    {
        var processInfo = new System.Diagnostics.ProcessStartInfo("git")
        {
            UseShellExecute = false,
            WorkingDirectory = Environment.CurrentDirectory,
            RedirectStandardOutput = true,
            RedirectStandardError = true,
            CreateNoWindow = true
        };

        var process = new System.Diagnostics.Process();
        process.StartInfo = processInfo;
        process.StartInfo.FileName = "git";
        process.StartInfo.Arguments = param;
        process.Start();
        process.WaitForExit();

        if (process.ExitCode != 0)
        {
            throw new Exception(process.StandardError.ReadLine());
        }

        process.StandardOutput.ReadLine();
    }
}
```
</details>

<br>

_Licensed with unlicense license_
