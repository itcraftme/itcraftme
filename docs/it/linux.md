###  [<< back](./index.md)
# Knowledge for linux

## A good link for linux studying 
- [knowledge of Linux](https://linux265.com/course/)
- [All knowledge](https://www.geeksforgeeks.org/)

# Linux command
## cat
- View the content of a file ```cat filename```
- View the content of multiple files ```cat filename1 filename2```
- View content of the file preceding with line number ```cat -n filename```
- Create a file and add content to a file  ```cat > newfilename```
- Copy content from one file to another file (can be more than two)```cat file1 file2 > mergedfilename```
- Suppress empty lines in output ```cat -s file1```
- Append the content of one file to the end of another file ```cat file1 >> file2```
- Display the content of one file in reverse order ```tac file1```
- Hight the end of line of each line ```cat -E file1```
- Display the content of ‘filename’ with non-printing characters visible, line endings highlighted, and tabs displayed as ‘^I’. ```cat -A (instead of -vET) file1```
- Open dashed file ```cat -- "-file1.txt"```
- Open a long file which can not fit into the command ```cat file1 | more```
- Open a file with whild card "*" ```cat *.txt``` (this will open all the txt files in targeted location)
- Append content to a file ```cat >> geeks.txt The newly added text.```
### Leetcode example
```cat words.txt | tr -s ' ' '\n' | sort | uniq -c | sort -r -n -k 1 |awk '{print $2, $1}'```
## awk 
### basic
- To print column 2,tab and column 3 ```awk '{print $2 "\t" $3}' file.txt```
- To print all the columns ```awk ' {print $0}' file.txt```
- To print lines that matches a certain pattern ```awk '/variable_to_be_matched/ {print $0}' file.txt```
- To print lines that matches 'a'````awk '/o/ {print $0}' file.txt```
- To print lines that matches 'e' ```awk '/e/ {print $0}' file.txt```
- To print column 3 and column 4 that matches 'a' ```awk '/a/ {print $3 "\t" $4}' file.txt```
- To print the count of the matches 'a' ```awk '/a/{++cnt} END {print "Count = ", cnt}' file.txt```
- To print column 3 and column 4 that matches 'a' and out put it to output.txt ```awk '/a/ {print $3 "\t" $4}' file.txt > Output.txt```
### advanced
#### awk build in variables
- FS  (Input field separator variable)
- OFS (Output Field Separator Variable) 
- RS  (Record Separator variable)
- ORS (Output Record Separator Variable)
- NR  (Number of Records Variable)
- NF  (Number of Fields in a record)
- FILENAME  (Name of the current input file)
- FNR (Number of Records relative to the current input file)
#### two ways for awk programing
- To program inside the command ```awk 'program' input-file1 input-file2 …```
- To put program in a file ```awk -f program-file input-file1 input-file2 …```
- To program without input file ```awk 'program'```
#### example
- If file.txt has the following content:

```
name age
alice 21
ryan 30
```

- Output the following:

```
name alice ryan
age 21 30
```

-Program

```
awk '
{
    for (i = 1; i <= NF; i++) {
        if (FNR == 1) {
            t[i] = $i;
        }else {
            t[i] = t[i] " " $i
        }
    }
}
END {
    for (i = 1; t[i] != ""; i++) {
        print t[i]
    }
}
' file.txt
```
