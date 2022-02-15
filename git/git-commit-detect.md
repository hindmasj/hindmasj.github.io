# Detect If You Are In The Correct Tag, Commit Or Branch

[Home](../index.md) | [Back](git-index.md)

I was recently writing some scripts that had to work with external git repositories and they had to be certain they were in specific commits. So the following bash snippets show how you can detect if you are in the correct commit, based on either the hash or the tag, or tracking a branch. Then switch if you are not.

## By Commit Hash (SHA1)

````
COMMIT_SHA="abc123de45"
echo -n "Checking we have checked out correct commit ... "
if [ $(git rev-parse HEAD) != ${COMMIT_SHA} ]
then
  echo no
  echo "Checking out required commit"
  git checkout ${COMMIT_SHA}
else
  echo "yes"
fi
````

## By Commit Tag

````
COMMIT_TAG="1.2.3-rc4"
echo -n "Checking we have checked out correct tag ... "
git describe --tags 2>/dev/null | grep ${COMMIT_TAG}
has_tag=$?
if [ ${has_tag} -ne 0 ]
then
 echo "no"
 git checkout ${COMMIT_TAG}
fi
````

## By Branch

This is only used for checking if you are tracking a branch and want the tip checked out. It is no good if you need to be in a commit further down the branch. Then you need to use the commit hash or tag as defined above.

````
COMMIT_BRANCH="2.3.x-maint"
echo -n "Checking we are checked out in the correct branch ... "
if [ $(git rev-parse --abbrev-ref HEAD) != ${COMMIT_BRANCH} ]
then
  echo no
  echo "Checking out required branch"
  git checkout ${COMMIT_BRANCH}
else
  echo "yes"
fi
````
