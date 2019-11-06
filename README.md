# Git Commands

### Sync upstream with local master
```
git checkout master
git fetch MTT // Fetch is used to pull from a remote repo
git merge MTT/master // merge upstream with local fork
git push origin master
```

### Merge master into working branch
```
git checkout <branch_name>
git merge master
git commit -m "Merging master into working branch <branch name>"
git push origin <branch name>
```

### List remote repos
```
anthony@ghost:~$ git remote -v
MTT	git@github.com:upstream-name/repo-name.git (fetch)
MTT	git@github.com:upstream-name/repo-name.git (push)
origin	git@github.com:amboxer21/repo-name.git (fetch)
origin	git@github.com:amboxer21/repo-name.git (push)
anthony@ghost:~$
```

### List remote branches
```
anthony@ghost:~$ git branch -r
  MTT/Add-CodeCov-Token
  MTT/baresip
  MTT/bluetooth-ios-fix
  ...
  ...
  MTT/recordinglogging
  MTT/revert-1077-hop_logging
  MTT/revert-1106-introduce-unions-as-optimizati
  ...
  ...
  MTT/softbuttons
  MTT/stable
  origin/AutomatedCallingGroupsLeadingZeroFix
  origin/CDRDownloadAdditionalFields
  origin/E911WorkGroups
  origin/E911WorkGroups2
  origin/HEAD -> origin/master
  ...
  ...
anthony@ghost:~$
```

### How to checkout a remote branch thats not on our local machine.
```
anthony@ghost:~$ git fetch origin
anthony@ghost:~$ git checkout --track <branch name> origin/<branch name>
```

### Releasing code into production for hosted
```
Step 01: Merge your PR(s) in/at github.com
Step 02: Open new PR merging upstream-name/repo-name master into upstream-name/repo-name stable
Step 03: Merge that pull request to merge your changes into the stable branch.
Step 04: Run bundle exec cap production deploy from your local machine where your code was written from
Step 05: Once that is done go to the hpbxgui folder on the app server.
Step 06: Set rails env to production via export RAILS_ENV=production
Step 07: Run migrations if need be 
Step 08: Run any tasks that are needed as well
Step 09: Perform a touch tmp/restart.txt
Step 10: Test release
Step 11: Restart CTI and AMI via /etc/init.d 
example:
[aguevara@app-server hpbxgui]$ sudo /etc/init.d/cti restart
[aguevara@app-server hpbxgui]$ sudo /etc/init.d/ami_fw_proxy restart
```

### Creating a new upstream
> List the current configured remote repository for your fork
```
anthony@ghost:~$ git remote -v
origin  git@github.com:amboxer21/hosted.git (fetch)
origin  git@github.com:amboxer21/hosted.git (push)
anthony@ghost:~$
```

> Specify a new remote upstream repository that will be synced with the fork
```
anthony@ghost:~$ git remote add MTT git@github.com:upstream-name/repo-name.git
```

> Verify the new upstream repository you've specified for your fork
```
anthony@ghost:~$ git remote -v
MTT git@github.com:upstream-name/repo-name.git (fetch)
MTT git@github.com:upstream-name/repo-name.git (push)
origin  git@github.com:amboxer21/repo-name.git (fetch)
origin  git@github.com:amboxer21/repo-name.git (push)
anthony@ghost:~$
```

Above instructions can be found [HERE](https://help.github.com/articles/configuring-a-remote-for-a-fork/)


### Patching production during work hours(outside of maintenance window)
```
Step 01) Merge your PR in/at github.com
Step 02) Add .patch to the end of the URL
example:
github.com/upstream-name/repo-name/pull/2485.patch
Step 03) Copy the url that now has a hash appended to it.
Step 04) SSH into the app server
Step 04) gain root access
Step 06) change your dir to /var/asterisk/hosted/current
Step 07) Use wget to pull the patch file in to the app server
example:
[root@app-server current]# wget https://patch-diff.githubusercontent.com/raw/upstream-name/repo-name/pull/2484.patch?token=AAQAYIT7XZ52BNNU3RJYSUK5XSPIW -O 2484.patch --no-check-certificate
Step 08) Run -> git apply --check 2484.patch
Step 09) If everything checks out then run the git apply command with out the --check option
Step 10) Run a touch tmp/restart.txt (still on the app server)
```
