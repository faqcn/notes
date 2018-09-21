#PowerCLI

###常用命令

-新建虚拟机

	New-VM -Name LAB-K8S-NODE02 -VMHost 10.255.240.100 -Description LAB-K8S-NODE02 -Datastore OKSTOR-OFDS1016-FRA-1T -NumCPU 4 -MemoryGB 8 -DiskGB 20 -NetworkName "DPG-240" -Floppy -CD -DiskStorageFormat Thin -GuestID centos7_64Guest

	New-VM -Name aaaa-t -VMHost 10.255.240.100 -Description LAB-K8S-NODE02 -Datastore OKSTOR-OFDS1016-FRA-1T -NumCPU 4 -MemoryGB 8 -DiskGB 20 -NetworkName "DPG-240" -Floppy -CD -DiskStorageFormat Thin -GuestID centos7_64Guest

-Get-VM

	get-vm k8s-master?,k8s-node? |stop-vm -confirm:$false
	get-vm k8s-master?,k8s-node? |Remove-VM  -DeletePermanently
	get-vm k8s-master?,k8s-node? |Remove-VM  -DeletePermanently -confirm:$false
	get-vm *rancher* |stop-vm -confirm:$false |Remove-VM  -DeletePermanently -confirm:$false
	get-vm *240.90 |Format-Table -Property name
	get-vm *240.90 |Format-Table -Property PowerState
	get-vm *240.90 |Where {$_.PowerState -eq "PoweredOn"} 
	get-vm *240.90 |Where-Object -FilterScript {$_.PowerState -eq "PoweredOn"}
	get-vm *240.90 |select PowerState
	get-vm *k8s* | Where-Object {$_.PowerState -match "PoweredOff"} |Format-Table -Property name 
	get-vm *240.90 | foreach{$_.Name}
	get-vm *k8s* | foreach{$_.Name} |select -Last 1
	get-vm *k8s* | foreach{$_.Name} |select -Last 2
	Get-Datastore| Where-Object {$_.Name -notmatch "local|datastore|XIO-RAID5"}
	Get-Datastore| Where-Object {$_.Name -match "OKSTOR"}

	Write-Host '确认要删除虚拟机$full吗？'
	Write-output "确认要删除虚拟机$full 吗？"

###获取GuestID

	[VMware.Vim.VirtualMachineGuestOsIdentifier].GetEnumValues()


###Windows 安装 PowerCLI

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

###Linux 安装 PowerCLI

-1、安装 powershell

	 #配置yum源
	 curl https://packages.microsoft.com/config/rhel/7/prod.repo > /etc/yum.repos.d/microsoft.repo 
	 yum install powershell

-2、安装VMware.PowerCLI 模块

	#运行powershell
	pwsh
	Save-Module -Name VMware.PowerCLI -Path ~/.local/share/powershell/Modules/
	Install-Module -Name VMware.PowerCLI

###PowerCLI常用脚本

-1、新建虚拟机

	#new-vm.ps1
	$Cluster = Cluster
	$NetworkName = DPG-240
	$VmName = Read-Host "请输入新建虚拟机的名称"
	$NumCPU = Read-Host "请输入新建虚拟机的CPU数量"
	$MemoryGB = Read-Host "请输入新建虚拟机的内存大小"
	$DiskGB = Read-Host "请输入新建虚拟机的磁盘大小"
	Write-Host "新建虚拟机可选择的存储有"
	Write-Host "1.XIO-RAID5   2.OKSTOR-OFDS1016-RAID6   3.OKSTOR-OFDS1016-Cache"
	$Datastore = Read-Host "请确认您的选择"
	switch ($Datastore)
	 {
	  1 {$Datastore = "XIO-RAID5"}
	  2 {$Datastore = "OKSTOR-OFDS1016-RAID6"}
	  3 {$Datastore = "OKSTOR-OFDS1016-Cache"}
	 }
	New-VM -Name $VmName -ResourcePool $Cluster -Description $VmName -Datastore $Datastore -NumCPU $NumCPU -MemoryGB $MemoryGB -DiskGB $DiskGB -NetworkName $NetworkName -Floppy -CD -DiskStorageFormat Thin -GuestID $GuestID



-2、删除虚拟机

	#rm-vm.ps1
	#从硬盘删除
	$VmName = Read-Host "请输入需要删除的虚拟机的名称"
	$FullVmName = get-vm *$VmName* | foreach{$_.Name} 
	$PowerState = get-vm $FullVmName | Where {$_.PowerState -eq "PoweredOn"} 
	$Choice =  Read-Host "确认要删除名为:$FullVmName 的虚拟机吗？y/n"
	if ($Choice -eq "y")
	{
	  if ($PowerState -eq $null)
	  {
	        get-vm $FullVmName |Remove-VM  -DeletePermanently -confirm:$false
	  }
	  else
	  {
	        get-vm $FullVmName |stop-vm -confirm:$false |Remove-VM  -DeletePermanently -confirm:$false
	  }
	}
	else
	{
	        Write-output "退出"
	}
