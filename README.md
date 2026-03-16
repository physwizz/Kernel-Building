# Kernel-Building


Building a Kernel for beginners
@physwizz
************************************


Requirements

Use Linux 64bit (I use Linux Mint Mate)

1. Linux setup
===============
type the followung into the linux terminal

sudo apt update
sudo apt upgrade
sudo apt-get install git-all make gcc

git config --global user.email "you@example.com"
git config --global user.name "Your Github Name"


sudo apt install python-is-python3 build-essential openssl pip


pip install virtualenv

export PATH=$PATH:/home/physwizz/.local/bin

apt install python3-virtualenv

sudo apt install python2.7

sudo aptitude install libssl-dev

sudo apt-get install libtinfo5

2. install repo
===============

mkdir -p ~/.bin
PATH="${HOME}/.bin:${PATH}"
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo
chmod a+rx ~/.bin/repo



3. Kernel setup
===============

Download the Source Code (https://opensource.samsung.com/main)
        Search for A205 and select QQ version (Latin)
    Extract the kernel.tar.7z 
    extract into "Kernel" folder


Kernel version
**************
to find your kernel version type
make kernelversion	

4. Github
=========
1. Go to github.com and Open a new Repo 

2. Set up SSH 
Note: You will need a new SSH key for each computer you use.

git config --global user.email "you@example.com"
git config --global user.name "Your Github Name"


ssh-keygen -t rsa -b 4096 -C "you@example.com"

Press enter 3 times

eval "$(ssh-agent -s)"

ssh-add ~/.ssh/id_rsa

sudo apt-get install xclip

xclip -sel clip < ~/.ssh/id_rsa.pub

Go to github => settings=> SSH and GPG keys

New SSH key

Paste key here

5. Toolchains
=============

You can download the toolchain for your device directly from Samsung Open source by searching fot "toolchain"

you can also get some of mine from my github:

mkdir ~/toolchains && cd ~/toolchains

git clone git@github.com:physwizz/toolchains-for-snapdragon.git
git clone git@github.com:physwizz/clang13.git
git clone git@github.com:physwizz/toolchains-for-exynos-850.git
git clone git@github.com:physwizz/Toolchains-for-Eureka.git
git clone git@github.com:physwizz/toolchain_clang-triple.git
git clone git@github.com:physwizz/toolchain_clang.git
git clone git@github.com:physwizz/toolchain_cross-compile.git
git clone git@github.com:physwizz/compiler.git
git clone git@github.com:physwizz/clang-r383902.git
git clone git@github.com:physwizz/clang-r353983c.git
https://github.com/VH-Devices/toolchains

6. Setting up the repo
======================
- set up a folder for your kerel.
- download the kernel source code from Samsung Open Source
- Extract the Kernel.tar.gz into the kernel folder

Create new repo on GitHub.com
Like this one
https://github.com/physwizz/New
Copy the text section "or create a new repository on the command line"
Something like this 

echo "# New" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:physwizz/New.git
git push -u origin main


Open Terminal In The Kernel Folder

Paste the section from github
Type
git add --all #( or use add .)
git commit -a
Type "initial"
Ctrl o
Ctrl x 
git push -u origin main
(DON'T add --all AFTER BUILD)


CONFIG_LOCALVERSION="-physwizz"
CONFIG_LOCALVERSION_AUTO=n


7. Add the toolchains
=====================
Add the toolchains as explained in README_Kernel.txt
You may want to create the "toolchain" directory and insert 2 of my toolchains (usually GCC and Clang)

8. To Build the kernel.
======================

type 

./build_kernel.sh


9. to change repos
==================

open a terminal in the kernel directory

git remote rm origin
git remote add origin <new repo>

git fetch --all

10. to cherry-pick
=================

git remote add other https:<other address>

eg
git remote add physwizz git@github.com:physwizz/A205_ker.git

git fetch --all   

or    git fetch physwizz

git config merge.renameLimit 999999

git cherry-pick [hash_id]

or git cherry-pick [hash_id]^..[hash_id] for a range


Sometimes you get extra lines of text in one of the files that should be deleted. The log on screen will tell you which file is corrupt.
Search the file for odd lines like this.


Head =========

<<<<

>>> Txt txt txt txt

Delete the lower line
Then delete all lines from
 <<<< to Head

git commit -a
ctrl-o enter ctrl-x
git push origin main -f

git cherry-pick —continue

make clean && make mrproper
git commit -a

git push origin main -f


11. to push changes
==================
After you build

make clean && make mrproper
git commit -a

write comment
ctrl o (to output)
enter
ctrl x (to exit)

git add --all

git push origin main -f



#After a cherry pick

sudo git commit -m "change made" --author="Author's name<author's email>"


eg
sudo git commit "change made" --author="Chatur27<jasonbright2709@gmail.com>"

git commit -m "Overclocked GPU using DTBO originally authored by Geckyn and modified by Chateu." --author="geckyn <geckyn@gmail.com>"


then

git push origin main



12. If it doesn't work or you change your mind
==============================================

git revert <commit hash code>
git push origin main

Or
git reset --hard < last clean commit hash >

git push origin main -f


git cherry-pick <clean commits >

git push origin main



13. to edit last commit
=======================

sudo git commit --amend
git push origin main


14. To clone a repo
===================

git clone git@github.com:physwizz/M356b-U-u1-u4-V-u4.git

To clone a branch

git clone https://github.com/xcitty2029x/twrp_device_samsung_a04e -b twrp-12.1 device/samsung/a04e

15. for src error
=================
git pull origin main

git add a_text_file.txt 
sudo git commit -m "Initial commit" 

git push origin main --force
git 

touch initialgit
git add initialgit
git commit -m "Fix refs" 
sudo git push origin main -f

----------------------------------------------------------------------------------

16. to remove large files from git --all
======================================
git rm --cached <filename>

git rm --cached <directory> -r

then 
git commit --amend -C HEAD 


17. for Exynos 9611 Mali GPU oc
===============================

git remote add -f gpu git@github.com:physwizz/M315f-S-u4.git

#CPU oc
git cherry-pick 0a36a221f1156d199a5241cafb931f64b993ce2c^..9bd157f79a0a7002ae364d9ec355cf00a2842f1f

#Mali GPU oc

git cherry-pick ab3547f1cc3b264e9cbc3df8f69cb7aae4826500^..f078bb9cc217b782563483c07d2ccacbb2e55c11



18. Permissive
==============

To change your enforcing kernel to permissive copy and paste the following commands into terminal. Then build it

make enforcing branch


#method 1 from Gabriel


#togglable selinux
git remote add -f M346b git@github.com:physwizz/M346B-U-u4.git

git config merge.renameLimit 999999

git cherry-pick 84074a4610ed9d352ac88b7c9e1109b12befbb7e

#Always permissive

CONFIG_SECURITY_SELINUX_DEVELOP=y
CONFIG_SECURITY_SELINUX_ALWAYS_ENFORCE=n
CONFIG_SECURITY_SELINUX_ALWAYS_PERMISSIVE=y

#disable defex & proca

CONFIG_SECURITY_DEFEX=n

CONFIG_PROCA=n

#module force 
CONFIG_MODULE_FORCE_LOAD=y
CONFIG_MODULE_UNLOAD=y
CONFIG_MODULE_FORCE_UNLOAD=y


#integrity

CONFIG_INTEGRITY=n
CONFIG_INTEGRITY_SIGNATURE=n
CONFIG_INTEGRITY_ASYMMETRIC_KEYS=n
CONFIG_INTEGRITY_TRUSTED_KEYRING=n
CONFIG_INTEGRITY_AUDIT=n

#Method 2 from Thomas 


CONFIG_CMDLINE="androidboot.selinux=permissive"
CONFIG_CMDLINE_EXTEND=y

CONFIG_SECURITY_SELINUX_DEVELOP=y
CONFIG_SECURITY_SELINUX_ALWAYS_ENFORCE=n
CONFIG_SECURITY_SELINUX_ALWAYS_PERMISSIVE=y

CONFIG_SECURITY_DEFEX=n

CONFIG_PROCA=n

CONFIG_FIVE=n

CONFIG_UH=n

CONFIG_UH_RKP=n


CONFIG_INTEGRITY=n
CONFIG_INTEGRITY_SIGNATURE=n
CONFIG_INTEGRITY_ASYMMETRIC=n
CONFIG_INTEGRITY_TRUSTED_KEYRING=n
CONFIG_INTEGRITY_AUDIT=n

#Also needed in some cases
CONFIG_WATCHDOG=n

19. kernelSU ksu
================
https://kernelsu.org/guide/how-to-integrate-for-non-gki.html


#Auto
curl -LSs "https://raw.githubusercontent.com/tiann/KernelSU/main/kernel/setup.sh" | bash -


CONFIG_KPROBES=y
CONFIG_HAVE_KPROBES=y
CONFIG_KPROBE_EVENTS=y


#Manually

curl -LSs "https://raw.githubusercontent.com/tiann/KernelSU/main/kernel/setup.sh" | bash -s v0.9.5

#KernelSU
CONFIG_KSU=y

CONFIG_KPROBES=n

CONFIG_HAVE_KPROBES=n

CONFIG_KPROBE_EVENTS=n

git remote add -f G780 git@github.com:physwizz/G780f-T-m-S20FE.git

git config merge.renameLimit 999999

git cherry-pick ef9130b4c214c72da35132cf42d5546b865e2f02


do_faccessat, usually in fs/open.c
do_execveat_common, usually in fs/exec.c
vfs_read, usually in fs/read_write.c
vfs_statx, usually in fs/stat.c

and follow instructions for manual changes

C90 forbids mixing declarations and code
#move code to the beginning of the block


20. rksu 
========

https://github.com/rsuntk/KernelSU

curl -LSs "https://raw.githubusercontent.com/rsuntk/KernelSU/main/kernel/setup.sh" | bash -s main

curl -LSs "https://raw.githubusercontent.com/rsuntk/KernelSU/main/kernel/setup.sh" | bash -s v3.0.0-30-legacy

#susfs
curl -LSs "https://raw.githubusercontent.com/rsuntk/KernelSU/main/kernel/setup.sh" | bash -s susfs-rksu-master

#Hook method

Syscall hook:
Used for Loadable Kernel Module (LKM) or GKI with this hook.
Default hook method on GKI kernels.
Does not support armv7l.
CONFIG_KSU_SYSCALL_HOOK=y
CONFIG_KPROBES=y
CONFIG_KRETPROBES=y
CONFIG_HAVE_SYSCALL_TRACEPOINTS=y

Manual hook:
See this repository for more information
Default hook method on Non-GKI kernels, with condition that CONFIG_KPROBES off by default.

#rksu
CONFIG_KSU_MANUAL_HOOK=y
CONFIG_KSU=y

CONFIG_KPROBES=n

CONFIG_HAVE_KPROBES=n

CONFIG_KPROBE_EVENTS=n

#rksu 4.19 manual
git remote add -f rksu-4.19 git@github.com:physwizz/A047f.git


git config merge.renameLimit 999999

git cherry-pick 4f1b4cfe1a702757a9df3d84be758418e639c2e9

21. to add python 2 (often needed for snapdragon)
===================
https://stackoverflow.com/questions/4340873/how-do-you-switch-between-python-2-and-3-and-vice-versa


sudo apt install pip

pip install virtualenv

export PATH=$PATH:/home/physwizz/.local/bin

apt install python3-virtualenv

sudo apt install python2.7

sudo -s

which python2.7

which python3


virtualenv -p /usr/bin/python2.7 Vpy27


source Vpy27/bin/activate


#from then on

sudo -s

virtualenv -p /usr/bin/python2.7 Vpy27

source Vpy27/bin/activate

21. lz4 default in zram
======================
#drivers/block/zram/zram_drv.c

#change to lz4

22. To support Apatch
=====================

You need modify kernel source and build  to enable
CONFIG_KALLSYMS=y 
CONFIG_KALLSYMS_ALL=y

CONFIG_KALLSYMS=y
CONFIG_KALLSYMS_ALL=n
Initial support 

23. to build dtb
================

make clean && make mrproper
export CROSS_COMPILE=/home/grahame/toolchains/Toolchains-for-Eureka-GCC-4.9/bin/aarch64-linux-android-
export ARCH=arm64
export PLATFORM_VERSION=12
export ANDROID_MAJOR_VERSION=s
export LLVM=1 
export DTB_LOC=/home/grahame/f41-S/arch/arm64/boot/dts
export TOOLS_LOC=$(pwd)/scripts/tools/bin
make physwizz_defconfig
make 

$TOOLS_LOC/mkdtboimg.py cfg_create $DTB_LOC/dtb.img --dtb-dir $DTB_LOC/exynos $TOOLS_LOC/dtb.cfg


$TOOLS_LOC/mkdtboimg.py cfg_create $DTB_LOC/dtbo.img --dtb-dir $DTB_LOC/samsung $TOOLS_LOC/dtbo.cfg


24. unrecognized option '-EL' error
===================================

in makefile
GCC_TOOLCHAIN_DIR := $(dir $(shell which $(CROSS_COMPILE)elfedit))

##remove this line
###CLANG_FLAGS	+= --prefix=$(GCC_TOOLCHAIN_DIR)

CLANG_FLAGS	+= --prefix=$(GCC_TOOLCHAIN_DIR)$(notdir $(CROSS_COMPILE))




25. strncpy fix
===============

CONFIG_SECURITY_DEFEX=n

then

git remote add a226 git@github.com:physwizz/a226-R.git
git fetch --all
git cherry-pick 3b1bf239a3f17873cb91537cfdaa03173d396b33

git push -u origin main 

26. wifi fix from rissu
==========================

For 4.19
---------

1. Remove the contents of drivers/misc/mediatek/connectivity

2. Copy the files from https://github.com/rsuntkOrgs/mtk_connectivity_module to drivers/misc/mediatek/connectivity

3. Build the Kernel

» Note: The driver currently only support 4.19 kernel, and only support mt6768 and mt6765 platform (tested on A055F, A045F, and A042F)

For 4.14
--------

Updated.
quick update command:

rm -rf drivers/misc/mediatek/connectivity && cd drivers/misc/mediatek/ && git clone https://github.com/rsuntkOrgs/mtk_connectivity_module --depth=1 -b staging-4.14 connectivity && rm -rf connectivity/.git && cd ../../..


For Snapdragon from @RissuDesu
------------------------------

CONFIG_MODVERSIONS=n

CONFIG_MODULE_SIG=n
CONFIG_MODULE_SIG_FORCE=n
CONFIG_MODULE_SIG_ALL=n

CONFIG_MODULE_SIG_SHA512=n

CONFIG_MODULE_SIG_HASH=n

Open kernel/modules.c, and apply this patch:

@@ -3097,7 +3097,7 @@
   } else if (!same_magic(modmagic, vermagic, info->index.vers)) {
     pr_err("%s: version magic '%s' should be '%s'\n",
            info->name, modmagic, vermagic);
-    return -ENOEXEC;
+    //return -ENOEXEC;
   }
 
   if (!get_modinfo(info, "intree")) {


#Wifi fix mtk from @david7xw

git remote add mtk -f git@github.com:Samsung-MT6769-Devs/android_kernel_samsung_a22_remake.git


git config merge.renameLimit 999999

git cherry-pick  8fd11aaf66cb0429b8a4f41f9a92c1e720045755


Qualcomm wifi fix

#From Edward


Initial merge:

git remote add qcacld-3.0 https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qcacld-3.0

git fetch qcacld-3.0 LA.UM.9.15.2.c25

git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD

git read-tree --prefix=drivers/staging/qcacld-3.0 -u FETCH_HEAD

git commit

#Updating to a newer tag:

git fetch qcacld-3.0 <TAG>

git merge -X subtree=drivers/staging/qcacld-3.0 FETCH_HEAD

git remote add wifi-host https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qca-wifi-host-cmn

git fetch wifi-host LA.UM.9.15.2.c25

git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD

git read-tree --prefix=drivers/staging/qca-wifi-host-cmn -u FETCH_HEAD

git commit


git remote add fw-api https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/fw-api

git fetch fw-api LA.UM.9.15.2.c25

git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD

git read-tree --prefix=drivers/staging/fw-api -u FETCH_HEAD

git commit

#add to defconfig
CONFIG_QCA_CLD_WLAN=m
CONFIG_TCP_CONG_WESTWOOD=y
CONFIG_TCP_CONG_HTCP=y

#add to drivers/staging/kconfig
source "drivers/staging/qcacld-3.0/Kconfig"

#add to drivers/staging/Makefile
obj-$(CONFIG_QCA_CLD_WLAN)  +=qcacld-3.0/

#build it

27. a125 oc mediatek
====================

git remote add a125 -f git@github.com:physwizz/a125f-S.git

git config merge.renameLimit 999999

#oc
git cherry-pick bb5683f456e9b1df807bf6219876bbb27aa1bc68^..ee4877dab6a387790f4147595a52264d971aeb88

#gsi touch fix
git cherry-pick a0d6921e60c1e749d5b0a65a65de2b60ad660571

#extra optimisations
git cherry-pick 1864747ba2ed6f7e2e0c021308e2b49b94f6dd1e^..2ff2136501b3f70ad69bf779beca448c7cc57463


28. X210 fix Carlo Dandan cdpkp method
======================================

git remote add qcacld-3.0 https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qcacld-3.0

git fetch qcacld-3.0 LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0

git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD

git read-tree --prefix=drivers/staging/qcacld-3.0 -u FETCH_HEAD

git commit


git remote add fw-api https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/fw-api

git fetch fw-api LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0

git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD

git read-tree --prefix=drivers/staging/fw-api -u FETCH_HEAD

git commit


git remote add qca https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qca-wifi-host-cmn

git fetch qca LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0

git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD

git read-tree --prefix=drivers/staging/qca -u FETCH_HEAD

git commit

git remote add data https://git.codelinaro.org/clo/la/platform/vendor/qcom/opensource/datarmnet

git fetch data LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0

git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD

git read-tree --prefix=drivers/staging/data -u FETCH_HEAD

git commit


git remote add ext https://git.codelinaro.org/clo/la/platform/vendor/qcom/opensource/datarmnet-ext


git fetch ext LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0

git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD

git read-tree --prefix=drivers/staging/dext -u FETCH_HEAD

git commit


git remote add cdpkp -f git@github.com:cdpkp/android_kernel_tree_samsung_sm6375.git

git cherry-pick 6993575bd4a22a227291bc2b74b6f7cb2cc8f9c8^..78a7cc4a5cfefc0274ff084b127462e46bd890ad


GitLab (https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qcacld-3.0)


29. to build Image.gz
=====================

CONFIG_KERNEL_GZIP=y

30. To find the files in a directory which contain this phrase

grep -rl "gpu_freq_khz_max" drivers/gpu/arm

31.Overclocked and underclocked

carlos-burelo authored overclocking
physwizz authored underclocking by modifying oc code (a205 Oct 20 2020)


#overclocking and underclocking 130MHz-2210MHz
git remote add -f overclock git@github.com:physwizz/a127f-T-u9.git

git config merge.renameLimit 999999

git cherry-pick 3bd273badf7d4f9031c0694bfd953b42ece91835^..f475322515fcf25b10475a38b47a955cdcad88ff

32. OTHER CHANGES MADE
======================

CONFIG_LOCALVERSION="-physwizz"
CONFIG_LOCALVERSION_AUTO=n

CONFIG_CC_STACKPROTECTOR_STRONG=n

CONFIG_SECURITY_DEFEX=n

CONFIG_PROCA=n

CONFIG_CPU_FREQ_GOV_PERFORMANCE=y
CONFIG_CPU_FREQ_GOV_POWERSAVE=y

CONFIG_CPU_FREQ_GOV_USERSPACE=y
CONFIG_CPU_FREQ_GOV_ONDEMAND=y

CONFIG_CPU_FREQ_GOV_INTERACTIVE=y

CONFIG_CPU_FREQ_GOV_CONSERVATIVE=y

CONFIG_TIMA=n
CONFIG_TIMA_LKMAUTH=n
CONFIG_TIMA_LKM_BLOCK=n
CONFIG_TIMA_LKMAUTH_CODE_PROT=n
CONFIG_UH=n
CONFIG_UH_RKP=n
CONFIG_RKP_KDP=n
CONFIG_RKP_NS_PROT=n
CONFIG_RKP_DMAP_PROT=n

CONFIG_TIMA_LOG=n
CONFIG_KNOX_KAP=n

CONFIG_DM_VERITY=n

CONFIG_SEC_RESTRICT_ROOTING=n


CONFIG_SEC_RESTRICT_SETUID=n
CONFIG_SEC_RESTRICT_FORK=n
CONFIG_SEC_RESTRICT_ROOTING_LOG=n

CONFIG_INTEGRITY=n
CONFIG_INTEGRITY_SIGNATURE=y
CONFIG_INTEGRITY_ASYMMETRIC_KEYS=n
CONFIG_INTEGRITY_TRUSTED_KEYRING=y
CONFIG_INTEGRITY_AUDIT=n

CONFIG_SECURITY_DSMS=n
CONFIG_PROCA=n

CONFIG_CC_OPTIMIZE_FOR_PERFORMANCE=y
CONFIG_CC_OPTIMIZE_FOR_PERFORMANCE_O3_OFAST_SUB_OPTIONS=y
CONFIG_CC_OPTIMIZE_FOR_SIZE=n

CONFIG_KNOX_NCM=n
CONFIG_ZRAM=y
CONFIG_ZRAM_WRITEBACK=y
CONFIG_ZRAM_MEMORY_TRACKING=y

CONFIG_CRYPTO_LZO=n
CONFIG_CRYPTO_LZ4=y

CONFIG_KERNEL_GZIP=y
Configures Output of image gz

#module force 
CONFIG_MODULE_FORCE_LOAD=y
CONFIG_MODULE_UNLOAD=y
CONFIG_MODULE_FORCE_UNLOAD=y

#disable samsung anti-root
 CONFIG_UH=n
 CONFIG_SECURITY_DEFEX=n
 CONFIG_PROCA=n
 CONFIG_FIVE=n

@physwizz
