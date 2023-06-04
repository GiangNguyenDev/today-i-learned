```console
C:\Windows\System32\inetsrv\appcmd stop apppool /apppool.name:"DefaultAppPool"
C:\Windows\System32\inetsrv\appcmd stop site /site.name:"Default Web Site"
C:\Windows\System32\inetsrv\appcmd start site /site.name:"Default Web Site"
C:\Windows\System32\inetsrv\appcmd start apppool /apppool.name:"DefaultAppPool"
PAUSE
```