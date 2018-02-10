---
title: Unity3D特定文件的打开方式
date: 2018-02-10 21:49:52
tags: Unity3D
---

> 在Unity3D编辑器中，双击Shader文件默认的打开应用是和c#脚本一致，一般是Visual Studio。但是Visual Studio原生并没有提供对ShaderLab的语法支持。一般情况下都是用Sublime Text或者Visual Studio Code来作为Shader的编辑器。但是如果能够双击Shader文件直接打开对应的编辑器就再好不过了，后来知道Unity3D提供了编辑器扩展，允许我们监听文件双击打开的消息。是通过 <u>`UnityEdtitor.Callbacks`</u> 来实现的。具体见如下代码


### 1. 示例代码

``` csharp
using UnityEngine;
using UnityEditor;
using UnityEditor.Callbacks;
using System.IO;

public class FileopenHandler
{
    public static string sublimeTextPath = @"D:\Develop_Tools\Sublime Text 3\sublime_text.exe";
    [OnOpenAsset]
    public static bool OnOpenShaderFile(int instanceId, int line)
    {
        if (EditorUtility.InstanceIDToObject(instanceId) is Shader)
        {
            string assetPath = AssetDatabase.GetAssetPath(instanceId);
            string fullPath = Path.GetFullPath(assetPath);
            System.Diagnostics.Process process = new System.Diagnostics.Process();
            System.Diagnostics.ProcessStartInfo startInfo = new System.Diagnostics.ProcessStartInfo();
            startInfo.WindowStyle = System.Diagnostics.ProcessWindowStyle.Hidden;
            startInfo.FileName = sublimeTextPath;
            startInfo.Arguments = "\"" + fullPath + "\"";
            process.StartInfo = startInfo;
            process.Start();
            return true;
        }
        return false;
    }
}
```

### 2.解释说明

以上代码关键部分在于`[OnOpenAsset]`这个属性。这里告诉Unity3D编辑器，当双击打开一个资源的时候执行下面这个函数。逻辑里面就通过参数实例ID拿到这个资源，判断如果是`Shader`类型的时候，就使用SublimeText编辑器来打开， 其中`sublimeTextPath`保存的是自己电脑上的Sublime路径。需要替换成自己电脑上的路径。