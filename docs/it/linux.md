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
