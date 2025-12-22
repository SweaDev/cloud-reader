# Amazon Cloud Reader

A very simple reader for Amazon Cloud Reader.

## Install from binary

Download latest deb package from [here](https://github.com/metalerk/cloud-reader/releases/latest)

```sh
sudo dpkg -i cloud-reader_<VERSION>_all.deb
```

## Install from source code

**Before installing, make sure to setup the build environment for golang in the 
[webview docs](https://github.com/webview/webview_go/blob/master/README.md)**
```sh
go get github.com/webview/webview_go
```

### Linux

```sh
$ git clone git@github.com:metalerk/cloud-reader.git
$ cd cloud-reader
$ GOOS=linux go build -o cloud-reader cloud_reader.go
```

### Other

```sh
go build -o cloud-reader cloud_reader.go
```

## Run

```sh
cloud-reader
```

## Screenshots

![screenshot1](https://i.imgur.com/SZhjZA5.png)
![screenshot1](https://i.imgur.com/0weXE1d.png)
![screenshot1](https://i.imgur.com/wbZgKQH.png)
![screenshot1](https://i.imgur.com/KXBxEjD.png)


## Troubleshooting

In case you see this:

```
# github.com/webview/webview_go
# [pkg-config --cflags -- gtk+-3.0 webkit2gtk-4.0]
Package webkit2gtk-4.0 was not found in the pkg-config search path.
Perhaps you should add the directory containing webkit2gtk-4.0.pc' to the PKG_CONFIG_PATH environment variable
Package 'webkit2gtk-4.0', required by 'virtual:world', not found
```

### Install the required libraries

Run:

```
$ sudo apt update
$ sudo apt install -y \
    libgtk-3-dev \
    libwebkit2gtk-4.1-dev \
    pkg-config \
    build-essential
```

This gives us **GTK3 + WebKitGTK** dev files and a compiler toolchain.

###  Create a compatibility symlink for pkg-config

`webview_go` is asking for `webkit2gtk-4.0`, but Ubuntu only ships `webkit2gtk-4.1.pc`.

We can make a small shim so pkg-config thinks `4.0` exists:

### find where the .pc file actually is
```
dpkg -L libwebkit2gtk-4.1-dev | grep '\.pc$'
```

We'll see something like:

`/usr/lib/x86_64-linux-gnu/pkgconfig/webkit2gtk-4.1.pc`


Now create a fake `4.0` file pointing to `4.1`:

```
cd /usr/lib/x86_64-linux-gnu/pkgconfig
```

**if the path from dpkg -L was different, cd there instead**

```
sudo ln -s webkit2gtk-4.1.pc webkit2gtk-4.0.pc
```

Verify:

```
pkg-config --cflags gtk+-3.0 webkit2gtk-4.0
```

If that prints some `-I/usr/include/...` flags and no error, we’re good.

### Build the program

Now just:

```
cd /path/to/your/project
go build -o cloud-reader cloud_reader.go
```

If CGO is disabled for some reason, enable it:

```
export CGO_ENABLED=1
go build -o cloud-reader cloud_reader.go
```
