
# Git and Github
---------------

## Local Git repository
```shell
$git init
$git add
```

There is a working directory, repository and staging area.

+ To show the difference between staging and working directory:
$git diff

+ To show the changes between HEAD (latest commit on current branch) and staging directory:
$git diff --staged

+ To show deltas between HEAD and working dir:
$git diff HEAD

+ To commit changes to the repo with a message:
$git commit -m "xxxx"

+ To skip the staging area (only works if the file has already been added with an initial -a):
$git commit -am "Commit skipping the staging area"

## Checkout
Before committing:

+Re-checkout all tracked files overwriting local changes:
$git checkout .

+Re-checkout just one specific file:
$git checkout -- <file>

+If the changes were already commited, after committing:
Revert the most recent commit:
$git revert HEAD


## Remote repos and Github

Link remote repo with your local repo:
git remote add origin <alias> <remote_url>

If I actually clone the repo from a remote source:
the alias is going to be "origin"
$git remote add origin https://github.com/try-git/try_git.git

To push changes to my branch out to the remote repository:
$git push <alias> <branch_name>
ex:
$git push origin master

To create a remote git repository from an existing local one:
Create the remote repo, then:
```shell
$git remote add origin URL
$git push -u origin master
``

If there is no common history. Merges histories of two projects that started their lives independently:
```shell
$git pull origin master --allow-unrelated-histories
```

## Git references

Train myself online:
https://try.github.io/
http://gitref.org/




