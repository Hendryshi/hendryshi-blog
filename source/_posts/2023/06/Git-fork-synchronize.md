---
title: Git fork and synchronize
categories:
  - Tech
tags:
  - Git
abbrlink: bec7bf7f
date: 2023-06-13 23:18:24
---

## Synchronize a forked project

#### Step 1: Enter to your local repository

```BASH
cd themes/butterfly/
```

#### Step 2: launch the command `git remote -v` to check your remote repository
make sure that you have the both "origin" and "source" repository

```bash
SHI YEJIA@Hendryshi MINGW64 /d/WorkSpace/Blog/NewBlog (hexo)
$ cd themes/butterfly/

SHI YEJIA@Hendryshi MINGW64 /d/WorkSpace/Blog/NewBlog/themes/butterfly (master)
$ git remote -v
origin  git@github.com:Hendryshi/hexo-theme-butterfly.git (fetch)
origin  git@github.com:Hendryshi/hexo-theme-butterfly.git (push)
source  https://github.com/jerryc127/hexo-theme-butterfly (fetch)
source  https://github.com/jerryc127/hexo-theme-butterfly (push)
```

#### Step 3: If you only have the origin repository, launch the following command to add the source forked project

```
git remote add source https://github.com/jerryc127/hexo-theme-butterfly.git
```

#### Step 4: Launch `git status` to make sure that you have not any checkout files before pull the latest version

#### Step 5: Launch `git fetch source` to pull the updated version of source forked project

```bash
$ git fetch source
remote: Enumerating objects: 373, done.
remote: Counting objects: 100% (306/306), done.
remote: Compressing objects: 100% (125/125), done.
Rremote: Total 373 (delta 191), reused 257 (delta 181), pack-reused 67
Receiving objects: 100% (373/373), 130.19 KiB | 17.00 KiB/s, done.
Resolving deltas: 100% (206/206), completed with 44 local objects.
From https://github.com/jerryc127/hexo-theme-butterfly
...
```

#### Step 6: Launch `git checkout master` to switch to your master branch

```bash
SHI YEJIA@Hendryshi MINGW64 /d/WorkSpace/Blog/NewBlog/themes/butterfly (master)
$ git checkout master
Already on 'master'
Your branch is up to date with 'origin/master'.
```

#### Step 7: Launch `git merge source/master` to merge the updated code with your local code

```bash
$ git merge source/master
Removing layout/includes/third-party/chat/gitter.pug
Merge made by the 'recursive' strategy.
 README.md                                          |  14 +-
 README_CN.md                                       |  10 +-
 _config.yml                                        | 453 ++++++++++----------
 languages/default.yml                              |   1 +
```

#### Step 8: Launch `git push origin master` to update codes to your personal remote repository



>Link:
>https://www.jianshu.com/p/021bb953ee8d
>https://github.com/selfteaching/the-craft-of-selfteaching/issues/67
