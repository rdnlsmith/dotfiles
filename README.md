# dotfiles

Obligatory dotfiles repository. This uses [`stow`](https://www.gnu.org/software/stow/) to maintain symlinks from files in the repo to the appropriate locations in the filesystem, similar to [this blog post by Alex Pearce](https://alexpearce.me/2016/02/managing-dotfiles-with-stow/).

I haven't previously had especially organized dotfiles, so I am using my recent purchase of a used ThinkPad T480 as an opportunity to start fresh. Consequently, this repo is a bit sparse, but it will gradually expand as I continue to use and configure my new computer.

## Overview

A `stow` directory can contain multiple sub-directories, each of which represents a separate program. For example, the hierarchy of my directory as of this writing looks like this:

```
.
├── bash
│   └── .bashrc
├── git
│   └── .gitconfig
├── vscode
│   └── .config
│       └── Code
│           └── User
│               ├── keybindings.json
│               └── settings.json
└── README.md
```

The files for each program are stored in a hierarchy which mirrors their real-world locations under `$HOME`.

### Deploying files

You can deploy all the files for a program with `stow <program name>`:

```bash
$ cd .dotfiles
$ stow bash
$ ls -al ~
total 8804
drwxr-xr-x 29 rdnlsmith rdnlsmith    4096 May 11 20:32 .
drwxr-xr-x  3 root      root         4096 May  5 17:12 ..
-rw-------  1 rdnlsmith rdnlsmith    2865 May 11 19:44 .bash_history
-rw-r--r--  1 rdnlsmith rdnlsmith     220 May  5 17:12 .bash_logout
lrwxrwxrwx  1 rdnlsmith rdnlsmith      22 May 11 20:01 .bashrc -> .dotfiles/bash/.bashrc

...

```

As you can see, `~/.bashrc` is now a symlink to the `.bashrc` in the repository. Any time you edit `~/.bashrc`, those changes will appear as unstaged edits within the repo, which you can commit and push as appropriate. If you push changes from another device, you can `git pull` and all of your deployed dotfiles will be immediately up-to-date.

### `stow` conflicts

If `~/.bashrc` already existed (as it likely does on a new computer), you will receive a conflict warning:

```bash
$ stow bash
WARNING! stowing bash would cause conflicts:
  * existing target is neither a link nor a directory: .bashrc
All operations aborted.
```

`stow` wants to protect you from losing data by overwriting your existing files. If you're sure you don't need them, you can delete any files it lists and run `stow` again. Once a file has been replaced by a symlink from somewhere inside the repo, `stow` will no longer complain, so it's no problem to re-run `stow` multiple times.

### Cleaning up

If you no longer want to manage a program's files via the repo, you can run `stow -D <program name>` to delete all the symlinks which were created for that program:

```bash
$ cd .dotfiles
$ stow -D bash
$ ls -al ~
total 8804
drwxr-xr-x 29 rdnlsmith rdnlsmith    4096 May 11 20:32 .
drwxr-xr-x  3 root      root         4096 May  5 17:12 ..
-rw-------  1 rdnlsmith rdnlsmith    2865 May 11 19:44 .bash_history
-rw-r--r--  1 rdnlsmith rdnlsmith     220 May  5 17:12 .bash_logout

...

```

It's then up to you to replace _e.g._ `~/.bashrc` with a new file.

### Computer-specific settings

If you need a specific machine to have some files be different, you can create a permanent branch for that machine in Git. Later changes that apply to this machine can be merged or cherry-picked into the branch.
