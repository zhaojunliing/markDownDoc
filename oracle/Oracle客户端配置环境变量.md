变量名：ORACLE_HOME
变量值：D:\Program Files\instantclient_11_2
变量名：TNS_ADMIN
变量值：D:\Program Files\instantclient_11_2
变量名：NLS_LANG
变量值：SIMPLIFIED CHINESE_CHINA.ZHS16GBK
修改Path变量，在后面添加 D:\Program Files\instantclient_11_2



```bash
%1 mshta vbscript:CreateObject("Shell.Application").ShellExecute("cmd.exe","/c %~s0 ::","","runas",1)(window.close)&&exit
cd /d "%~dp0"

@echo off
echo  请输入ORACLE instantclient安目录
set /p answer=  
wmic ENVIRONMENT create name="ORACLE_HOME",username="<system>",VariableValue="%answer%"
wmic ENVIRONMENT create name="TNS_ADMIN",username="<system>",VariableValue="%answer%"
wmic ENVIRONMENT create name="NLS_LANG",username="<system>",VariableValue="SIMPLIFIED CHINESE_CHINA.ZHS16GBK"
wmic ENVIRONMENT where "name='PATH' and username='<system>'" set VariableValue="%path%;%%ORACLE_HOME%%;"
echo 设置完成 按下任意键退出
pause
```

