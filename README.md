# Synok3s
## K3S on Synology ds218

Based on: https://medium.com/@marco.mezzaro/k3s-on-synology-what-if-it-works-e980b4b09fcb

## Compile missing kernel modules

download from sourceforge kernel source and dsm toolchain (my DS918+ has apollolake intel cpu)
- https://sourceforge.net/projects/dsgpl/files/Synology%20NAS%20GPL%20Source/24922branch/apollolake-source/ and download linux-4.4.x.txz
- https://sourceforge.net/projects/dsgpl/files/DSM%206.2.2%20Tool%20Chains/Intel%20x86%20Linux%204.4.59%20%28Apollolake%29/

from a brand new debian system:
create a folder:

```
sudo apt update
sudo apt install wget 
mkdir -p ~/dsm/archives 
cd ~/dsm
```
put:
- apollolake-gcc493_glibc220_linaro_x86_64-GPL.txz
- linux-4.4.x.txz

install all build deps:

```
sudo apt-get install mc make gcc build-essential kernel-wedge libncurses5 libncurses5-dev libelf-dev binutils-dev kexec-tools makedumpfile fakeroot lzma bc libssl-dev
```

extract and move txz archives away:

```
tar Jxvf linux-4.4.x.txz 
tar Jxvf apollolake-gcc493_glibc220_linaro_x86_64-GPL.txz 
mv linux-4.4.x.txz apollolake-gcc493_glibc220_linaro_x86_64-GPL.txz archives/
```

enter in kernel source folder and copy kernel config in place

```
cd linux-4.4.x/
cp synoconfigs/apollolake .config
```

export magic number version (if not exported could cause headaches!) and alias for dsm make

Magic number for x64 is `+`, where on arm64 it is `+1` 🤯
```
export LOCALVERSION=+
alias dsm6make='make ARCH=x86_64 CROSS_COMPILE=~/dsm/x86_64-pc-linux-gnu/bin/x86_64-pc-linux-gnu-'
```

config the kernel, select all modules for iptables and make modules:

```
dsm6make menuconfig
dsm6make modules
find . -iname "*.ko" -type f
```

copy the kernel modules in /lib/modules on synology machine. `DO NOT OVERWRITE` already present modules.
use insmod command to load.
for troubleshooting check dmesg for errors.


## Links

- http://billauer.co.il/blog/2013/10/version-magic-insmod-modprobe-force/
- https://stackoverflow.com/questions/9341701/cross-compiling-a-kernel-module-invalid-module-format
