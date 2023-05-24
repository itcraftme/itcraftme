# Powershell knowledge
## Run code in parallel:
  - Command: `measure-command {ForEach-Object -InputObject $list  -Process { sleep(3) }}`
