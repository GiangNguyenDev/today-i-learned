# How to create file with VBA

```vb
Option Explicit

' Go to Tools -> References... and check "Microsoft Scripting Runtime" to be able to use
' the FileSystemObject which has many useful features for handling files and folders
Public Function SaveTextToFile()

    ' The advantage of correctly typing fso as FileSystemObject is to make autocompletion
    ' (Intellisense) work, which helps you avoid typos and lets you discover other useful
    ' methods of the FileSystemObject
    Dim fso As FileSystemObject
    Set fso = New FileSystemObject
    
    Set curDb = Application.CurrentDb
    dbFullPath = fso.GetAbsolutePathName(curDb.Name)
    Dim filePath As String
    filePath = dbFullPath & ".locked"
    
    Dim fileStream As TextStream

    ' Here the actual file is created and opened for write access
    Set fileStream = fso.CreateTextFile(filePath)

    ' Write something to the file
    fileStream.WriteLine "File is currently used from another user and therefore locked."

    ' Close it, so it is not locked anymore
    fileStream.Close

    ' Here is another great method of the FileSystemObject that checks if a file exists
    If fso.FileExists(filePath) Then
        MsgBox "Yay! The file was created! :D"
    End If

    ' Explicitly setting objects to Nothing should not be necessary in most cases, but if
    ' you're writing macros for Microsoft Access, you may want to uncomment the following
    ' two lines (see https://stackoverflow.com/a/517202/2822719 for details):
    Set fileStream = Nothing
    Set fso = Nothing
End Function
```

---

Source: https://stackoverflow.com/a/49674605/1986935