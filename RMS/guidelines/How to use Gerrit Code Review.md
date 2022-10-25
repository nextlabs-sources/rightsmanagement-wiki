** Deprecated **  
Note: Gerrit is no longer used for code reviews and this article is no longer relevant. Code reviews are managed on BitBucket. This article should be archived.

## Why gerrit

Gerrit is a web-based code reviewing tool developed by Google. I briefly list a few advantages compare to bitbucket Pull Request.

1. Rebase only/fast-forward only is imported (Gerrit can enforce it but pull request can't)
2. Branch merge can be disabled in gerrit
3. Atomic/related changes all in one commit. Squash related updates and push as single commit
4. Avoid repeating review already reviewed code between multiple patch set.
5. Can be integrated with code analysis tools (Findbugs/checksytle/etc.)
6. Can be easily integrated with JIRA/Jenkens (Jenkins can deny the checkin)
7. Gerrit allows developer push draft reviews, this is better than push a temporary branch direct to git repo.
8. Gerrit allows developer track all temporary patch set.
9. Provide better permission model.
10. Much better code review UI, make code review more efficient.


## Account setup for gerrit
* generate certificate/private key to login with ssh if have not done it before.

		$ ssh-keygen

- Open browser and navigate to: https://rmtest.nextlabs.solutions:8443
- Login to gerrit with your credential
* click your name link at the right corner, select **Settings** menu item
	* Select **Contact Information** menu, make sure you have a correct **Preferred Email**, if you click on **Register email** button. You will receive an email, and click the confirm link in your email to finish setup email.
	* Select **SSH Public Keys**, copy the text from your ~/.ssh/id_rsa.pub and paste in text area, and press **Add** button.
* Select **Group** menu, to make you have **Developer** group permission.

## Check out code
* Select **Projects** -> List, make you you can see **rmsng** project
* add following int ~/.gitconfig file:

		[user]
		    email = your email address that matches in "Preferred Email"
* Click on **rmsng** -> **clone** -> **SSH**, you will see command to clone the project, it looks as following:

		$ git clone ssh://your_account@rmtest.nextlabs.solutions:29418/rmsng

* Workaround OpenSSL issues if you saw error message: Unable to negotiate with 54.208.120.56: no matching key exchange method found. Their offer: diffie-hellman-group1-sha1
fatal: Could not read from remote repository. 

		$ vi ~/.ssh/config
		# Add following lines
		Host rmtest.nextlabs.solutions
		        KexAlgorithms +diffie-hellman-group1-sha1
		
## Working with git
You can use git command as normal when working on local repository

## Push changes
You can't push changes directly back to remote git repo. Instead use following command to push changes for review:
```
$ git push ssh://your_account_name@rmtest.nextlabs.solutions:29418/rmsng YOUR_LOCAL_BRANCH_NAME:refs/for/REMOTE_BRANCH_NAME
```

## Code review
* After you push changes to gerrit, visit https://rmtest.nextlabs.solutions:8443, find your changes, add other engineer for code view.
* You can submit your changes after your changes get approved.
* sync your code back from gerrit to your local

		$ git pull --rebase
		# or you created alias already in .gitconfig
		$ git sync
	
## Update patch set
You may want to make more changes or after code review, you need address issues pointed in code review. After you making changes, **DONOT** push a separate commit to gerrit. Instead, you need to patch to original gerrit review: 
```
git push ssh://your_account_name@rmtest.nextlabs.solutions:29418/rmsng YOUR_LOCAL_BRANCH_NAME:refs/changes/CHANGE_ID
```
The CHANGE_ID is the last part of URL for the changes on gerrit. For example: https://rmtest.nextlabs.solutions:8443/#/c/1/
The CHANGE_ID IS "1" in above example.

After you push patch set, you will find a new patch set under original changes.

## Download patch set from gerrit
It's safe to delete your local branch after pushed to gerrit, you can sync it back at anytime. You can also download patch set make by other developer. Just click **copy** icon in gerrit and paste the command in your terminal.

## Working on multiple changes at the same time
You may want to work on other changes while the gerrit review is pending. Create a new local branch for the new code changes. Don't keep working on the same commit any more.
```
$git checkout -b temp --track origin/master # create a local branch named "temp"
$git branch -D master  # delete old "master" branch
$git branch -m master  # rename "temp" to "master
```

## Some rules for using gerrit
* Please checkin smaller changes in each commit, this makes review easier.
* Each commit should be doing one thing, avoid making multiple things in the same commit.
* Avoid change files across two projects in the same commit.
* Please add meaningful title for each commit, describe what this commit is for
* Squash your commit only when the changes are related to original commit.


## Helper functions for push commit to gerrit
You can add following functions in your .profile (or .bash_profile)
```
function gitp {
    remote=`git remote -v | awk '/origin.*\(push\)/ { print $2 }'`
    git push $remote HEAD:refs/for/$1
}

function gitn {
    remote=`git remote -v | awk '/origin.*\(push\)/ { print $2 }'`
    git push $remote HEAD:refs/changes/$1
}
```

If you want to push new commit to gerrit, you can simply use:
```
$ gitp REMOTE_BRANCH_NAME
```

instead of:

```
$ git push ssh://your_account_name@rmtest.nextlabs.solutions:29418/rmsng YOUR_LOCAL_BRANCH_NAME:refs/for/REMOTE_BRANCH_NAME
```

You can now use following command to update the patch set on gerrit:
```
$ gitn CHANGE_ID
```

instead of:
```
git push ssh://your_account_name@rmtest.nextlabs.solutions:29418/rmsng YOUR_LOCAL_BRANCH_NAME:refs/changes/CHANGE_ID
```