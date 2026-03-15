# Kernel Building Guide for Beginners

**Author:** @physwizz

---

## Table of Contents

1. [Linux Setup](#1-linux-setup)
2. [Install Repo Tool](#2-install-repo-tool)
3. [Kernel Setup](#3-kernel-setup)
4. [GitHub Setup](#4-github-setup)
5. [Toolchains](#5-toolchains)
6. [Initialize Your Kernel Repo](#6-initialize-your-kernel-repo)
7. [Add Toolchains](#7-add-toolchains)
8. [Build the Kernel](#8-build-the-kernel)
9. [Change Remotes](#9-change-remotes)
10. [Cherry-Picking Commits](#10-cherry-picking-commits)
11. [Push Changes](#11-push-changes)
12. [Revert Changes](#12-revert-changes)
13. [Edit the Last Commit](#13-edit-the-last-commit)
14. [Clone a Repo](#14-clone-a-repo)
15. [Fix Source Errors](#15-fix-source-errors)
16. [Remove Large Files from Git](#16-remove-large-files-from-git)
17. [Exynos 9611 Mali GPU Overclock](#17-exynos-9611-mali-gpu-overclock)
18. [SELinux Permissive](#18-selinux-permissive)
19. [KernelSU (KSU)](#19-kernelsu-ksu)
20. [RKSU](#20-rksu)
21. [Python 2 Setup](#21-python-2-setup)
22. [LZ4 Default in ZRAM](#22-lz4-default-in-zram)
23. [APatch Support](#23-apatch-support)
24. [Build DTB](#24-build-dtb)
25. [Fix: Unrecognized Option `-EL`](#25-fix-unrecognized-option--el)
26. [Fix: strncpy](#26-fix-strncpy)
27. [Fix: WiFi](#27-fix-wifi)
28. [MediaTek A125 Overclock](#28-mediatek-a125-overclock)
29. [X210 Fix (Carlo/cdpkp Method)](#29-x210-fix-carlocdpkp-method)
30. [Build `Image.gz`](#30-build-imagegz)
31. [Search for a Phrase in Files](#31-search-for-a-phrase-in-files)
32. [CPU/GPU Overclock & Underclock](#32-cpugpu-overclock--underclock)
33. [Other Common Config Changes](#33-other-common-config-changes)

---

## 1. Linux Setup

> **Requirements:** 64-bit Linux (author uses Linux Mint MATE)

```bash
sudo apt update && sudo apt upgrade
sudo apt-get install git-all make gcc
sudo apt install python-is-python3 build-essential openssl pip
sudo apt install python3-virtualenv python2.7
sudo aptitude install libssl-dev
sudo apt-get install libtinfo5
pip install virtualenv
export PATH=$PATH:~/.local/bin
```

Configure Git:
```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Github Name"
```

---

## 2. Install Repo Tool

```bash
mkdir -p ~/.bin
PATH="${HOME}/.bin:${PATH}"
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo
chmod a+rx ~/.bin/repo
```

---

## 3. Kernel Setup

1. Download the source code from [Samsung Open Source](https://opensource.samsung.com/main)
   - Search for your device (e.g. `A205`) and select the correct version (e.g. QQ Latin)
2. Extract `kernel.tar.7z` into a folder named `Kernel`

To check your kernel version:
```bash
make kernelversion
```

---

## 4. GitHub Setup

### Create a new repo on GitHub.com, then configure SSH:

> **Note:** You need a new SSH key for each computer you use.

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Github Name"

ssh-keygen -t rsa -b 4096 -C "you@example.com"
# Press Enter 3 times

eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa

sudo apt-get install xclip
xclip -sel clip < ~/.ssh/id_rsa.pub
```

Then go to **GitHub → Settings → SSH and GPG keys → New SSH key** and paste your key.

---

## 5. Toolchains

You can download the toolchain for your device from [Samsung Open Source](https://opensource.samsung.com/main) (search "toolchain"), or clone from the author's GitHub:

```bash
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
```

Alternative: https://github.com/VH-Devices/toolchains

---

## 6. Initialize Your Kernel Repo

1. Set up a folder for your kernel and extract the source code into it.
2. Create a new repo on GitHub.com.
3. Copy the *"create a new repository on the command line"* section from GitHub, which looks like:

```bash
echo "# New" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:YourName/YourRepo.git
git push -u origin main
```

4. Open a terminal in the kernel folder and run:

```bash
git add --all   # Do NOT use --all after a build
git commit -a   # Type "initial", then Ctrl+O → Enter → Ctrl+X
git push -u origin main
```

Set these in your defconfig:
```
CONFIG_LOCALVERSION="-physwizz"
CONFIG_LOCALVERSION_AUTO=n
```

---

## 7. Add Toolchains

Create a `toolchain` directory and add 2 toolchains (usually GCC and Clang) as explained in `README_Kernel.txt`.

---

## 8. Build the Kernel

```bash
./build_kernel.sh
```

---

## 9. Change Remotes

```bash
git remote rm origin
git remote add origin <new-repo-url>
git fetch --all
```

---

## 10. Cherry-Picking Commits

```bash
git remote add other https://<other-address>
# Example:
git remote add physwizz git@github.com:physwizz/A205_ker.git

git fetch --all   # or: git fetch physwizz
git config merge.renameLimit 999999

git cherry-pick <hash>
# For a range:
git cherry-pick <start-hash>^..<end-hash>
```

### Resolving merge conflicts after cherry-pick:

The log will tell you which file is corrupt. Search it for odd lines like:

```
#Head
=========
<<<<
>>> Txt txt txt txt
```

Delete the lower line, then delete all lines from `<<<<` to `#Head`. Then:

```bash
git commit -a   # Ctrl+O → Enter → Ctrl+X
git push origin main -f
git cherry-pick --continue

make clean && make mrproper
git commit -a
git push origin main -f
```

---

## 11. Push Changes

After building:

```bash
make clean && make mrproper
git commit -a   # Write comment, Ctrl+O → Enter → Ctrl+X
git add --all
git push origin main -f
```

### For cherry-picked changes (preserving authorship):

```bash
git commit -m "Description of change" --author="Author Name <author@email.com>"
# Example:
git commit -m "Overclocked GPU using DTBO originally authored by Geckyn." --author="geckyn <geckyn@gmail.com>"
git push origin main
```

---

## 12. Revert Changes

```bash
# Revert a specific commit
git revert <commit-hash>
git push origin main

# Hard reset to a clean state
git reset --hard <last-clean-commit-hash>
git push origin main -f

# Re-apply clean commits after reset
git cherry-pick <clean-commits>
git push origin main
```

---

## 13. Edit the Last Commit

```bash
git commit --amend
git push origin main
```

---

## 14. Clone a Repo

```bash
git clone git@github.com:physwizz/M356b-U-u1-u4-V-u4.git

# Clone a specific branch:
git clone https://github.com/xcitty2029x/twrp_device_samsung_a04e -b twrp-12.1 device/samsung/a04e
```

---

## 15. Fix Source Errors

```bash
git pull origin main
git add a_text_file.txt
git commit -m "Initial commit"
git push origin main --force

touch initialgit
git add initialgit
git commit -m "Fix refs"
git push origin main -f
```

---

## 16. Remove Large Files from Git

```bash
git rm --cached <filename>
git rm --cached <directory> -r
git commit --amend -C HEAD
```

---

## 17. Exynos 9611 Mali GPU Overclock

```bash
git remote add -f gpu git@github.com:physwizz/M315f-S-u4.git
git config merge.renameLimit 999999

# CPU OC
git cherry-pick 0a36a221f1156d199a5241cafb931f64b993ce2c^..9bd157f79a0a7002ae364d9ec355cf00a2842f1f

# Mali GPU OC
git cherry-pick ab3547f1cc3b264e9cbc3df8f69cb7aae4826500^..f078bb9cc217b782563483c07d2ccacbb2e55c11
```

---

## 18. SELinux Permissive

### Method 1 (Gabriel)

```bash
# Togglable SELinux
git remote add -f M346b git@github.com:physwizz/M346B-U-u4.git
git config merge.renameLimit 999999
git cherry-pick 84074a4610ed9d352ac88b7c9e1109b12befbb7e
```

Add to defconfig:
```
# Always permissive
CONFIG_SECURITY_SELINUX_DEVELOP=y
CONFIG_SECURITY_SELINUX_ALWAYS_ENFORCE=n
CONFIG_SECURITY_SELINUX_ALWAYS_PERMISSIVE=y

# Disable DEFEX & PROCA
CONFIG_SECURITY_DEFEX=n
CONFIG_PROCA=n

# Module force loading
CONFIG_MODULE_FORCE_LOAD=y
CONFIG_MODULE_UNLOAD=y
CONFIG_MODULE_FORCE_UNLOAD=y

# Integrity
# CONFIG_INTEGRITY is not set
# CONFIG_INTEGRITY_SIGNATURE is not set
# CONFIG_INTEGRITY_ASYMMETRIC_KEYS is not set
# CONFIG_INTEGRITY_TRUSTED_KEYRING is not set
# CONFIG_INTEGRITY_AUDIT is not set
```

### Method 2 (Thomas)

```
CONFIG_CMDLINE="androidboot.selinux=permissive"
CONFIG_CMDLINE_EXTEND=y

CONFIG_SECURITY_SELINUX_DEVELOP=y
# CONFIG_SECURITY_SELINUX_ALWAYS_ENFORCE is not set
CONFIG_SECURITY_SELINUX_ALWAYS_PERMISSIVE=y

# CONFIG_SECURITY_DEFEX is not set
# CONFIG_PROCA is not set
# CONFIG_FIVE is not set
# CONFIG_UH is not set
# CONFIG_UH_RKP is not set

# CONFIG_INTEGRITY is not set
# CONFIG_INTEGRITY_SIGNATURE is not set
# CONFIG_INTEGRITY_ASYMMETRIC is not set
# CONFIG_INTEGRITY_TRUSTED_KEYRING is not set
# CONFIG_INTEGRITY_AUDIT is not set

# Also needed in some cases:
CONFIG_WATCHDOG=n
```

---

## 19. KernelSU (KSU)

Reference: https://kernelsu.org/guide/how-to-integrate-for-non-gki.html

### Auto Integration

```bash
curl -LSs "https://raw.githubusercontent.com/tiann/KernelSU/main/kernel/setup.sh" | bash -
```

```
CONFIG_KPROBES=y
CONFIG_HAVE_KPROBES=y
CONFIG_KPROBE_EVENTS=y
```

### Manual Integration (specific version)

```bash
curl -LSs "https://raw.githubusercontent.com/tiann/KernelSU/main/kernel/setup.sh" | bash -s v0.9.5
```

```
CONFIG_KSU=y
CONFIG_KPROBES=n
CONFIG_HAVE_KPROBES=n
CONFIG_KPROBE_EVENTS=n
```

```bash
git remote add -f G780 git@github.com:physwizz/G780f-T-m-S20FE.git
git config merge.renameLimit 999999
git cherry-pick ef9130b4c214c72da35132cf42d5546b865e2f02
```

### Required Manual Hook Files

Hook the following functions (typically in `fs/`):

| Function | File |
|---|---|
| `do_faccessat` | `fs/open.c` |
| `do_execveat_common` | `fs/exec.c` |
| `vfs_read` | `fs/read_write.c` |
| `vfs_statx` | `fs/stat.c` |

> **Note:** `C90 forbids mixing declarations and code` — move declarations to the beginning of the block.

---

## 20. RKSU

Reference: https://github.com/rsuntk/KernelSU

```bash
# Latest
curl -LSs "https://raw.githubusercontent.com/rsuntk/KernelSU/main/kernel/setup.sh" | bash -s main

# Specific version
curl -LSs "https://raw.githubusercontent.com/rsuntk/KernelSU/main/kernel/setup.sh" | bash -s v3.0.0-30-legacy

# SUSFS
curl -LSs "https://raw.githubusercontent.com/rsuntk/KernelSU/main/kernel/setup.sh" | bash -s susfs-rksu-master
```

### Hook Methods

**Syscall hook** (GKI / LKM, does not support armv7l):
```
CONFIG_KSU_SYSCALL_HOOK=y
CONFIG_KPROBES=y
CONFIG_KRETPROBES=y
CONFIG_HAVE_SYSCALL_TRACEPOINTS=y
```

**Manual hook** (default for non-GKI where KPROBES is off):
```
CONFIG_KSU_MANUAL_HOOK=y
CONFIG_KSU=y
CONFIG_KPROBES=n
CONFIG_HAVE_KPROBES=n
CONFIG_KPROBE_EVENTS=n
```

### RKSU on kernel 4.19 (manual)

```bash
git remote add -f rksu-4.19 git@github.com:physwizz/A047f.git
git config merge.renameLimit 999999
git cherry-pick 4f1b4cfe1a702757a9df3d84be758418e639c2e9
```

---

## 21. Python 2 Setup

> Often needed for Snapdragon devices. Reference: https://stackoverflow.com/questions/4340873

```bash
sudo apt install pip
pip install virtualenv
export PATH=$PATH:~/.local/bin
sudo apt install python3-virtualenv python2.7

sudo -s
virtualenv -p /usr/bin/python2.7 Vpy27
source Vpy27/bin/activate
```

For subsequent sessions:
```bash
sudo -s
virtualenv -p /usr/bin/python2.7 Vpy27
source Vpy27/bin/activate
```

---

## 22. LZ4 Default in ZRAM

In `drivers/block/zram/zram_drv.c`, change the default compressor to `lz4`.

---

## 23. APatch Support

Add to defconfig:
```
CONFIG_KALLSYMS=y
CONFIG_KALLSYMS_ALL=y

# Or for initial support only:
CONFIG_KALLSYMS=y
CONFIG_KALLSYMS_ALL=n
```

---

## 24. Build DTB

```bash
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
```

---

## 25. Fix: Unrecognized Option `-EL`

In your `Makefile`, find and replace:

```makefile
# Remove this line:
CLANG_FLAGS += --prefix=$(GCC_TOOLCHAIN_DIR)

# Replace with:
CLANG_FLAGS += --prefix=$(GCC_TOOLCHAIN_DIR)$(notdir $(CROSS_COMPILE))
```

Also set:
```makefile
GCC_TOOLCHAIN_DIR := $(dir $(shell which $(CROSS_COMPILE)elfedit))
```

---

## 26. Fix: strncpy

```
# CONFIG_SECURITY_DEFEX is not set
```

Then:

```bash
git remote add a226 git@github.com:physwizz/a226-R.git
git fetch --all
git cherry-pick 3b1bf239a3f17873cb91537cfdaa03173d396b33
git push origin main -f
```

---

## 27. Fix: WiFi

### MediaTek 4.19

1. Remove `drivers/misc/mediatek/connectivity`
2. Copy files from https://github.com/rsuntkOrgs/mtk_connectivity_module into `drivers/misc/mediatek/connectivity`
3. Build the kernel

> Supports mt6768 and mt6765 (tested on A055F, A045F, A042F)

### MediaTek 4.14

```bash
rm -rf drivers/misc/mediatek/connectivity && \
  cd drivers/misc/mediatek/ && \
  git clone https://github.com/rsuntkOrgs/mtk_connectivity_module --depth=1 -b staging-4.14 connectivity && \
  rm -rf connectivity/.git && \
  cd ../../..
```

### Snapdragon (from @RissuDesu)

Add to defconfig:
```
# CONFIG_MODVERSIONS is not set
# CONFIG_MODULE_SIG is not set
# CONFIG_MODULE_SIG_FORCE is not set
# CONFIG_MODULE_SIG_ALL is not set
# CONFIG_MODULE_SIG_SHA512 is not set
# CONFIG_MODULE_SIG_HASH is not set
```

In `kernel/modules.c`, apply this patch:

```diff
@@ -3097,7 +3097,7 @@
  } else if (!same_magic(modmagic, vermagic, info->index.vers)) {
    pr_err("%s: version magic '%s' should be '%s'\n",
           info->name, modmagic, vermagic);
-   return -ENOEXEC;
+   //return -ENOEXEC;
  }
```

### MediaTek WiFi (from @david7xw)

```bash
git remote add mtk -f git@github.com:Samsung-MT6769-Devs/android_kernel_samsung_a22_remake.git
git config merge.renameLimit 999999
git cherry-pick 8fd11aaf66cb0429b8a4f41f9a92c1e720045755
```

### Qualcomm WiFi (from Edward)

```bash
# qcacld-3.0
git remote add qcacld-3.0 https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qcacld-3.0
git fetch qcacld-3.0 LA.UM.9.15.2.c25
git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD
git read-tree --prefix=drivers/staging/qcacld-3.0 -u FETCH_HEAD
git commit

# qca-wifi-host-cmn
git remote add wifi-host https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qca-wifi-host-cmn
git fetch wifi-host LA.UM.9.15.2.c25
git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD
git read-tree --prefix=drivers/staging/qca-wifi-host-cmn -u FETCH_HEAD
git commit

# fw-api
git remote add fw-api https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/fw-api
git fetch fw-api LA.UM.9.15.2.c25
git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD
git read-tree --prefix=drivers/staging/fw-api -u FETCH_HEAD
git commit
```

Add to defconfig:
```
CONFIG_QCA_CLD_WLAN=m
CONFIG_TCP_CONG_WESTWOOD=y
CONFIG_TCP_CONG_HTCP=y
```

Add to `drivers/staging/Kconfig`:
```
source "drivers/staging/qcacld-3.0/Kconfig"
```

Add to `drivers/staging/Makefile`:
```makefile
obj-$(CONFIG_QCA_CLD_WLAN) += qcacld-3.0/
```

---

## 28. MediaTek A125 Overclock

```bash
git remote add a125 -f git@github.com:physwizz/a125f-S.git
git config merge.renameLimit 999999

# CPU/GPU OC
git cherry-pick bb5683f456e9b1df807bf6219876bbb27aa1bc68^..ee4877dab6a387790f4147595a52264d971aeb88

# GSI touch fix
git cherry-pick a0d6921e60c1e749d5b0a65a65de2b60ad660571

# Extra optimizations
git cherry-pick 1864747ba2ed6f7e2e0c021308e2b49b94f6dd1e^..2ff2136501b3f70ad69bf779beca448c7cc57463
```

---

## 29. X210 Fix (Carlo/cdpkp Method)

```bash
# qcacld-3.0
git remote add qcacld-3.0 https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qcacld-3.0
git fetch qcacld-3.0 LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0
git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD
git read-tree --prefix=drivers/staging/qcacld-3.0 -u FETCH_HEAD
git commit

# fw-api
git remote add fw-api https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/fw-api
git fetch fw-api LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0
git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD
git read-tree --prefix=drivers/staging/fw-api -u FETCH_HEAD
git commit

# qca-wifi-host-cmn
git remote add qca https://git.codelinaro.org/clo/la/platform/vendor/qcom-opensource/wlan/qca-wifi-host-cmn
git fetch qca LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0
git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD
git read-tree --prefix=drivers/staging/qca -u FETCH_HEAD
git commit

# datarmnet
git remote add data https://git.codelinaro.org/clo/la/platform/vendor/qcom/opensource/datarmnet
git fetch data LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0
git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD
git read-tree --prefix=drivers/staging/data -u FETCH_HEAD
git commit

# datarmnet-ext
git remote add ext https://git.codelinaro.org/clo/la/platform/vendor/qcom/opensource/datarmnet-ext
git fetch ext LA.UM.9.16.r1-14900.01-MANNAR.QSSI12.0
git merge -s ours --no-commit --allow-unrelated-histories FETCH_HEAD
git read-tree --prefix=drivers/staging/dext -u FETCH_HEAD
git commit

# cdpkp patches
git remote add cdpkp -f git@github.com:cdpkp/android_kernel_tree_samsung_sm6375.git
git cherry-pick 6993575bd4a22a227291bc2b74b6f7cb2cc8f9c8^..78a7cc4a5cfefc0274ff084b127462e46bd890ad
```

---

## 30. Build `Image.gz`

Add to defconfig:
```
CONFIG_KERNEL_GZIP=y
```

---

## 31. Search for a Phrase in Files

```bash
grep -rl "gpu_freq_khz_max" drivers/gpu/arm
```

---

## 32. CPU/GPU Overclock & Underclock

> Overclock by carlos-burelo; underclock by physwizz (a205, Oct 2020)
> Range: 130 MHz – 2210 MHz

```bash
git remote add -f overclock git@github.com:physwizz/a127f-T-u9.git
git config merge.renameLimit 999999
git cherry-pick 3bd273badf7d4f9031c0694bfd953b42ece91835^..f475322515fcf25b10475a38b47a955cdcad88ff
```

---

## 33. Other Common Config Changes

```
CONFIG_LOCALVERSION="-physwizz"
# CONFIG_LOCALVERSION_AUTO is not set

# Stack protection
# CONFIG_CC_STACKPROTECTOR_STRONG is not set

# CPU frequency governors
CONFIG_CPU_FREQ_GOV_PERFORMANCE=y
CONFIG_CPU_FREQ_GOV_POWERSAVE=y
CONFIG_CPU_FREQ_GOV_USERSPACE=y
CONFIG_CPU_FREQ_GOV_ONDEMAND=y
CONFIG_CPU_FREQ_GOV_INTERACTIVE=y
CONFIG_CPU_FREQ_GOV_CONSERVATIVE=y

# Disable Samsung security features
# CONFIG_TIMA is not set
# CONFIG_TIMA_LKMAUTH is not set
# CONFIG_TIMA_LKM_BLOCK is not set
# CONFIG_TIMA_LKMAUTH_CODE_PROT is not set
# CONFIG_UH is not set
# CONFIG_UH_RKP is not set
# CONFIG_RKP_KDP is not set
# CONFIG_RKP_NS_PROT is not set
# CONFIG_RKP_DMAP_PROT is not set
# CONFIG_TIMA_LOG is not set
# CONFIG_KNOX_KAP is not set
# CONFIG_DM_VERITY is not set

# Disable anti-root restrictions
# CONFIG_SEC_RESTRICT_ROOTING is not set
# CONFIG_SEC_RESTRICT_SETUID is not set
# CONFIG_SEC_RESTRICT_FORK is not set
# CONFIG_SEC_RESTRICT_ROOTING_LOG is not set
# CONFIG_KNOX_NCM is not set

# Disable DEFEX & PROCA
CONFIG_SECURITY_DEFEX=n
CONFIG_PROCA=n
CONFIG_FIVE=n
CONFIG_UH=n

# Integrity
# CONFIG_INTEGRITY is not set
# CONFIG_INTEGRITY_SIGNATURE is not set
# CONFIG_INTEGRITY_ASYMMETRIC_KEYS is not set
# CONFIG_INTEGRITY_TRUSTED_KEYRING is not set
# CONFIG_INTEGRITY_AUDIT is not set
# CONFIG_SECURITY_DSMS is not set

# Module force loading
CONFIG_MODULE_FORCE_LOAD=y
CONFIG_MODULE_UNLOAD=y
CONFIG_MODULE_FORCE_UNLOAD=y

# Compiler optimizations
CONFIG_CC_OPTIMIZE_FOR_PERFORMANCE=y
CONFIG_CC_OPTIMIZE_FOR_PERFORMANCE_O3_OFAST_SUB_OPTIONS=y
# CONFIG_CC_OPTIMIZE_FOR_SIZE is not set

# ZRAM
CONFIG_ZRAM=y
CONFIG_ZRAM_WRITEBACK=y
CONFIG_ZRAM_MEMORY_TRACKING=y
# CONFIG_CRYPTO_LZO is not set
CONFIG_CRYPTO_LZ4=y

# Kernel image format
CONFIG_KERNEL_GZIP=y
```

---

*@physwizz*
