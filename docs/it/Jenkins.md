###  [<< back](./index.md)
# Groovy syntax: [syntax](https://groovy-lang.org/syntax.html)
# Groovy with jenkins file: [Jenkins with groovy](https://www.eficode.com/blog/jenkins-groovy-tutorial)
# Get console output
```
$URL =  ($env:BUILD_URL+"consoleText")
write-host $URL
$content = Invoke-webrequest $URL
```
