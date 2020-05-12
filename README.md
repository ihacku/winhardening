# windows 加固脚本

基于 https://gist.github.com/mackwage/08604751462126599d7e52f233490efe 修改，待规模测试后提交给上游。

* Block executable files from running unless they meet a prevalence, age, or trusted list criterion
Enabled   
修改为 AuditMode，没实际测过 Enabled，但是目测还是不拦截只记录合适一点，如果想试下开启拦截，可以改回 Enabled

* 新增 attack surface reduction 规则  
Use advanced protection against ransomware  
Block Office communication application from creating child processes

* 注释掉了 Prevent sharing of local drives via Remote Desktop Session Hosts  
这个远程桌面有时候要用到挂载本地盘

* Block Win32 binaries from making netconns when they shouldn't 增加 SysWOW64 路径  
参照 https://lolbas-project.github.io/ 增加  
Bitsadmin.exe  
Certutil.exe  
Esentutl.exe  
Expand.exe  
Extrac32.exe  
Findstr.exe  
Ieexec.exe  
Makecab.exe  
Replace.exe  
Excel.exe  
Powerpnt.exe  
Squirrel.exe  
Update.exe  
Winword.exe  
Wsl.exe  

* Uninstall common extra apps found on a lot of Win10 installs  
注释掉相关卸载操作
