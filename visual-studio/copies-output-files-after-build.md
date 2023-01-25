# How to copy output files with post-build event

## Scenario

For my console application, I'd like to have a structured output folder. This means, on root folder, only main files should be there, e.g., `.exe` and `.config` files. The root folder should contain a lib subfolder for all library files (from system or third party).

## Solution

It can be achieved with post-build event in vs project setting

```console
if not exist "$(ProjectDir)bin\Setup" mkdir "$(ProjectDir)bin\Setup"
if not exist "$(ProjectDir)bin\Setup\lib" mkdir "$(ProjectDir)bin\Setup\lib"
xcopy "$(TargetPath)" "$(ProjectDir)bin\Setup" /Y
xcopy "$(TargetDir)*.config" "$(ProjectDir)bin\Setup" /Y
xcopy "$(TargetDir)*.dll" "$(ProjectDir)bin\Setup\lib" /Y
```
