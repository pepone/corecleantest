This repository shows a problem with CoreClean MSBuild task not
deleting CopyLocal files when the output path is not the project
directory.


- Restore nuget packages

```
nuget restore msbuild\ConsoleApp1.sln
```

- Build the project

```
C:\Users\ppgut\Desktop\test>MSBuild msbuild\ConsoleApp1.csproj /p:Platform=AnyCPU
Microsoft (R) Build Engine version 15.6.85.37198 for .NET Framework
Copyright (C) Microsoft Corporation. All rights reserved.

Build started 4/12/2018 8:37:30 AM.
Project "C:\Users\ppgut\Desktop\test\msbuild\ConsoleApp1.csproj" on node 1 (default targets).
GenerateBindingRedirects:
  No suggested binding redirects from ResolveAssemblyReferences.
GenerateTargetFrameworkMonikerAttribute:
Skipping target "GenerateTargetFrameworkMonikerAttribute" because all output files are up-to-date with respect to the input files.
CoreCompile:
  C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\MSBuild\15.0\bin\Roslyn\csc.exe /noconfig /nowarn:1701,1702 /nostdlib+ /platform:anycpu32bitpreferred /errorreport:prompt /warn:4 /d
  efine:DEBUG;TRACE /highentropyva+ /reference:C:\Users\ppgut\Desktop\test\msbuild\packages\zeroc.ice.net.3.7.0\lib\Ice.dll /reference:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework
  \.NETFramework\v4.6.1\Microsoft.CSharp.dll" /reference:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.6.1\mscorlib.dll" /reference:"C:\Program Files (x86)\Referen
  ce Assemblies\Microsoft\Framework\.NETFramework\v4.6.1\System.Core.dll" /reference:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.6.1\System.Data.DataSetExtension
  s.dll" /reference:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.6.1\System.Data.dll" /reference:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\
  .NETFramework\v4.6.1\System.dll" /reference:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.6.1\System.Net.Http.dll" /reference:"C:\Program Files (x86)\Reference A
  ssemblies\Microsoft\Framework\.NETFramework\v4.6.1\System.Xml.dll" /reference:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.6.1\System.Xml.Linq.dll" /debug+ /deb
  ug:full /filealign:512 /optimize- /out:obj\Debug\ConsoleApp1.exe /ruleset:"C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\Team Tools\Static Analysis Tools\\Rule Sets\MinimumRecomm
  endedRules.ruleset" /subsystemversion:6.00 /target:exe /utf8output ..\Program.cs Properties\AssemblyInfo.cs "C:\Users\ppgut\AppData\Local\Temp\.NETFramework,Version=v4.6.1.AssemblyAttributes.cs"
  Using shared compilation with compiler from directory: C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\MSBuild\15.0\bin\Roslyn
_CopyFilesMarkedCopyLocal:
  Copying file from "C:\Users\ppgut\Desktop\test\msbuild\packages\zeroc.ice.net.3.7.0\lib\Ice.dll" to "..\Ice.dll".
  Copying file from "C:\Users\ppgut\Desktop\test\msbuild\packages\zeroc.ice.net.3.7.0\lib\Ice.pdb" to "..\Ice.pdb".
  Copying file from "C:\Users\ppgut\Desktop\test\msbuild\packages\zeroc.ice.net.3.7.0\lib\Ice.xml" to "..\Ice.xml".
  Creating "C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.csproj.CopyComplete" because "AlwaysCreate" was specified.
_CopyAppConfigFile:
  Copying file from "App.config" to "..\ConsoleApp1.exe.config".
CopyFilesToOutputDirectory:
  Copying file from "obj\Debug\ConsoleApp1.exe" to "..\ConsoleApp1.exe".
  ConsoleApp1 -> C:\Users\ppgut\Desktop\test\ConsoleApp1.exe
  Copying file from "obj\Debug\ConsoleApp1.pdb" to "..\ConsoleApp1.pdb".
Done Building Project "C:\Users\ppgut\Desktop\test\msbuild\ConsoleApp1.csproj" (default targets).
```

The task `_CopyFilesMarkedCopyLocal` copy the assemblies from the NuGet package to the output directory as expected

- Check the generated `msbuild\obj\Debug\ConsoleApp1.csproj.FileListAbsolute.txt`

```
cat msbuild\obj\Debug\ConsoleApp1.csproj.FileListAbsolute.txt
C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.csprojResolveAssemblyReference.cache
C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.csproj.CoreCompileInputs.cache
C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.csproj.CopyComplete
C:\Users\ppgut\Desktop\test\ConsoleApp1.exe.config
C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.exe
C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.pdb
C:\Users\ppgut\Desktop\test\ConsoleApp1.exe
C:\Users\ppgut\Desktop\test\ConsoleApp1.pdb
```

The files copied by `_CopyFilesMarkedCopyLocal` are not in this list and as a results are not clean by
CoreClean.

- Clean the project

```
C:\Users\ppgut\Desktop\test>MSBuild msbuild\ConsoleApp1.csproj /p:Platform=AnyCPU /t:Clean
Microsoft (R) Build Engine version 15.6.85.37198 for .NET Framework
Copyright (C) Microsoft Corporation. All rights reserved.

Build started 4/12/2018 8:50:10 AM.
Project "C:\Users\ppgut\Desktop\test\msbuild\ConsoleApp1.csproj" on node 1 (Clean target(s)).
CoreClean:
  Deleting file "C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.csprojResolveAssemblyReference.cache".
  Deleting file "C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.csproj.CoreCompileInputs.cache".
  Deleting file "C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.csproj.CopyComplete".
  Deleting file "C:\Users\ppgut\Desktop\test\ConsoleApp1.exe.config".
  Deleting file "C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.exe".
  Deleting file "C:\Users\ppgut\Desktop\test\msbuild\obj\Debug\ConsoleApp1.pdb".
  Deleting file "C:\Users\ppgut\Desktop\test\ConsoleApp1.exe".
  Deleting file "C:\Users\ppgut\Desktop\test\ConsoleApp1.pdb".
Done Building Project "C:\Users\ppgut\Desktop\test\msbuild\ConsoleApp1.csproj" (Clean target(s)).
```

- The files copied to output directory are still there

```
dir Ice.*
 Volume in drive C has no label.
 Volume Serial Number is 5476-13EF

 Directory of C:\Users\ppgut\Desktop\test

07/21/2017  03:17 PM           720,968 Ice.dll
07/21/2017  03:17 PM         1,771,008 Ice.pdb
07/21/2017  03:17 PM           474,330 Ice.xml
```
