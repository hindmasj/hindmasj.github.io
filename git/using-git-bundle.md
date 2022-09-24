# Using Git Bundle

[Home](../index) | [Up](git-index)

Use a bundle to create a versioned extract of part or all of your repository. You use the bundle like a remote but it is not a repo. It represents a fixed point in time for the repo and cannot be pushed to.

## Select The Refs You Need
Only refs (branches or specific commits) you checkout can find their way into the bundle. This is what they mean when they say "set the base". If you need everything then look at "clone --mirror" instead which creates a bare repo that mirrors the origin.

```
git checkout [any refs you need]
```

## Create The Bundle
For example, pick just the main and develop branches. When you clone, the name of the working directory is derived from the name of the bundle, so it is a good idea is to use the suffix ".bundle".
```
git bundle create [--progress] foo.bundle HEAD main develop [...]
```

Or use "--all" for the rev list to get everything. Also use "--branches" and / or "--tags" to get specific

## Verify The Bundle
```
git bundle verify foo.bundle

git bundle list-heads foo.bundle
```

## Use The Bundle
The bundle file can be treated like an archive file, copied or emailed as required. Once you have a bundle file, use it like a remote, but remember it is read-only.

### In A New Working Directory
Clone the bundle. This creates a working directory following the name of the bundle file.
```
git clone foo.bundle
cd foo
git checkout bar
```

## In An Existing Directory
Add the bundle as a remote and pull and checkout as required.
```
git remote add bndl foo.bundle
```

## Finally
A bundle is a one way street. To send your changes back, create a new bundle with the refs you have changed. At the far end add the new bundle as a remote and pull in the changes; push them to the main repo as needed.

---
[Home](../index.md) | [Up](git-index.md)
