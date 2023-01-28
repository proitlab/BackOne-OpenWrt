# BackOne-OpenWrt
## v1.6.6

[BackOne](https://backone.cloud) is a programm to create a global provider-independent virtual private cloud.
This project offers a OpenWrt packages for BackOne.

## Compiling from Sources

### Feed
A working OpenWRT/LEDE build enviroment is expected (else see below).

Put this line in your feed definition (eg. feeds.conf.default)
```
src-git backone https://github.com/proitlab/BackOne-OpenWrt.git
```

Update the feeds
```
./scripts/feeds update
```

Make sure the package is in the feeds now
```
./scripts/feeds search backone
```

Install the package
```
./scripts/feeds install backone
```

Select and build the package
```
make menuconfig
make
```

### The other way

To inlcude BackOne into your OpenWRT image or to create
an .ipk package (equivalent to Debians .deb files),
you have to build an OpenWRT image.

For building OpenWrt on Debian, you need to install these packages:
```
sudo apt-get install subversion g++ zlib1g-dev build-essential git python
sudo apt-get install libncurses5-dev gawk gettext unzip file libssl-dev wget
```

Now build OpenWrt:
```
git clone git://git.openwrt.org/15.05/openwrt.git
cd openwrt

./scripts/feeds update -a
./scripts/feeds install -a

git clone https://github.com/mwarning/zerotier-openwrt.git
cp -rf BackOne-OpenWrt/backone package/
rm -rf BackOne-OpenWrt/

make defconfig
make menuconfig
```

At this point select the appropiate "Target System" and "Target Profile"
depending on what target chipset/router you want to build for.
Also mark the BackOne package under "Network" => "VPN".

Now compile/build everything:

```
make
```

The images and all *.ipk packages are now inside the bin/ folder.
You can install the BackOne .ipk using "opkg install &lt;ipkg-file&gt;" on the router.

For details please check the OpenWRT documentation.
