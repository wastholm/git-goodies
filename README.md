# Git Goodies

Some extra gadgets for your Git tool belt.


## git-hogs

Find the largest files in a Git repo, no matter where they are or if they're in your current working directory.


### Example Usage

Find the ten largest files in the [public Bitcoin Git repo](https://github.com/bitcoin/bitcoin):

```
$ git clone git@github.com:bitcoin/bitcoin.git
[some Git chit chat]
$ cd bitcoin
$ git-hogs
src/qt/res/icons/bitcoin.psd
libeay32.dll
contrib/macdeploy/background.psd
src/qt/res/icons/bitcoin.icns
uiproject.fbp
src/qt/res/icons/bitcoin.icns
uiproject.fbp
share/pixmaps/bitcoin.ico
src/qt/res/icons/bitcoin.png
src/qt/res/icons/bitcoin.png
```

As can be seen above, the exact same path may appear multiple times in the list. Each such entry will of course correspond to a different commit.

Same again, but also show the hash and size of each entry:

```
$ git-hogs --long
c0bf5e3937cda96d76d73cea34dcbd7dd5ae1384      2689594 src/qt/res/icons/bitcoin.psd
3bc745c4c0f4751cab5195d9fbdf19ea42113016      1306630 libeay32.dll
fdc4f4ca4a07ea4c6082ee1357b6ec7e8db99d72       982442 contrib/macdeploy/background.psd
b04ad6867f6d477d623298bebb5e0f6796f8f7b3       973805 src/qt/res/icons/bitcoin.icns
313e5aa6cccec63ab2eda55b4e35134814981163       952868 uiproject.fbp
54d02d34dd9f11a784acd7de8d7ef7ed44849505       919273 src/qt/res/icons/bitcoin.icns
3f5519094e848306fe5427c3a361ca1d5a118a60       457620 uiproject.fbp
48a9822a46abd9da41d10f44dae5c34ba405afcc       353118 share/pixmaps/bitcoin.ico
705a20260af0ebd2f2fbe3f3dfaf99f32db4be3b       350390 src/qt/res/icons/bitcoin.png
435621af23b440792cd26ccfc419379613d10586       312944 src/qt/res/icons/bitcoin.png
```

Evict the three biggest files (completely erase them from the repo; see below):

```
$ git-hogs --num=3 | xargs git-evict
[more Git chit chat]
```


## git-evict

Erase files *completely* from your Git repo -- that is, don't just delete them, make it look as though they were never there. This is good if you want to get rid of sensitive data that has been accidentally committed to Git, or if you want to downsize a Git repo by completely eliminating large files. The downside is that it will make further pushing and pulling difficult; there is no way around this since we are rewriting Git history. Everyone working on this repo will most likely need to re-clone it, so be sure to coordinate your work before evicting files.


### Example Usage

Let's look at that Bitcoin repo again and see how much we can downsize it by completely getting rid of the three largest files:

```
$ cd ~
$ git clone git@github.com:bitcoin/bitcoin.git
[again Git talks to itself]
$ cd bitcoin
$ du -hs .
87M     .
$ git-hogs --num=3 | xargs git-evict
[yes, more Git output here]
$ du -hs .
53M     .
```

Also evict that `bitcoin.png` file, but save the most-recently committed version to `/tmp`:

```
$ git-evict --evict-to=/tmp src/qt/res/icons/bitcoin.png
/home/peter/bin/git-evict: warning: path exists in HEAD, skipping: src/qt/res/icons/bitcoin.png
```

By default, `git-evict` will not expel files currently in the working directory. But suppose we insist:

```
$ git-evict --force --evict-to=/tmp src/qt/res/icons/bitcoin.png
[Git working and talking]
$ ls /tmp/src/qt/res/icons/bitcoin.png
/tmp/src/qt/res/icons/bitcoin.png
```
