###  [<< back](./index.md)
# git init
## Basic command: `git init [--bare] [directory]`
### git init
- use current directory as a repository
  
### git init directory
- use [directory] as a repository
  
### git init --bare directory
- use [directory] as a empty repository

# git config

```
git config --list
git config --global user.name [username] //set user name
git config --global user.email [email] //set email
git config --system --unset credential.helper //unset credential.helper so that it will pop up prompt to ask for password
```

# git commit
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

# git add  
Ask for help: `git add --help`
## Basic command:
```
git add [--verbose | -v] [--dry-run | -n] [--force | -f] [--interactive | -i] [--patch | -p]
	  [--edit | -e] [--[no-]all | --[no-]ignore-removal | [--update | -u]]
	  [--intent-to-add | -N] [--refresh] [--ignore-errors] [--ignore-missing] [--renormalize]
	  [--chmod=(+|-)x] [--] [<pathspec>…​]
``` 
## Example
- Add all the files to the context
  ```
  git add .
  ```
- Add two files to the context 
  ```
  git add hello.c hello.h
  ```
- Add all the changes to the context
  ```
  git add --all
  ```
  
# Git check out branch 
Ask for help: `git checkout --help`
## Basic command
```
git checkout [-q] [-f] [-m] [<branch>]
git checkout [-q] [-f] [-m] --detach [<branch>]
git checkout [-q] [-f] [-m] [--detach] <commit>
git checkout [-q] [-f] [-m] [[-b|-B|--orphan] <new_branch>] [<start_point>]
git checkout [-f|--ours|--theirs|-m|--conflict=<style>] [<tree-ish>] [--] <paths>…​
git checkout [<tree-ish>] [--] <pathspec>…​
git checkout (-p|--patch) [<tree-ish>] [--] [<paths>…​]
```
## Example
- Check out one branch
```
git checkout <branch>
```

# git push 
Ask for help: `git push --help`
## Basic command

```
git push [--all | --mirror | --tags] [--follow-tags] [--atomic] [-n | --dry-run] [--receive-pack=<git-receive-pack>]
	   [--repo=<repository>] [-f | --force] [-d | --delete] [--prune] [-v | --verbose]
	   [-u | --set-upstream] [--push-option=<string>]
	   [--[no-]signed|--signed=(true|false|if-asked)]
	   [--force-with-lease[=<refname>[:<expect>]]]
	   [--no-verify] [<repository> [<refspec>…​]]
```
## Example

```
git push origin <branch>
```

# git working with branch

```
- git branch  //list all the branches in local working director
- git checkout <branch> //working on this branch
- git branch -d <branch>  //delete local branch
- git push origin -d <branch>   //delete remote branch
```

# git merge

[Link](https://blog.csdn.net/All_In_gzx_cc/article/details/125482617)

# git create branch

```
git checkout -b <branchname> <origin/branchname>
```
# push local branch to remote

```
git checkout -b <new branch name>
git push -u origin <new branch name>
```

# git update

```
git update-git-for-windows
```

# git restore

```
git restore [FileName] //Revert changes in current branch
```

# git pull

```
git pull <remote> <branch>
```
