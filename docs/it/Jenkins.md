###  [<< back](./index.md)
# Get console output
```
$URL =  ($env:BUILD_URL+"consoleText")
write-host $URL
$content = Invoke-webrequest $URL
```
