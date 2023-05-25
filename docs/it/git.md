# Git init
## Basic command: `git init [--bare] [directory]`
### git init
- use current directory as a repository
### git init directory
- use [directory] as a repository
### git init --bare directory
- use [directory] as a empty repository
# Git commit
## Basic command: `git commit [file1] [file2] ... -m [message]`
### Examples
- Edit, remove and add and commit: 
```
$ edit hello.c
$ git rm goodbye.c
$ git add hello.c
$ git commit
```
- Edit remove and commit -a (do the add and remove for you)
```
$ edit hello.c
$ rm goodbye.c
$ git commit -a
```
- Make changes but only commit certain change
```
$ edit hello.c hello.h
$ git add hello.c hello.h
$ edit Makefile
$ git commit Makefile
```
- Commit information in the environment variables
```
GIT_AUTHOR_NAME
GIT_AUTHOR_EMAIL
GIT_AUTHOR_DATE
GIT_COMMITTER_NAME
GIT_COMMITTER_EMAIL
GIT_COMMITTER_DATE
```
