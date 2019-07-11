# Windows 定时备份数据库脚本

注意使用GB2312编码格式的文件进行保存,修改相关的用户名密码路径即可使用。
```powershell
@echo off
echo ================================================
echo  Windows环境下Oracle数据库的自动备份脚本
echo  1. 使用当前日期命名备份文件。
echo  2. 自动删除7天前的备份。
echo ================================================
::以“YYYYMMDD”格式取出当前时间。
set BACKUPDATE=%date:~0,4%%date:~5,2%%date:~8,2%
set CURTIME=%time:~0,2%
REM 小时数如果小于10，则在前面补0
if "%CURTIME%"==" 0" set CURTIME=00
if "%CURTIME%"==" 1" set CURTIME=01
if "%CURTIME%"==" 2" set CURTIME=02
if "%CURTIME%"==" 3" set CURTIME=03
if "%CURTIME%"==" 4" set CURTIME=04
if "%CURTIME%"==" 5" set CURTIME=05
if "%CURTIME%"==" 6" set CURTIME=06
if "%CURTIME%"==" 7" set CURTIME=07
if "%CURTIME%"==" 8" set CURTIME=08
if "%CURTIME%"==" 9" set CURTIME=09
set CURTIME=%CURTIME%%time:~3,2%%time:~6,2%

::设置用户名、密码和要备份的数据库。
set USER=test
set PASSWORD=test
set DATABASE=192.168.10.10:1521/orcl
::set nls_lang=american_america.al32utf8
::创建备份目录。
if not exist "D:\backup\data\%BACKUPDATE% "     mkdir D:\backup\data\%BACKUPDATE%
if not exist "D:\backup\log\%BACKUPDATE% "      mkdir D:\backup\log\%BACKUPDATE%
set DATADIR=D:\backup\data\%BACKUPDATE%
set LOGDIR=D:\backup\log\%BACKUPDATE%
set DELDATADIR=D:\backup\data
set DELLOGDIR=D:\backup\log
exp %USER%/%PASSWORD%@%DATABASE%  file=%DATADIR%\%USER%_%BACKUPDATE%%CURTIME%.dmp log=%LOGDIR%\log_%BACKUPDATE%%CURTIME%.log
echo 数据库文件备份成功，所在目录为: %DATADIR%
echo 正在查找旧备份文件创建时间...
::删除30天前的备份。
::先判断要删除的文件是否存在
for /r %DELDATADIR% %%a in (*) do for /f "tokens=1,2* delims= " %%b in (
'dir /tc "%%a" ^| find /i "%%~nxa"'
) do (
echo "%%b %%c"
set /a dataCount+=1
)
for /r %DELLOGDIR% %%a in (*) do for /f "tokens=1,2* delims= " %%b in (
'dir /tc "%%a" ^| find /i "%%~nxa"'
) do (
echo "%%b %%c"
set /a logCount+=1
)
if %dataCount% gtr 1 goto beginCleandata
:iscleanlog
if %logCount% gtr 1 goto beginCleanlog
if %dataCount% equ 1 goto end
if %logCount% equ 1 goto end
::删除文件、删除空文件夹
:beginCleandata
forfiles /p "%DELDATADIR%" /s /m *.* /d -30 /c "cmd /c del @path"
for /f "tokens=*" %%a in ('dir /b /ad /s "%DELDATADIR%"^|sort /r') do rd "%%a"
goto iscleanlog
:beginCleanlog
forfiles /p "%DELLOGDIR%" /s /m *.* /d -30 /c "cmd /c del @path"
for /f "tokens=*" %%a in ('dir /b /ad /s "%DELLOGDIR%"^|sort /r') do rd "%%a"
echo 旧备份文件删除成功!
pause
exit
:end
echo 无旧备份文件。
echo 执行成功。
pause
```

摘抄自博客 https://blog.csdn.net/wht21888/article/details/78419917