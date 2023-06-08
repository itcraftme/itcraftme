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

### Hash with MD5
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
