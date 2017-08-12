---
layout: post
title: git yourcommand
subtitle: How to write your own git commands?
date: 2014-04-05T09:37:00.000Z
author: Shobhit Puri
header-img: img/posts/git-refresh/git-your-command-bg.jpeg
comments: true
tags:
  - CodeMonkey
published: true
---

## git yourcommand

#### What are your most frequent actions using git?
Can you think of certain set of commands that you use daily with git and maybe multiple times a day? One of the most frequent actions I do is to commit code and push to remote branch multiple times a day. Whenever I feel I have reached a state that I would like to remember, I commit. 

#### Isn't it too many words to type?
When the team is big, the master branch might update regularly. Before each commit, I updated my local with the master. This prevents conflicts when making pull requests. I had to do the following six steps many times a day.Instead of `master` branch in the example below, it can be any base branch on which multiple developers in your team are working simultaneously.
	
	git stash
	git checkout master
	git pull --rebase origin master
	git checkout current-branch
	git rebase master
	git stash apply

I thought that for a task that I do frequently everyday, "There is got to be a better way. And there is Kevin!"

<p align="center">
  <img src ="img/posts/git-refresh/better-way-joe.gif" />
</p>

#### Any shortcuts?
* **`&&`**: How about we use `&&` in between commands on terminal? So all commands can be in one line Ah! :unamused:.

        git stash && git checkout master && git pull --rebase origin master &&
        git checkout current-branch && git rebase master && git stash apply


* **`alias`:** Well, `&&` doesn't help much. How about we shorten each command Meh! :expressionless:?
        
        git status -> git st
        git checkout master -> git co master
        git pull --rebase origin master -> git pro master
        git stash apply -> git sap

    If you open `~/.gitconfig` file and add the following mapping, you can create alias of the git commands:

        [alias]
            st = status
            co = checkout
            pro = pull --rebase origin
            sap = stash apply

    With the shortcuts, the chain of command becomes: 

        git stash && git co master && git pro master && git co current-branch && git rebase master && git sap

    `alias`'s are handy when using one line commands but not helping for our case.

#### Give me one line solution!
* **`git refresh master`**: How about one command to stash the unstaged changes, checkout the base branch, pull the remote changes, come back to our branch and bring our current branch up-to-date with the base branch? :astonished:. Lets see how to create a script quickly. 

    - Step 1: Create a file called `git-yourcommand` (`git-refresh` in our case) and keep it in any folder, lets call `gitScripts`. It has following commands:

            #!/bin/sh

            # Check if params are sufficient enough to go ahead.
            remoteBranch=$1
            test -z $remoteBranch && echo "ERROR: Please provide the source branch with which you want to update your current branch." 1>&2 && exit 1

            # Find which is your current branch
            if currentBranch=$(git symbolic-ref --short -q HEAD)
            then
              echo On branch $currentBranch
              echo Pulling updates from the remote branch $remoteBranch ...
              
              # Stash current changes
              git stash
              # Checkout remote branch from where you want to update. 
              git checkout $remoteBranch
              # Pull the branch to update it
              git pull --rebase origin $remoteBranch
              # Checkout current branch which you were on before.
              git checkout $currentBranch
              # Rebase the changes
              git rebase $remoteBranch
              # Appply the stashed changess
              git stash apply

              echo Updated the $currentBranch with changes from $remoteBranch
            else
              echo ERROR: Cannot find the current branch!
            fi

    - Step 2: Add the directory path to your environment `PATH`. For Linux/Mac, you can edit your `bash_profile`. Add following line in the beginning:

            #!/bin/bash
            # For git commands
            export PATH=$PATH:/Users/user/Documents/gitScripts
            # Other existing export statements.
            # End of file
    
        `source` the file to apply the changes and **you are done!**:

            source ~/.bash_profile


#### GitHub Repository
You can find the scripts for the `git refresh` and other custom commands like `git switch` and `git pushremote`  on the following git repository: https://github.com/shobhitpuri/git-refresh 

You can create your own commands by following the above steps. Just create a new file `git-yourcommand` and write the commands in the shell script. You can pass the params as required. Feel free to write your feedback and let me know what awesome shortcuts have you created.
