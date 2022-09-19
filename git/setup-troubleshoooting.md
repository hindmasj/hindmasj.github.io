# Setup Troubleshooting

[Home](../index.md) | [Up](git-index.md)

Overcoming the main problems you encounter cloning and setting up a new workspace.

# Ignore SSL
If the remote repo does not have trusted SSL certificates.
## When Cloning
```
git -c http.sslVerify=false clone <url>
```

## Fix Forward
```
git config http.sslVerify false
```
# Ignore A Proxy
In cases where the repo is not behind a proxy, but a proxy has been set up.

## Solve Once
This is solved in bash.
```
export no_proxy=<url>
```

## Solve In Config
```
git config --add remote.<name>.proxy ""
```

# Saving Credentials
To avoid being prompted for your password every time you push. You store the credentials, so you never get asked again, or you can cache them to avoid having to use them too often.

## Store
```
git config credential.helper store
```

## Cache
```
git config credential.helper cache
```
Which gives 15 minutes by default. For longer, set the timeout. For 8 hours (ie the rest of the day). Note the quoting around the whole config definition.
```
git config credential.helper 'cache --timeout 28800'
```

# Avoid Push Warning
```
git config --global push.default simple
```
---
[Home](../index.md) | [Up](git-index.md)
