---
title: "How I Lost and Recovered 3 Hours Worth of Work Using Git"
date: 2021-04-11T21:37:35+02:00
dscription: "How I Lost and Recovered 3 Hours Worth of Work Using Git"
tags: [Tutorial, Drupal 8, Drupal 9, PHP, unix, Git]
categories: ["Tutorials", "Drupal 8"]
draft: false

featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
code:
  copy: true
  # ...
math:
  enable: true
  # ...
mapbox:
  accessToken: ""
  # ...
share:
  enable: true
  # ...
---


## How I lost and recovered 3 hours' worth of work using Git

### Prologue

So, imagine you sit down in front of your computer; you start a new project in your favourite editor, and you're flowing. Four hours later, you've ticked every item on your to-do list and it's time to commit and push your project to git. You add your files, commit and push. Go on the remote server and what? Half your files are not there. You go back to your local project; you check the head and nothing. Oh  sh.t! you say to yourself; the git ignore excluded a bunch of  files,  and some files that shouldn't have been committed were added. We'll fix it, you think to yourself. Delete the .gitignore, then `git  rm` the files to remove, then you've removed the wrong files. Oh  sh.t, again!. You decide to reset the branch to the origin: `git reset  --hard origin/master`. As soon as you type enter, you realise your uncommitted files are gone. Disappeared. Never to be found again. Oh  f#ck!

<!--more-->


So that's pretty much what happened to me. And This is how I recovered my files:

### Step 1: I tried to use  reflog  to find

After spending some time trying to work out what was possible to recover the lost files, I found out that Git kept some sort of reference of all uncommitted changes  on  something called  a "Directed Acyclic Graph" for internal bookkeeping — don't worry, I, too, do not know what it means, but at least there was some hope. Running `git  reflog` showed the "real" head states and hashes ... and you could checkout one of those states using a head hash and all your files would be back.  E.g.:
  
![git reflog output](https://i.imgur.com/zdWGW2r.jpg)

One problem! If you guess the .gitignore  file, you were right. Because there was the .gitignore  file at the head ref, those files would not be recovered.

### Step 2: If they  reflog  could recover  uncommitted files, then they had to be somewhere, right?

As I went  through  one  stack overflow  post after the other, there seemed to be a consensus. The files were gone and could not be recovered. I tried many things and one of them was `git  fsck` — basically a utility command that "Verifies the connectivity and validity of the objects in the database" according to [the man page](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/git-fsck.html). I tested out [this answer](https://stackoverflow.com/a/7376959/2305403) on  stackoverflow, but the command `git  fsck  --cache  --unreachable $(git for-each-ref  --format="%(objectname)")` outputte  nothing and I quickly gave up on that trail — well, I shouldn't. I come back to that, but I could have saved 20 minute had I spent more time dissecting the answer.

At this point, I  have been looking for almost an hour, and I tell myself, I will just redo the whole work, these things happen, then again, there has to be a way — who the  f#ck  loses files using git, right?

### Step 3: The answer.

To answer my last question, apparently many people. I kept looking on  stack overflow  then I found this [post](https://stackoverflow.com/questions/59706666/is-there-way-to-recover-files-ignored-via-gitignore-in-the-first-commit) that was not as popular has the other ones I looked, but one thing caught my eyes in the accepted [answer](https://stackoverflow.com/a/59707544/2305403) — another mention of `git  fsck`. I read the answer which essentially stated that running the command  ``git  fsck  --lost-found`` moved all unreferenced dangling blob to a directory in .git/lost-found. I executed the commands and checked the directory. There was a bunch of blobs named by hash  strings,  and I knew that those were my lost files. I just had to check them in an editor and rename them.

```
git  fsck  --lost-found
ls .git/lost-found
cd .git/lost-found/other
ls
```

ls command output:

![files in lost-found](https://i.imgur.com/DqeR4Ks.jpeg)
  
### Step 4: Finding the files of interests.

I wanted one module files  particularly -- about  8 or 9 individual files, so I got to work. I ran a  grep command to find the files I wanted and move them into a temp directory and work with them individually. The command I used to copy all files containing a particular keyword is:

`cp  grep  -lir  'my_keyword' * ~/tmp`

And ten minutes later I had all my files back. phew!

### Take away

One big take away I got out of this entire thing is to always run `git revert` over `git reset` for undoing your changes. Git reset will override your history and revert won't.

I hope this helps someone out there.  

R