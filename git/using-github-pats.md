# Using GitHub Personal Access Tokens

[Home](../index) | [Up](git-index)

In Progress

GitHub really want you to use the fine-grained personal access tokens (PAT). This guide shows you how to set it up on Rocky Linux with local git repositories with GitHub remotes.

## Create Token

Create the token from the menu "Account" --> "Settings" --> "Credentials" --> "Fine-grained personal access tokens".

Generate a new token, give it a new name and description.

* Set expiry date. For personal use set it to "never expire".
* Set repository = "All repositories"
* Add Permission = "Content" --> "Read & Write access"

Create the token and then save it somewhere safe, like your password manager. You will never see this token again, but you can change the permissions for it.

## Change Your Push Remote

You cannot store the PAT in plaintext in the git remote as you could for the classic tokens. So remove any existing tokens from the remote's push URL.

```
git remote show origin
git remote set-url --push origin https://github.com/<username>/<repo-name>.git
```

Now test the token by trying a push. You will need to paste in the token value when prompted for the password.

```
$ git push
Username for 'https://github.com': foo
Password for 'https://foo@github.com':
Everything up-to-date
```

## Install Github CLI Credential Manager (gh)

(This bit is in progress)

The [GitHub CLI](https://github.com/cli/cli/blob/trunk/docs/install_linux.md#rpm) contains a credential manager for integrating into git on the Linuc command line. This needs to be manually installed as it is not available in the main Rocky 9 repo.

* Verify the GPG keys.
* Install the repo using DNF4 (for Rocky 9) commands.
* Cache your new token in gh. Apply the following configuration. Press return to accept the defaults.
  * Use on github.com (default)
  * Preferred protocol = 'https' (default)
  * Authenticate with GitHub credentails = 'Y' (default)
  * How would you like to login = Paste token. (Press down to select)
  * Paste your authentication token and press return.
  * Success message will show the correct username.

```
curl -fsSL -o - https://cli.github.com/packages/githubcli-archive-keyring.asc | gpg --show-keys

sudo dnf install 'dnf-command(config-manager)'
sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install gh

gh auth login
...
? Paste your authentication token: *********************************************************************************************
- gh config set -h github.com git_protocol https
✓ Configured git protocol
! Authentication credentials saved in plain text
✓ Logged in as foo
```
Now try a push on some new commit and test that it works.

---
[Home](../index) | [Up](git-index)
