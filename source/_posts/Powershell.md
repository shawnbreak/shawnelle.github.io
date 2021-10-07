---
title: Powershell
---

## Execution Policy

有三种: Bypass, RemoteSigned, Restricted

windows 10默认为 Restricted, 执行脚本时会出错

推荐设置为 RemoteSigned

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned  -Scope CurrentUser
```



## Help System

```powershell
Get-Help -Name Get-GetCommand
Get-Command
Get-Member
```



```powershell
Get-Service -Name Mysql | Get-Member
Get-Command -ParameterType ServiceController
Get-Service -Name MySQL | Select-Object -Property *
```



```powershell
Get-Service -Name MySQL | Get-Member -MemberType Method
(Get-Service -Name MySQL).Stop()

Get-Service | Where-Object Name -EQ mysql
```





## 指定重定向文件格式

> powershell 默认输出文件格式为utf16，
powershell 中 chcp 65001 无法改变输出重定向文件格式

```sh
echo "test text" | Out-File -Encoding utf8
```

## 服务管理

```powershell
Get-Service -Name my*
Get-Service -Name mysql
Get-Service -Name mysql | Select-Object -Property Name,Status,StartType
Start-Service -Name mysql
Stop-Service -Name mysql
Restart-Service -Name mysql

```

## 查看wifi密码

```powershell
netsh wlan show profiles name="Heming_5G" key=clear
```



## PowerShellGet

```powershell
Find-Module -Name MrToolKit | Install-Module
```



## Format

```powershell
Get-Service -Name w32time | Format-Table
Get-Service -Name w32time | Format-List
```



## Provider

修改环境变量，文件系统，注册表等的接口

```powershell
Get-PSProvider
Get-PSDrive
```



## WMI, CIM

WMI: Windows Management Instrumentations (deprecated)

CIM: Common Infomation Model (Can be used both windows and non-windows machines)

```powershell
Get-Command -Noun WMI*
Get-Command -Module CimCmdlets
```



```powershell
#(same result)
Get-CimInstance -Query 'Select * from win32_BIOS'
Get-CimInstance -ClassName Win32_BIOS
```



```powershell
Get-CimInstance -ClassName Win32_BIOS -Property SerialNumber |
>> Select-Object -ExpandProperty SerialNumber
```

-Propery 减少后台查询的信息

-ExpandProperty 返回一个String而不是一个Object



也可下面这样

```powershell
(Get-CimInstance -ClassName Win32_BIOS -Property SerialNumber).SerialNumber
```



扩展：可以查询远程设备的信息

## Remoting

```powershell
Get-Command -ParameterName ComputerName
```



```powershell
Enable-PSRemoting
```



## oh-my-posh

这篇博客有写好的脚本：

https://github.com/microexs/PowerAdmin/blob/master/PCInit.md

注册powershell repository

```sh
Get-PSRepository # 如果没有则进行下面的注册操作
Register-PSRepository -Default
Get-PSRepository
```

oh-my-posh:

https://github.com/JanDeDobbeleer/oh-my-posh

powerline(字体):

https://github.com/powerline/fonts

```powershell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser

# Start the default settings
Set-Prompt
# Alternatively set the desired theme:
Set-Theme Agnoster

if (!(Test-Path -Path $PROFILE )) { New-Item -Type File -Path $PROFILE -Force }
notepad $PROFILE
```



```powershell
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Paradox
```



删除

```powershell
Uninstall-Module posh-git
Uninstall-Module oh-my-posh
""> $PROFILE
```

## parameter

- Parameter
- name parameter
- Positional parameter
- Switch parameter

```sh
Param(
    [Parameter(Mandatory=$true, Position=2)][string]$name, # paramter, postional parameter
    [Parameter(Mandatory=$true, Position=1)][string]$greeting,
    [switch]$show, # switch parameter
    [Parameter(Mandatory=$true, ValuefromPipeline=$true)][string]$computers # get parameter from pipeline
)

```



## help info

get-help显示的帮助信息

```sh
<#
.SYNOPSIS
Get information from the help
.DESCRIPTION
Wirte description here.
.PARAMETER computers
parameer computers
.EXAMPLE
some example
#>

```



## exception handle

```sh
try {

} catch {

}
```



## Get-PSDrive

文件系统

注册表

证书

环境变量

## Moduel

```sh
$PSModulePath
get-module -ListAvailabe
get-module
Install-Module
```

## function

```sh
function Get-CompInfo {
<#
.SYNOPSIS
fadsf
.DESCRIPTION
fadsf
.EXAMPLE
#>
}
```



## write module

文件名后缀`compInfo.psm1`

放到$PSModulePath可直接导入

```sh
Import-Module compInfo
```

或者指定路径导入

```sh
Import-Module ./compInfo
```

