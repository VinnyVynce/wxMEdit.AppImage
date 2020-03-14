# AppImage Diary
This file is the procedure on how I made this AppImage. This file will be part of my procedure for future releases of [wxMEdit](https://github.com/wxMEdit/wxMEdit).

## Building wxMEdit from source
Since it was on a pretty old version of Ubuntu, installing the dependencies was pretty easy.
```
sudo apt-add-repository 'deb http://repos.codelite.org/wx3.0.4/ubuntu/ xenial universe'
sudo apt update
sudo apt-get install libwxbase3.0-0-unofficial libwxbase3.0-dev libwxgtk3.0-0-unofficial libwxgtk3.0-dev wx3.0-headers wx-common libwxbase3.0-dbg libwxgtk3.0-dbg wx3.0-i18n wx3.0-examples wx3.0-doc libboost-all-dev libcurl4-gnutls-dev icu-devtools glib-2.0 gtk+-2.0
```
Install the latest release of wxMEdit Github page
```
tar xvf wxMEdit-3.1_FIX1.tar.gz 
cd wxMEdit-3.1_FIX1/
./configure --prefix=$HOME/wxmedit
make
make install
```
## File structure

```
AppDir
├── AppRun
├── bin
│   └── wxmedit
├── lib
│   ├── libasn1.so.8
│   ├── libatk-1.0.so.0
│   ├── libcairo.so.2
│   ├── libcrypt.so.1
│   ├── libcurl-gnutls.so.4
│   ├── libffi.so.6
│   ├── libgnutls.so.30
│   ├── libgraphite2.so.3
│   ├── libgssapi_krb5.so.2
│   ├── libgssapi.so.3
│   ├── libgtk-x11-2.0.so.0
│   ├── libhcrypto.so.4
│   ├── libheimbase.so.1
│   ├── libheimntlm.so.0
│   ├── libhx509.so.5
│   ├── libicudata.so.55
│   ├── libicui18n.so.55
│   ├── libicuuc.so.55
│   ├── libjbig.so.0
│   ├── libk5crypto.so.3
│   ├── libkeyutils.so.1
│   ├── libkrb5.so.26
│   ├── libkrb5.so.3
│   ├── libkrb5support.so.0
│   ├── liblber-2.4.so.2
│   ├── liblzma.so.5
│   ├── libnettle.so.6
│   ├── libpcre.so.3
│   ├── libpixman-1.so.0
│   ├── libpng12.so.0
│   ├── libroken.so.18
│   ├── librtmp.so.1
│   ├── libsasl2.so.2
│   ├── libselinux.so.1
│   ├── libsqlite3.so.0
│   ├── libtasn1.so.6
│   ├── libwx_baseu_unofficial-3.0.so.0
│   ├── libwx_gtk2u_unofficial_aui-3.0.so.0
│   ├── libwx_gtk2u_unofficial_core-3.0.so.0
│   ├── libXau.so.6
│   ├── libxcb-shm.so.0
│   ├── libXcomposite.so.1
│   ├── libXcursor.so.1
│   ├── libXext.so.6
│   ├── libXfixes.so.3
│   ├── libXinerama.so.1
│   ├── libXi.so.6
│   └── libXxf86vm.so.1
├── wxmedit.desktop
└── wxmedit.png
```
## AppRun
Fairly straightforward, use the shared libraries and launch the app.
```
#!/bin/bash
HERE="$(dirname "$(readlink -f "${0}")")"
export LD_LIBRARY_PATH=${HERE}/lib/:$LD_LIBRARY_PATH
"${HERE}"/bin/wxmedit "$@"
```
## Shared Libraries
To get all the dependencies of the executable:
```
files=$(ldd ./wxmedit | sed -e 's/.*. =>//g' | sed -e 's/ .0x.*.//g' | tr -d " \t\d\r")
for fl in `echo "$files"`; do
	cp -L $fl .
done
```
But doing that is a bit too overkill, [you need to remove some librairies blacklisted by the AppImage team](https://github.com/AppImage/pkg2appimage/blob/master/excludelist).  

However one dependencies was missing when getting "all" of them from ldd.
```
 cp -L /usr/lib/x86_64-linux-gnu/libicudata.so.55 .
```
