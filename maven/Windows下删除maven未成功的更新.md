## Windows下删除maven未成功的更新
> 将下面的脚本存为批处理文件，修改对应的maven仓库地址后，执行即可。

```powershell
@echo off 
rem create by sunhao(sunhao.java@gmail.com) 
rem crazy coder 

rem -- 这里写你的仓库路径 
set REPOSITORY_PATH=D:\.m2\repository
rem -- 正在搜索... 
for /f "delims=" %%i in ('dir /b /s "%REPOSITORY_PATH%\*lastUpdated*"') do ( 
    del /s /q %%i 
) 
rem 搜索完毕 
pause
```

