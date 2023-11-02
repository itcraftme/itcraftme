###  [<< back](./index.md)
# Powershell knowledge
## Run code in parallel:
  - Command: `measure-command {ForEach-Object -InputObject $list  -Process { sleep(3) }}`
## `break` & ForEach-Object
### Break in ForEach-Object
ForEach-Object is not a loop in powershell so following code with break will break the whole execution.
#### Code
```powershell
$list = 1,2,3
$list | ForEach-Object -process {
  write-host $_ 
  if($_ -eq 2){break}
}
Write-Host "Check how break works in powershell"
```    
#### Output
```
1
2
```
### Break in ForEach 
    The best way is to use ForEach instead of ForEach-Object even though there is some tricky way but I dont recommend.
#### Code
```powershell
$list = 1,2,3
ForEach($eachItem in $list){
 write-host $eachItem
 if($eachItem -eq 2){break}
}
Write-Host "Check how break works in powershell"
```
#### Output
```
1
2
Check how break works in powershell
```
## A tricky way to convert string to object
#### code
```powershell
$ht = ("{`""+"a=1;b=2".Replace(";","`",`"").Replace("=","`":`"")+"`"}") | ConvertFrom-Json
$ht.a
```
#### output
1

## Hash with MD5
#### Code

```powershell
(Get-FileHash $_.FullName -Algorithm MD5).Hash
```
#### Output
1BC0CE544888AD95BC9587EFE95159CE
#### Code

```powershell
$hasher=[System.Security.Cryptography.HashAlgorithm]::Create('MD5')
[String]$hasher.ComputeHash([System.Text.Encoding]::UTF8.GetBytes("testing"))
```
#### Output
174 43 31 202 81 89 73 229 213 79 178 43 142 217 85 117
## Start-Process with credential
```powershell
 $credentail = [System.Management.Automation.PSCredential]::new("username",(ConvertTo-SecureString -String "password" -AsPlainText -Force))
 start-process -FilePath powershell -ArgumentList "Stop-Process -Name MaxwellServiceMonitor -force;sleep(60)" -Credential $credentail
```
## Start-Process with space in path
```powershell
 $command = 'dir "c:\program files";sleep(30)'
 $bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
 $encodedCommand = [Convert]::ToBase64String($bytes)
 start-process -FilePath powershell -ArgumentList "-encodedCommand $encodedCommand"
```
## Get size of a folder
```powershell
((Get-ChildItem $path -Recurse | Measure-Object -Property Length -Sum -ErrorAction Stop).Sum / 1MB)
```
## Working with WMI
``` powershell
$csvFile =".\ComputerName.csv"
$outputFile = ".\ComputerStatus.csv"
$existed = Test-Path $outputFile
if ($existed)
{
    remove-item -Path $outputFile
}
Add-Content -Path $outputFile "Computer,IP,App1,App2,App3"

Function GetNetWorkConfig{
    param([string]$computer)
    $namespace = "ROOT\CIMV2"
    $classname = "Win32_NetworkAdapterConfiguration"
    $network = WMILib $computer $namespace $classname |  where-object {($_.Description -notlike "*Hyper-V*")}
    $network.IPAddress
}

Function GetAppVersion{
    param([string]$computer)
    $namespace = "ROOT\CIMV2"
    $classname = "Win32_Product"
    WMILib $computer $namespace $classname | where-object {($_.Name -like "*something*") -or ($_.Name -like "*something*") -or ($_.Name -like "*SQL Server*Database Engine Service*") -or ($_.Name -like "*something*")}
}

Function WMILib {
    param([string]$computer, [string]$namespace,[string]$classname)
    $pwd = ConvertTo-SecureString -String "password" -AsPlainText -Force
    $cred = [System.Management.Automation.PSCredential]::new("username",$pwd)
    Get-WmiObject -Class $classname -ComputerName $computer -Namespace $namespace -Credential $cred 
}

Function main{
    $query = Import-Csv -path $csvFile 
    $query.ComputerName | ForEach-Object -Process{
        if((Test-NetConnection -ComputerName $_).pingSucceeded)
        {
            Write-Host $_
            $IP = GetNetWorkConfig $_ 
            $Versions = GetAppVersion $_
            $Version = ($Versions | where-object {$_.Name -like "*somekeyword*"}).Version
            $SQLVersion = ($Versions | where-object {$_.Name -like "*somekeyword*"})[0].Name
            $WVersion = ($Versions | where-object {$_.Name -like "*somekeyword*"}).Name
            Add-Content -Path $outputFile ($_ + "," + $IP + "," + $Version + "," + $WVersion + "," + $SQLVersion)
       }
       else
       {
            Add-Content -Path $outputFile ($_ + "," + "Offline" + "," + "Null" + "," + "Null" + "," + "Null")
       }
    }
}

main
```
## Execute command in a remote machine
``` powershell
$DCOM = New-CimSessionOption -Protocol Dcom
$pwd = ConvertTo-SecureString -String "password" -AsPlainText -Force
$Cred = [pscredential]::new("username",$pwd)
$CimSession = New-CimSession -ComputerName 192.168.83.18 -SessionOption $DCOM -Credential $Cred
#Get-CimInstance -CimSession $CimSession -ClassName Win32_BIOS
$app = Invoke-CimMethod -CimSession $CimSession -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine = "cmd.exe /c C:\Temp\abc.bat" }
```
## Powershell with UI automation
### Root element for everything
```
$root = [Windows.Automation.AutomationElement]::RootElement
```
### Main assemblies with UI automation
```
Add-Type -Path ".\UIAutomationTypes.dll"
Add-Type -Path ".\UIAutomationClient.dll"
Add-Type -Path ".\WindowsBase.dll"
```
### To find controls
```
function FindWithClassnameandName($whichcontrol,$classname,$name)
{
    $targetcontrol = $null
    if($whichcontrol -eq $null)
    {
        "the start point of the control is null" | Out-Host
        return $targetcontrol
    }
    $con1 = New-Object Windows.Automation.PropertyCondition([Windows.Automation.AutomationElement]::ClassNameProperty, $classname)
    $con2 = New-Object Windows.Automation.PropertyCondition([Windows.Automation.AutomationElement]::NameProperty, $name)
    $target  =  New-Object Windows.Automation.AndCondition($con1,$con2)
    $targetcontrol = $whichcontrol.FindFirst([Windows.Automation.TreeScope]::Descendants, $target)
    $targetcontrol.Current | Out-Host
    $info = $whichcontrol.current
    if($targetcontrol -eq $null)
    {
        "Can not find the target control with $info classname : $classname and name : $name" | Out-Host
    }
    return $targetcontrol
}
function FindWithName($whichcontrol,$name)
{
    $targetcontrol = $null
    if($whichcontrol -eq $null)
    {
        "the start point of the control is null" | Out-Host
        return $targetcontrol
    }
    $con1 = New-Object Windows.Automation.PropertyCondition([Windows.Automation.AutomationElement]::NameProperty, $name)
    $targetcontrol = $whichcontrol.FindFirst([Windows.Automation.TreeScope]::Descendants, $con1)
    $targetcontrol.Current | Out-Host
    $info = $whichcontrol.current
    if($targetcontrol -eq $null)
    {
        "Can not find the target control with $info  name : $name" | Out-Host
    }
    return $targetcontrol
}
```
### To work with tree walker
```
    $treewalker = [Windows.Automation.TreeWalker]::RawViewWalker   //RawView, ContentView, ControlView
    $child = $treewalker.GetFirstChild($root)
    $child.current | Out-Host
    $sibling = $treewalker.GetNextSibling($child)
    $sibling.current | Out-Host

```
### To maximize a window
```
function MaximizeWindow($root,$name)
{
    sleep -s 2
    $window = FindWithName $root $name
    [System.Windows.Automation.WindowPattern]$windowpattern = $window.GetCurrentPattern([System.Windows.Automation.WindowPattern]::Pattern)
    $windowpattern.SetWindowVisualState([System.Windows.Automation.WindowVisualState]::Maximized)
}
```
### Tools for UI automation
- Precondition: Windows SDK installed
- inspect.exe
- accevent.exe
- UIAVerify
