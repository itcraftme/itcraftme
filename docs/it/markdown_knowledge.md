###  [<< back home](./index.md)
# Content
Note of studying markdown in youtube  [video link](https://www.youtube.com/watch?v=bTVIMt3XllM)
  
## Cons to HTML
- Much easier to learn
- A lot of documentation for markdown language
- Github support MD file well  
  
## Apps that supports markdown
- Bear App
- Stardard Notes
- Joplin
  
## Syntax of markdown

### Format of text
- **Table of Conents**
- ~~Table of Contents~~
- **Table *of* Contents**
   
### List of content
1. content one
    1. content one of content one
    2. content two of content one
2. content two
3. content three
  
### List of content (could be only numbered one)
1. content one
    1. content one of content one
    1 content two of content one
1. content two
1. content three   
  
### Unordered list
- list one
- list two
- list three

### Code block with single line
- Command: `pip install graphene`
   
### Code block with language mark
```py
              import copy
              def count_sets(arr,total):
                  mem = {}
                  count =  rec(arr,total,len(arr)-1,mem)
                  return count

              def rec(arr,total,i,mem):
                  key = str(total)+":"+ str(i)
                  if key in mem:
                      return mem[key]
                  if total == 0:
                      return 1
                  elif total < 0:
                      return 0
                  elif i < 0:
                      return 0
                  elif total < arr[i]: 
                      mem[key] =  rec(arr,total,i-1,mem)
                  else:
                      mem[key] =  rec(arr,total-arr[i],i-1,mem) + rec(arr,total,i-1,mem)
                  return mem[key]

              if __name__ == "__main__":
                  # count = count_sets([6,10,2,4,6,8,10,33,73,77,88,99],83)
                  count = count_sets([6,2,2,4,6,8,11],10)
                  print(count)
   ```    
   
### Seperated line
   ---  
   
### Links for content
- [Google](www.google.com)

### Embed an image  
![one image](./images/Sample.png)

### Use HTML tags
some text </br> another line of text

### Checkbox
- [ ] my choice1
- [ ] My choice2

### Table

| Name | Age | Role
| :---- | :---- | :---- 
| Yan  |  22  |  QA
  
### Section cound be expanded
<details>
  <summary>Section header</summary>
  some information in detail here </br> Foot note & Footer (git hub does not support)  
</details>
