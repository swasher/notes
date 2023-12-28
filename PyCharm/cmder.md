General info about integration is here

[link](https://github.com/cmderdev/cmder/wiki/Seamless-IntelliJ-based-Integration)


### Cmder

cmd.exe /k "%CMDER_ROOT%\vendor\init.bat"
cmd.exe /k "%CMDER_ROOT%\vendor\bin\cmder_shell.cmd"


не знаю, что правильнее, работает и то и то

also,

```
cmd.exe /k ""%CMDER_ROOT%\vendor\init.bat""
```

The double-double-quotes are intentional, as they counteract the missing double quotes in the environment variable.


### Cmder + powershell

```
cmd.exe /c pwsh.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -NoExit -Command "Invoke-Expression '. ''%CMDER_ROOT%/vendor/profile.ps1'''"
```

### Cmder + ConEmu-Maximus5

"cmd.exe" /k set ConEmuDir=""%CMDER_ROOT%"\vendor\conemu-maximus5&&Call %CMDER_ROOT%\vendor\init.bat"
