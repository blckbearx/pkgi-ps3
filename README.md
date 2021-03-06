# PKGi PS3

[![Downloads][img_downloads]][pkgi_downloads] [![Release][img_latest]][pkgi_latest] [![License][img_license]][pkgi_license]


`pkgi-ps3` allows to download pkg files on your PS3.

This homebrew allows to download & install pkg files directly on your PlayStation 3.

# Features

* **easy** way to see list of available downloads, including searching, filter & sorting.
* **standalone**, no PC required, everything happens directly on the PS3.
* **automatic** download and unpack, just choose an item, and it will be installed, including bubble in live area.
* **resumes** interrupted download, you can stop download at any time, switch applications, and come back to download
  from place it stopped.

Current limitations:
* **no support for DLC or PSM**.
* **no queuing** up multiple downloads.
* **no background downloads** - if application is closed or Vita is put in sleep then download will stop.

# Download

Get latest version as [pkg file here][pkgi_latest].

# Setup instructions

You need to create a `pkgi.txt` file in `/dev_hdd0/game/NP00PKGI3/USRDIR` that will contain items available for installation. This file is in very
simple CSV format where each line means one item in the list:

```
contentid,flags,name,name2,rap,url,size,checksum
```

where:

* `contentid` is full content id of item, for example: `UP0000-NPXX99999_00-0000112223333000`.
* `flags` is currently unused number, set it to 0.
* `name` is arbitrary string to display for name.
* `name2` is currently unused alternative name, leave it empty.
* `rap`  the 16 hex bytes for a RAP file, if needed by the pkg (.rap will be created on `/dev_hdd0/exdata`). Leave empty to skip .rap file.
* `url` is HTTP URL where to download PKG, pkg content id must match the specified contentid.
* `size` is size of pkg in bytes, or 0 if not known.
* `checksum` is sha256 digest of pkg as 32 hex bytes to make sure pkg is not tampered with. Leave empty to skip the check.

Name cannot contain newlines or commas.

An example `pkgi.txt` file:
```
EP0000-NP9999999_00-0AB00A00FR000000,0,My PKG Test,,dac109e963294de6cd6f6faf3f045fe9,http://192.168.1.1/html/mypackage.pkg,2715513,afb545c6e71bd95f77994ab4a659efbb8df32208f601214156ad89b1922e73c3
UP0001-NP00PKGI3_00-0000000000000000,0,PKGi PS3 v0.1.0,,,http://bucanero.heliohost.org/pkgi.pkg,284848,3dc8de2ed94c0f9efeafa81df9b7d58f8c169e2875133d6d2649a7d477c1ae13
```

# Usage

Using application is pretty straight forward. Select item you want to install and press X. To sort/filter/search press triangle.
It will open context menu. Press triangle again to confirm choice(s) you make in menu. Or press O to cancel any changes you did.

Press left or right shoulder button to move page up or down.

# Q&A

1. Where to get a RAP string? 

  You must use a tool like RIF2RAP plugin to generate a RAP from your existing RIF files. Then you can use any generic Hex viewer to get the hex byte string.

2. Where to get pkg URL?

  You can use [PSDLE][] to find pkg URL for games you own. Then either use original URL, or host the file on your own server.

3. Where to remove interrupted/failed downloads to free up the space?

  In `/dev_hdd0/packages` folder - each download will be in separate pkg file by its title id. Simply delete the file & start again.

4. Download speed is too slow!

  Optimization is still pending.

# Building

You need to have installed:

- [PS3 toolchain](https://github.com/bucanero/ps3toolchain)
- [PSL1GHT](https://github.com/bucanero/PSL1GHT) library
- [tiny3D lib & libfont](https://github.com/wargio/tiny3d) (from Estwald)
- [YA2D lib](https://github.com/bucanero/ya2d_ps3) (an extended version from my repo)
- [dbglogger lib](https://github.com/bucanero/psl1ght-libs/tree/master/dbglogger) (my own debug logging library)

Run `make` to create a debug build, or `cmake -DCMAKE_BUILD_TYPE=Release .` to create an optimized release build.

After than run `make pkg` to create a pkg install file. You can set environment variable `PS3LOAD` (before running `make`) to the PS3's IP address
, that will allow to use `make run` for sending pkgi.self file directly to the PS3Load listener.

To enable debugging logging pass `-DPKGI_ENABLE_LOGGING=ON` argument to cmake. Then application will send debug messages to
UDP multicast address 239.255.0.100:30000. To receive them you can use [socat][] on your PC:

    $ socat udp4-recv:30000,ip-add-membership=239.255.0.100:0.0.0.0 -

# License

`pkgi-ps3` is released under the [MIT License](LICENSE).

[pkg_dec]: https://github.com/weaknespase/PkgDecrypt
[pkg_releases]: https://github.com/mmozeiko/pkgi/releases
[PSDLE]: https://repod.github.io/psdle/
[socat]: http://www.dest-unreach.org/socat/
[pkgi_downloads]: https://github.com/bucanero/pkgi-ps3/releases
[pkgi_latest]: https://github.com/bucanero/pkgi-ps3/releases/latest
[pkgi_license]: https://github.com/bucanero/pkgi-ps3/blob/master/LICENSE
[img_downloads]: https://img.shields.io/github/downloads/bucanero/pkgi-ps3/total.svg?maxAge=3600
[img_latest]: https://img.shields.io/github/release/bucanero/pkgi-ps3.svg?maxAge=3600
[img_license]: https://img.shields.io/github/license/bucanero/pkgi-ps3.svg?maxAge=2592000
