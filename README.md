# windows 加固脚本

基于 https://gist.github.com/mackwage/08604751462126599d7e52f233490efe 修改，待规模测试后提交给上游。

* 取消 Block executable files from running unless they meet a prevalence, age, or trusted list criterion
Enabled 注释，并修改为 AuditMode。没实际测过 Enabled，但是目测还是不拦截只记录合适一点。  
如果想试下开启拦截，可以改回 Enabled。    

* 增加 attack surface reduction 规则  
Use advanced protection against ransomware  
Block Office communication application from creating child processes

* 注释掉了 Prevent sharing of local drives via Remote Desktop Session Hosts  
这个远程桌面有时候要用到挂载本地盘

* Block Win32 binaries from making netconns when they shouldn't  
增加对应文件的 SysWOW64 路径  
参照 https://lolbas-project.github.io/ 增加其他带下载功能的系统自带程序禁止联网规则  
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
Winword.exe  
Wsl.exe  
增加 lolbin 中没有的 inseng.dll

* Uninstall common extra apps found on a lot of Win10 installs  
This will prevent these apps from being reinstalled on new user first logon  
注释掉相关卸载操作

* 添加 Auditpol subcategory 对应中文名称命令，否则中文环境系统会报错。  

* 新增了部分审核策略，详见 ::相对上游新增部分，更多类型可参考[微软文档](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/advanced-security-audit-policy-settings)自行添加。  

* BCDEDIT 增加一行  
BCDEDIT /set loadoptions ENABLE_INTEGRITY_CHECKS  

* 修改防火墙策略  
netsh advfirewall set publicprofile firewallpolicy blockinboundalways,allowoutbound  
为  
netsh advfirewall set allprofiles firewallpolicy blockinbound,allowoutbound  
原策略会无视如自定义白名单策略  
profile 有三种，all 对应全部   
Domain 对应域网络  
Private 对应专用网络  
Public 对应共用网络  
部分场景下需要添加远程桌面防火墙例外，可以再加一行  
netsh firewall set service remotedesktop enable  

* 反 mimikatz 相关  
[参照](https://medium.com/blue-team/preventing-mimikatz-attacks-ed283e7ebdd5)增加  
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest" /v Negotiate /t REG_DWORD /d 0 /f  
Debug Privilege 需要修改组策略，未添加  
Restricted Admin Mode 需要远程桌面连接的时候加参数，未添加  
Credential Caching 需要在域控上配置组策略，未添加  
Protected Users Group 需要在域控上配置组策略，未添加

* SmartScreen 级别从 Block 修改为 Warn  
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v ShellSmartScreenLevel /t REG_SZ /d Warn /f  

* 新增关闭 Windows 默认共享,见 :: Disable Shares

* 取消如下几项注释  
Enable Windows Defender real time monitoring  
Restrict privileged local admin tokens being used from network  
Ensure outgoing secure channel traffic is encrytped  
