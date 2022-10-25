# Setup Git Development Environment  

git UI tools has many limitations. We recommend use git command line directly. It's much easier when solving git issues with command line. 

## configuration

### .gitconfig
You can config your git behavior for current login user by adding .gitconfig file under user's home folder.

```
$ vi ~/.gitcofig
```

Here is a typical .gitconfig file:
```
[user]
        name = Your name
        email = your_email@nextlabs.com
[core]
        excludesfile = ~/.gitignore
        autocrlf = false
        eol = lf
[alias]
        sync = pull --rebase
        st = status
        br = branch
        co = checkout
        ls = ls-files
        lg = log -p
        lol = log --graph --decorate --pretty=oneline --abbrev-commit
        lola = log --graph --decorate --pretty=oneline --abbrev-commit --all
```

### file mode on Windows
Windows doesn't support file mode, execute following command let git ignore file mode change:
```
$ git config --global core.filemode false
```

### .gitignore
Sometimes you want to prevent local files to be committed to repo. You can add a **.gitignore** file. There are two cases:

- If you want to keep a local file in project folder for your own use, and other user don't need it, you can create **.gitignore** file under your users's home folder (this file will not be checked in).

- If a file is required by project in local file system, but you don't want to be checkin in repo, you should create a **.gitignore** file in that folder and add filename in .gitignore file. And commit this .gitignore file into repo.

### rebase

When you pull changes from remote repo, be sure always use --rebase option. If you already added `sync = pull --rebase` alias int `.gitconfig`, you can simply use `git sync` to pull changes from remote git repo.

### Squash your local changes
You may want to squash multiple local commits into one and push to gerrit. You can use following command:
```
$ git rebase -i HEAD~2
```

The above command would initiate an interactive rebase beginning _two_ commits prior to the HEAD of the current branch.  Should you need to squash/remove/amend more commits than this, replace `2` with however many commits you wish to go back from the HEAD.