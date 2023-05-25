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
