###  [<< back](./index.md)
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
# Git add  
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
# Git with branch 
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
# Git push 
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
