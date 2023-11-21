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
```powershell
$root = [Windows.Automation.AutomationElement]::RootElement
```
### Main assemblies with UI automation
```powershell
Add-Type -Path ".\UIAutomationTypes.dll"
Add-Type -Path ".\UIAutomationClient.dll"
Add-Type -Path ".\WindowsBase.dll"
```
### To find controls
```powershell
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
```powershell
    $treewalker = [Windows.Automation.TreeWalker]::RawViewWalker   //RawView, ContentView, ControlView
    $child = $treewalker.GetFirstChild($root)
    $child.current | Out-Host
    $sibling = $treewalker.GetNextSibling($child)
    $sibling.current | Out-Host

```
### To maximize a window
```powershell
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
# Powershell with windows keys
### Lib to invoke

```powershell
Param (
    [Parameter(position=0, mandatory=$true, parametersetname='key')]
    [char]$Key,
    [Parameter(position=0, mandatory=$true, parametersetname='scancode')]
    [int]$ScanCode
)

$code = @"
using System;
using System.Collections.Generic;
using System.Runtime.InteropServices;

public static class KBEmulator {	
    public enum InputType : uint {
	    INPUT_MOUSE = 0,
	    INPUT_KEYBOARD = 1,
	    INPUT_HARDWARE = 3
    }

    [Flags]
    internal enum KEYEVENTF : uint
    {
	    KEYDOWN = 0x0,
        EXTENDEDKEY = 0x0001,
        KEYUP = 0x0002,
        SCANCODE = 0x0008,
        UNICODE = 0x0004
    }

    [Flags]
    internal enum MOUSEEVENTF : uint
    {
        ABSOLUTE = 0x8000,
        HWHEEL = 0x01000,
        MOVE = 0x0001,
        MOVE_NOCOALESCE = 0x2000,
        LEFTDOWN = 0x0002,
        LEFTUP = 0x0004,
        RIGHTDOWN = 0x0008,
        RIGHTUP = 0x0010,
        MIDDLEDOWN = 0x0020,
        MIDDLEUP = 0x0040,
        VIRTUALDESK = 0x4000,
        WHEEL = 0x0800,
        XDOWN = 0x0080,
        XUP = 0x0100
    }

    // Master Input structure
    [StructLayout(LayoutKind.Sequential)]
    public struct lpInput {
	    internal InputType type;
	    internal InputUnion Data;
	    internal static int Size { get { return Marshal.SizeOf(typeof(lpInput)); } }			
    }

    // Union structure
    [StructLayout(LayoutKind.Explicit)]
    internal struct InputUnion {
	    [FieldOffset(0)]
	    internal MOUSEINPUT mi;
	    [FieldOffset(0)]
	    internal KEYBDINPUT ki;
	    [FieldOffset(0)]
	    internal HARDWAREINPUT hi;
    }

    // Input Types
    [StructLayout(LayoutKind.Sequential)]
    internal struct MOUSEINPUT
    {
        internal int dx;
        internal int dy;
        internal int mouseData;
        internal MOUSEEVENTF dwFlags;
        internal uint time;
        internal UIntPtr dwExtraInfo;
    }

    [StructLayout(LayoutKind.Sequential)]
    internal struct KEYBDINPUT
    {
        internal short wVk;
        internal short wScan;
        internal KEYEVENTF dwFlags;
        internal int time;
        internal UIntPtr dwExtraInfo;
    }

    [StructLayout(LayoutKind.Sequential)]
    internal struct HARDWAREINPUT
    {
        internal int uMsg;
        internal short wParamL;
        internal short wParamH;
    }

    private class unmanaged {
	    [DllImport("user32.dll", SetLastError = true)]
	    internal static extern uint SendInput (
		    uint cInputs, 
		    [MarshalAs(UnmanagedType.LPArray)]
		    lpInput[] inputs,
		    int cbSize
	    );
	
	    [DllImport("user32.dll", CharSet = CharSet.Unicode, SetLastError = true)]
	    public static extern short VkKeyScan(char ch);
    }

    internal static short VkKeyScan(char ch) {
	    return unmanaged.VkKeyScan(ch);
    }

    internal static uint SendInput(uint cInputs, lpInput[] inputs, int cbSize) {
	    return unmanaged.SendInput(cInputs, inputs, cbSize);
    }

    public static void SendScanCode(short scanCode) {
        lpInput[] KeyInputs = new lpInput[1];
	    lpInput KeyInput = new lpInput();
	    // Generic Keyboard Event
	    KeyInput.type = InputType.INPUT_KEYBOARD;
	    KeyInput.Data.ki.wScan = 0;
	    KeyInput.Data.ki.time = 0;
	    KeyInput.Data.ki.dwExtraInfo = UIntPtr.Zero;
	
	
	    // Push the correct key
	    KeyInput.Data.ki.wVk = scanCode;
	    KeyInput.Data.ki.dwFlags = KEYEVENTF.KEYDOWN;
	    KeyInputs[0] = KeyInput;
	    SendInput(1, KeyInputs, lpInput.Size);
	
	    // Release the key
	    KeyInput.Data.ki.dwFlags = KEYEVENTF.KEYUP;
	    KeyInputs[0] = KeyInput;
	    SendInput(1, KeyInputs, lpInput.Size);
	
	    return;
    }

    public static void SendKeyboard(char ch) {
	    lpInput[] KeyInputs = new lpInput[1];
	    lpInput KeyInput = new lpInput();
	    // Generic Keyboard Event
	    KeyInput.type = InputType.INPUT_KEYBOARD;
	    KeyInput.Data.ki.wScan = 0;
	    KeyInput.Data.ki.time = 0;
	    KeyInput.Data.ki.dwExtraInfo = UIntPtr.Zero;
	
	
	    // Push the correct key
	    KeyInput.Data.ki.wVk = VkKeyScan(ch);
	    KeyInput.Data.ki.dwFlags = KEYEVENTF.KEYDOWN;
	    KeyInputs[0] = KeyInput;
	    SendInput(1, KeyInputs, lpInput.Size);
	
	    // Release the key
	    KeyInput.Data.ki.dwFlags = KEYEVENTF.KEYUP;
	    KeyInputs[0] = KeyInput;
	    SendInput(1, KeyInputs, lpInput.Size);
	
	    return;
    }
}
"@

if(([System.AppDomain]::CurrentDomain.GetAssemblies() | ?{$_ -match "KBEmulator"}) -eq $null) {
    Add-Type -TypeDefinition $code
}

switch($PSCmdlet.ParameterSetName){
    'key' { [KBEmulator]::SendKeyboard($Key) }
    'scancode' { [KBEmulator]::SendScanCode($ScanCode) }
}
```

### sample (open calculator)

```powershell
.\KBEmulator.ps1 -ScanCode '0x5B'
Start-Sleep 1
.\KBEmulator.ps1 -key 'c'
Start-Sleep 1
.\KBEmulator.ps1 -key 'a'
Start-Sleep 1
.\KBEmulator.ps1 -key 'l'
Start-Sleep 1
.\KBEmulator.ps1 -key 'c'
Start-Sleep 1
.\KBEmulator.ps1 -ScanCode '0x0D'
```

### keycode reference
[link](https://boostrobotics.eu/windows-key-codes/)

# Identify dot net framework version 
```powershell
$release = Get-ItemPropertyValue -LiteralPath 'HKLM:SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full' -Name Release
switch ($release) {
    { $_ -ge 533320 } { $version = '4.8.1 or later'; break }
    { $_ -ge 528040 } { $version = '4.8'; break }
    { $_ -ge 461808 } { $version = '4.7.2'; break }
    { $_ -ge 461308 } { $version = '4.7.1'; break }
    { $_ -ge 460798 } { $version = '4.7'; break }
    { $_ -ge 394802 } { $version = '4.6.2'; break }
    { $_ -ge 394254 } { $version = '4.6.1'; break }
    { $_ -ge 393295 } { $version = '4.6'; break }
    { $_ -ge 379893 } { $version = '4.5.2'; break }
    { $_ -ge 378675 } { $version = '4.5.1'; break }
    { $_ -ge 378389 } { $version = '4.5'; break }
    default { $version = $null; break }
}

if ($version) {
    Write-Host -Object ".NET Framework Version: $version"
} else {
    Write-Host -Object '.NET Framework Version 4.5 or later is not detected.'
```

# Example of turning on bit locker
```powershell
param(
[Parameter(Mandatory = $True)]
[string] $toEnableTPM
) 
Add-Type -Path ".\UIAutomationTypes.dll"
Add-Type -Path ".\UIAutomationClient.dll"
Add-Type -Path ".\WindowsBase.dll"
$retriesLong = 60
$retriesShort = 10
$interval = 500
function EnableTPM()
{
    "Enabling TPM" | out-host
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\FVE" -Name "UseAdvancedStartup" -Value 1
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\FVE" -Name "EnableBDEWithNoTPM" -Value 1
}
function FindWithClassnameandName($whichcontrol,$classname,$name,$isMandatory,$intLongOrShort)
{
    switch ($intLongOrShort){
        0{$retries = $retriesShort}
        1{$retries = $retriesLong}
    }
    $targetcontrol = $null
    if($whichcontrol -eq $null)
    {
        "the start point of the control is null" | Out-Host
        return $targetcontrol
    }
    $con1 = New-Object Windows.Automation.PropertyCondition([Windows.Automation.AutomationElement]::ClassNameProperty, $classname)
    $con2 = New-Object Windows.Automation.PropertyCondition([Windows.Automation.AutomationElement]::NameProperty, $name)
    $target  =  New-Object Windows.Automation.AndCondition($con1,$con2)
    for ($i=0;$i -lt $retries;$i++)
    {
        sleep -Milliseconds $interval
        $targetcontrol = $whichcontrol.FindFirst([Windows.Automation.TreeScope]::Descendants, $target)
        if($targetcontrol -ne $null) {break}
        if(($i -eq ($retries-1)) -and $isMandatory) {exit 1}
        "Tryig to find control with $name and $classname with $i times" | out-host
    }
    $targetcontrol.Current | Out-Host
    $info = $whichcontrol.current
    if($targetcontrol -eq $null)
    {
        "Can not find the target control with $info classname : $classname and name : $name" | Out-Host
    }
    return $targetcontrol
}
function FindWithName($whichcontrol,$name,$isMandatory,$intLongOrShort)
{
    switch ($intLongOrShort){
        0{$retries = $retriesShort}
        1{$retries = $retriesLong}
    }
    $targetcontrol = $null
    if($whichcontrol -eq $null)
    {
        "the start point of the control is null" | Out-Host
        return $targetcontrol
    }
    $con1 = New-Object Windows.Automation.PropertyCondition([Windows.Automation.AutomationElement]::NameProperty, $name)
    for ($i = 0;$i -lt $retries;$i++)
    {
        sleep -Milliseconds $interval
        $targetcontrol = $whichcontrol.FindFirst([Windows.Automation.TreeScope]::Descendants, $con1)
        if($targetcontrol -ne $null) {break}
        if(($i -eq ($retries-1)) -and $isMandatory) {exit 1}
        "Tryig to find control with $name with $i times" | out-host
    }
    $targetcontrol.Current | Out-Host
    $info = $whichcontrol.current
    if($targetcontrol -eq $null)
    {
        "Can not find the target control with $info  name : $name" | Out-Host
    }
    return $targetcontrol
}
function FindChildWithName($whichcontrol,$name,$isMandatory,$intLongOrShort)
{
    switch ($intLongOrShort){
        0{$retries = $retriesShort}
        1{$retries = $retriesLong}
    }
    $targetcontrol = $null
    if($whichcontrol -eq $null)
    {
        "the start point of the control is null" | Out-Host
        return $targetcontrol
    }
    $con1 = New-Object Windows.Automation.PropertyCondition([Windows.Automation.AutomationElement]::NameProperty, $name)
    for ($i = 0;$i -lt $retries;$i++)
    {
        sleep -Milliseconds $interval
        $targetcontrol = $whichcontrol.FindFirst([Windows.Automation.TreeScope]::Children, $con1)
        if($targetcontrol -ne $null) {break}
        if(($i -eq ($retries-1)) -and $isMandatory) {exit 1}
        "Tryig to find control with $name with $i times" | out-host
    }
    $targetcontrol.Current | Out-Host
    $info = $whichcontrol.current
    if($targetcontrol -eq $null)
    {
        "Can not find the target control with $info  name : $name" | Out-Host
    }
    return $targetcontrol
}
function FindWithId($whichcontrol,$Id,$isMandatory,$intLongOrShort)
{
    switch ($intLongOrShort){
        0{$retries = $retriesShort}
        1{$retries = $retriesLong}
    }
    $targetcontrol = $null
    if($whichcontrol -eq $null)
    {
        "the start point of the control is null" | Out-Host
        return $targetcontrol
    }
    $con1 = New-Object Windows.Automation.PropertyCondition([Windows.Automation.AutomationElement]::AutomationIdProperty, $Id)
    for ($i=0;$i -lt $retries;$i++)
    {
        sleep -Milliseconds $interval
        $targetcontrol = $whichcontrol.FindFirst([Windows.Automation.TreeScope]::Descendants, $con1)
        if($targetcontrol -ne $null) {break}
        if(($i -eq ($retries-1)) -and $isMandatory) {exit 1}
        "Tryig to find control with $Id with $i times" | out-host
    }
    $targetcontrol.Current | Out-Host
    $info = $whichcontrol.current
    if($targetcontrol -eq $null)
    {
        "Can not find the target control with $info  AutomationId : $Id" | Out-Host
    }
    return $targetcontrol
}
function InvokeButton($root,$classname,$name,$isMandatory,$intLongOrShort)
{
    $target = FindWithClassnameandName $root $classname  $name $isMandatory $intLongOrShort
    $target.GetCurrentPattern([Windows.Automation.InvokePattern]::Pattern).Invoke()
}
function MaximizeWindow($root,$name,$isMandatory,$intLongOrShort)
{
    $window = FindChildWithName $root $name $isMandatory $intLongOrShort
    [System.Windows.Automation.WindowPattern]$windowpattern = $window.GetCurrentPattern([System.Windows.Automation.WindowPattern]::Pattern)
    $windowpattern.SetWindowVisualState([System.Windows.Automation.WindowVisualState]::Maximized)
}
function main()
{
    if([System.Convert]::ToBoolean($toEnableTPM)){EnableTPM}
    
    #start a window
    $gp = [Diagnostics.Process]::Start('control.exe')

    #get root 
    $root = [Windows.Automation.AutomationElement]::RootElement

    #maximize a window from root
    MaximizeWindow $root "Control Panel" $true 0

    #click on system and security
    InvokeButton $root "ControlPanelLink"  "System and Security" $true 0

    #click on bitlocker drive encryption
    InvokeButton $root "ControlPanelLink"  "BitLocker Drive Encryption" $true 0

    #click on Turn on BitLocker
    InvokeButton $root "Button"  "Turn on BitLocker" $true 0

    #click on next button
    InvokeButton $root "CCPushButton"  "Next" $false 1

    #click on next button
    InvokeButton $root "CCPushButton"  "Next" $false 1

    #click on Insert a USB flash drive
    InvokeButton $root "CCCommandLink"  "Insert a USB flash drive" $false 0

    #click on save
    InvokeButton $root "CCPushButton"  "Save" $false 0

    #click on next button
    InvokeButton $root "CCPushButton"  "Next" $false 0


    #click on Save to a USB flash drive
    InvokeButton $root "CCCommandLink"  "Save to a USB flash drive" $true 0

    #click on save
    InvokeButton $root "CCPushButton"  "Save" $true 0

    #click on two next button
    InvokeButton $root "CCPushButton"  "Next" $true 0

    #click on two next button
    InvokeButton $root "CCPushButton"  "Next" $true 0
    
    #click on continue 
    InvokeButton $root "CCPushButton"  "Continue" $false 0

    #click on Start encrypting 
    InvokeButton $root "CCPushButton"  "Start encrypting" $false 0

}
main
```
