# kill-rk
## userland Azazel and Jynx2 rootkit removal script
this script is designed to remove Azazel and Jynx2 from the system. the script utilizes very simple flaws in the rootkits and uses the flaws to bypass the rootkit file protections.
</br>
# how does it work
it's rather simple really.</br>
## azazel
Azazel comes with a flaw that allows a user to call symlink() on any protected rootkit files. by utilizing this flaw, we can bypass basic file protection, allowing us to read from files and write to them too. the script initially checks to see if the Azazel shared object file (libselinux.so) exists, then creates a temporary symbolic link to ld.so.preload in /tmp, then writes a temporary value to ld.so.preload via this symbolic link so that the rootkit's shared object file is no longer being loaded. it then proceeds to remove other miscellaneous rootkit files, and copies the shared object file to /tmp.
</br>
how to patch this? overwrite the symlink() and symlinkat() library symbols and protect your files from those two calls.</br>
#### azazel_patched
I applied patches to the original Azazel source that patches the symlink() and symlinkat() vulnerabilities. I also stopped users from being able to check if the Azazel shared object file exists. this will stop my script from being able to remove installations of Azazel.
## jynx2
unlike Azazel, this presented some issues. since Jynx2 uses magic GIDs to hide the majority of rootkit files and directories, we can't just use a symlink() vulnerability to bypass rootkit file protection. we have to utilize the previously existing reality.so library that Jynx2 installs in its installation directory in order to temporarily bypass rootkit file protection. this more or less works the same as the Azazel removal part of the script, except this time we need to use reality.so. this option also copies both jynx2.so and reality.so to /tmp.
</br>
how to patch this? stop using external libraries to call the previous symbols. do it internally via the rootkit's core library itself.</br>
#### jynx2_patched
not patched this yet. I will at some point though.
</br>
## usage
```
root@localhost ~# ./killrk.sh
/-/ argument not given
Usage: ./killrk.sh [option]
       azazel -- removes the userland azazel rootkit
       jynx2 -- removes the similar (to azazel) userland rootkit
```
