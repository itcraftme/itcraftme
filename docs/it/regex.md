###  [<< back](./index.md)
## Content

  This part contains the knowledge of regular expressions and [here](https://www.youtube.com/watch?v=sa-TUpSx1JA) is the video I followed in Youtube
  
## Basic knowledge

| Character | Meaning 
| :----  | :---- 
| .  |  Any character except new line    
|[]  |  Matches characters in brackets  
| () |    Group  
|[^ ] |   Matches characters not in bracket  
|  $  |   End of a string  
|  \| |   Or
|  ?  |   Zero or one
|  *  |   Zero or more
|  +  |   One or More
| \d  |    Digit (0-9)
| \D   |   Not digit (0-9)
| \w   |   Word Character (a-z,A-Z,0-9,_ )
| \W  |   Not word character  
| \s  |   Whitespace (Space,tab,newline)  
| \S  |   Not Whitespace (Space,tab,newline)   
| \b  |   Word boundary 
| \B  |   Not word boundary 
| ^  |   Beginning of a string 
| {3}  |   Exact number
| {3,4}  | Range of numbers (Minimum, Maxinum)
| \ |  Escape character

## Regex with powershell
- To get time from a string and convert it to DateTime
### Code
```
$str = "Host.Service_2023-06-07_05-39-24.log"
$bMatch = $str -match "Host\.Service_(?<time>.*)\.log"
if($bMatch)
{
    $hosttime = [datetime]::parseexact($Matches.time, 'yyyy-MM-dd_HH-mm-ss', $null)
    Write-Host "Host Time:$hosttime"  
}
```
### Output
```
Host Time:06/07/2023 05:39:24
```
