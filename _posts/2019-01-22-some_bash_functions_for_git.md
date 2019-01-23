---
title: Some bash functions for git
tags: [git, bash]
image: "/images/Bash_git/git.png"
bigimg: "/images/Bash_git/banner.jpg"
---

Here some git related functions in my `.bachrc`. Is mostly a backup for me, but it might also be useful for someone else.

## Cloning a git repo

Because I usually clone repos from my github account, this is a shortcut that allows me to just type git <repo_name> and it will create the URL.

```bash

    function clone {

        if [ $# -eq 0 ]; then
            echo "Please enter repo name or full url:";
            read repo;
            clone $repo;
        elif [[ $1 == --help ]] || [[ $1 == --h ]] || [[ $1 == --? ]]; then
            echo "This will clone a git repo.";
            echo "";
            echo "Option 1: You can just provide the name, eg:";
            echo "$ clone membership";
            echo "This will do: git clone https://github.com/phillip-kruger/membership.git";
            echo "";
            echo "Option 2: Provide the full URL";
            echo "$ clone https://github.com/smallrye/smallrye-rest-client.git";
            echo "This will do: git clone https://github.com/smallrye/smallrye-rest-client.git";
        else    
            if [[ $1 == https://* ]] || [[ $1 == git://* ]] || [[ $1 == ssh://* ]] ; then
                URL=$1;
            else
                URL='https://github.com/phillip-kruger/'$1'.git';
            fi    

            echo git clone "$URL";
            git clone "$URL";
        fi
    }

    export -f clone

```

**Usage:** 

clone <reponame>  - this will go to my github account

![clone1](/images/Bash_git/clone_1.gif)

clone <url>       - clone the repo at the url

![clone1](/images/Bash_git/clone_2.gif)

clone             - will ask for the repo name or url

![clone1](/images/Bash_git/clone_3.gif)

## Syncing your fork to upstream

If you contribute to projects, and you are working against your own fork, this is 
a handy way to keep you fork in sync with changes in the upstream master.

```bash

    function sync {

        if git remote -v | grep -q 'upstream'; then
            echo "upstream exist";
        else
            echo "Please enter the upstream git url:";
            read url;
            git remote add upstream "$url"
        fi

        git remote -v
        git fetch upstream
        git pull upstream master
        git checkout master
        git rebase upstream/master
    }

    export -f sync

```

![clone1](/images/Bash_git/sync.gif)

## Commit

Normal commit, but adding -s to include your signature.

```bash
    function commit {

        if [ $# -eq 0 ]; then
            echo "Please enter a commit message:";
            read msg;
            commit "$msg";
        elif [[ $1 == --help ]] || [[ $1 == --h ]] || [[ $1 == --? ]]; then
            echo "This will commit changes to a local git repo, eg:";
            echo "$ commit 'some changes made'";
            echo "This will do: git commit -s -m 'some changes made'";
        else    
            echo git commit -s -a -m "$1"
            git commit -s -a -m "$1";
        fi
    }

    export -f commit
```

