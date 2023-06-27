# Git Hints and Tips

[Home](../index)

* [Git Setup Troubleshooting](setup-troubleshooting)
* [A Workflow For Jumping Between Red and Green Side](red-green-working)
* [Detect If You Are In The Right Tag Or Commit](git-commit-detect)
* [Using Git Bundle](using-git-bundle)

# Quick Tips

## Set default branch name

``git config --global init.defaultBranch main``

## Set your github access token in the remote

This means you no longer need to provide credentials to sync with githib when you make changes in the particular repository.

First create a token in github: Settings / Developer Settings / Personal Access Tokens / Tokens (classic)

Then edit the "push" remote to use the token. Add your username and token in the host part of the push URL.

```
git remote -v

origin  https://github.com/hindmasj/hindmasj.github.io.git (fetch)
origin  https://github.com/hindmasj/hindmasj.github.io.git (push)

git remote set-url --push origin  https://hindmasj:ghp_xxx@github.com/hindmasj/hindmasj.github.io.git
```

Be careful to keep this local repository private as those tokens give full access to your github account.

## Configure git to trust local certificates on Windows

Assuming you have a private repository server to use as a remote, and you are using Windows as your local development environment, then you need to get git to trust the certificates from the remote, instead of using "sslVerify=false" all the time.

``git config --global http.sslBackend schannel``
