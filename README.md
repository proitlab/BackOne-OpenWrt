# BackOne-OpenWrt
## v1.12.1

## Compiling from Sources

To include BackOne into your OpenWrt image or to create
an .ipk package (equivalent to Debians .deb files),
you have to build an OpenWrt image.

To build OpenWrt on Debian, you need to install these packages:

```
sudo apt-get install subversion g++ zlib1g-dev build-essential git python
sudo apt-get install libncurses5-dev gawk gettext unzip file libssl-dev wget
```

Now prepare OpenWrt:

```
git clone https://github.com/openwrt/openwrt
cd openwrt

./scripts/feeds update -a
./scripts/feeds install -a
```

Now you can insert the backone package using a package feed (see `Add package by feed`) or add the package manually (see `Add package by hand`).

### Add package by feed

A feed is the standard way packages are made available to the OpenWrt build system.

Put this line in your feeds list file (e.g. `feeds.conf`, or `feeds.conf.default`)

```
src-git backone https://github.com/proitlab/BackOne-OpenWrt.git
```

Update and install the new feed

```
./scripts/feeds update backone
./scripts/feeds install backone
```

Now continue with the building packages section (see `Building Packages`).

### Add package by hand

```
git clone https://github.com/proitlab/BackOne-OpenWrt.git
cp -rf BackOne-OpenWrt/backone package/
rm -rf BackOne-OpenWrt/
```

Now continue with the building packages section (see `Building Packages`).

### Building Packages

Configure packages:

```
make menuconfig
```

Now select the appropiate "Target System" and "Target Profile"
depending on what target chipset/router you want to build for.
Also mark the BackOne package under Network ---> VPN ---> <\*> backone.

Now compile/build everything:

```
make -j8
```

The images and all \*.ipk packages are now inside the `bin/` folder, including the backone package.
You can install the BackOne .ipk on the target device using `opkg install <ipkg-file>`.

For details please check the OpenWrt documentation.

#### Build bulk packages

For a release, it is useful the build packages at a bulk for multiple targets:

```
#!/bin/sh

# dump-target-info.pl is used to get all targets configurations:
# https://git.openwrt.org/?p=openwrt/openwrt.git;a=blob;f=scripts/dump-target-info.pl

./scripts/dump-target-info.pl architectures | while read pkgarch target1 rest; do
  echo "CONFIG_TARGET_${target1%/*}=y" > .config
  echo "CONFIG_TARGET_${target1%/*}_${target1#*/}=y" >> .config
  echo "CONFIG_PACKAGE_example1=y" >> .config

  # Debug output
  echo "pkgarch: $pkgarch, target1: $target1"

  make defconfig
  make -j4 tools/install
  make -j4 toolchain/install

  # Build package
  make package/backone/{clean,compile}

  # Free space (optional)
  rm -rf build_dir/target-*
  rm -rf build_dir/toolchain-*
done
```
