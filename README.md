# pacnoob

A simple script for finding and dealing with
[.pacnew](https://wiki.archlinux.org/index.php/Pacman/Pacnew_and_Pacsave)
files produced during Arch Linux system upgrades.


## Motivation

As an Arch Linux novice, I was not aware of the importance of `.pacnew` files.
During a recent upgrade, I failed to notice the warning message (buried in
hundreds of lines of output) telling me that my `/etc/pam.d/system-login` file
was changed and needed to be manually updated. After a reboot, I found myself
[unable to login](https://bugs.archlinux.org/task/67641) to my own system.
Although the solution turned out to be straightforward (boot with kernel command
line `init=/bin/bash` and edit the file), the moment of panic was real.

So I started reading about `.pacnew` files and ways to manage them
automatically. `pacdiff` seemed great at first, but I'm not a huge fan of
`vimdiff` or merge tools in general. I tried a couple others as well, but none
of them felt quite right. So I made `pacnoob`.


## Usage

Download the script, make it executable, and copy it to `/usr/local/bin/`:
```
$ curl https://raw.githubusercontent.com/jwgcarlson/pacnoob/master/pacnoob -o pacnoob
$ chmod +x pacnoob
$ sudo cp pacnoob /usr/local/bin/
```

To run it automatically after a system upgrade (so that you never forget), add a
[Pacman hook](https://wiki.archlinux.org/index.php/Pacman#Hooks):
```
$ sudo mkdir -p /etc/pacman.d/hooks/
$ sudo tee -a /etc/pacman.d/hooks/pacnoob.hook >/dev/null << EOF
[Trigger]
Operation = Upgrade
Type = Package
Target = *

[Action]
Description = Running pacnoob
When = PostTransaction
Exec = /usr/local/bin/pacnoob
EOF
```


## TODO

Ultimately I'd like to have something like `git add --patch`, which is a
fantastic tool for selectively applying a subset of a diff. I couldn't find a
good way to use it outside of a `git` context though, so I settled for a cheap
knock-off.
