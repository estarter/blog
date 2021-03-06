extract a folder into a separate repo: https://help.github.com/articles/splitting-a-subfolder-out-into-a-new-repository/
```bash
$ git filter-branch --prune-empty --subdirectory-filter FOLDER-NAME  BRANCH-NAME 
# Filter the specified branch in your directory and remove empty commits
Rewrite 48dc599c80e20527ed902928085e7861e6b3cbe6 (89/89)
Ref 'refs/heads/BRANCH-NAME' was rewritten

```

merge repos:
```bash
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "illegal number of parameters"
    exit 1
fi

if [ -d "$1" ]; then
    echo "Folder already exists"
    exit 2
fi

git clone gitlab@gitlab.e3ag.net:dlp/$1.git
cd $1

git remote remove origin

git filter-branch --prune-empty --tree-filter '
if [[ ! -e '$1' ]]; then
    mkdir -p '$1'
    git ls-tree --name-only $GIT_COMMIT | xargs -I files mv files '$1'
fi' --tag-name-filter 'sed s/^/'$1'_/'

cd ../dlp-controller
git checkout develop
git remote add $1 ../$1
git fetch $1
git merge $1/develop --allow-unrelated-histories -m "merge $1"
git remote rm $1%                                                                                                                                                                                                                                                                                                                                                         ➜  deleteme2 
```
