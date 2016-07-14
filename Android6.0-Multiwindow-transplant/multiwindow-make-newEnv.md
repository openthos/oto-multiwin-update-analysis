# Android5.1-multiwindow:

1. 安装以下几个库，新环境中没有安装这几个
```sh
apt-get install gettext bc genisoimage
```

2. 运行
```sh
source build/envsetup.sh

lunch android_x86_64-eng

make -j16 iso_img
```

# Android6.0-multiwindow:

1. 安装以下几个库，新环境中没有安装这几个
```sh
apt-get install gettext bc genisoimage
```

2. 指定USER
```sh
export USER=$(whoami)
```

3. 运行
```sh
source build/envsetup.sh

lunch 7

make -j16 iso_img
```
