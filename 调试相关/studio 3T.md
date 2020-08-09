1.无限试用

创建studio3t.bat文件

```bat
j@echo off
ECHO 重置Studio 3T的使用日期......
FOR /f "tokens=1,2,* " %%i IN ('reg query "HKEY_CURRENT_USER\Software\JavaSoft\Prefs\3t\mongochef\enterprise" ^| find /V "installation" ^| find /V "HKEY"') DO ECHO yes | reg add "HKEY_CURRENT_USER\Software\JavaSoft\Prefs\3t\mongochef\enterprise" /v %%i /t REG_SZ /d ""
ECHO 重置完成, 按任意键退出......
pause>nul
exit
```

放置到   `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp`  运行即可