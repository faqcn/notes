# notes
###PowerCLI

-1、打开WindowsPowerShell

 Find-Module -Name VMware.PowerCLI
 mkdir  ~\Documents\WindowsPowerShell\Modules

-2、下载PowerCLI

 Save-Module -Name VMware.PowerCLI -Path ~\Documents\WindowsPowerShell\Modules\


-3、安装
 Install-Module -Name VMware.PowerCLI
 提示“因为在此系统上禁止运行脚本”
 Set-ExecutionPolicy RemoteSigned
 
 Set-PowerCLIConfiguration -Scope User -ParticipateInCEIP $true
 Import-Module VMware.PowerCLI
-4、快捷键
 C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -noe -c "Import-Module VMware.PowerCLI"

###PowerCLI for linux
-1、安装 powershell
  #配置yum源
  curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/microsoft.repo 
  yum install powershell
-2、安装VMware.PowerCLI 模块
 Install-Module -Name VMware.PowerCLI
 Install-Module -Name VMware.PowerCLI
 Install-Module -Name VMware.PowerCLI
 Install-Module -Name VMware.PowerCLI
