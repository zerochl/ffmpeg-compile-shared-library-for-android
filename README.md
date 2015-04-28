# ffmpeg for android shared library
  移植ffmpeg到android，编译可用于jni调用的so库.<br>
  编译出的so在android apk中的使用参考我的另一个项目[ffmpeg-jni-sample](https://github.com/dxjia/ffmpeg-jni-sample)

# 环境
  ubuntu 12.04LTS x86_64<br>
  android-ndk64-r10-linux-x86_64<br>
  ffmpeg 2.6.2<br>
  我是在ubuntu下进行移植的，windows下应该也可以，没有尝试过。

# 获取代码
```
  git clone git@github.com:dxjia/ffmpeg-for-android-shared-library.git
```

# 使用
##Step 1
安装android linux NDK以及SDK，并配置环境变量；<br>
从[ffmpeg官网](http://ffmpeg.org/)下载ffmpeg源码包;<br>
##Step 2
修改build_android.sh中的 TMPDIR、NDK、SYSROOT、TOOLCHAIN、PREFIX变量为自己的具体情况，具体如下：<br>
#####1.指定零时目录
```
export TMPDIR=/home/djia/tmpdir
```
必须要有，ffmpeg编译要用；<br>
#####2.指定NDK路径
```
NDK=/home/djia/android/android-ndk-r10
```
#####3.指定使用NDK Platform版本
```
SYSROOT=$NDK/platforms/android-16/arch-arm/
```
这里指定的ndk platform的路径，一定要`选择比你的目标机器使用的版本低的`，比如你的手机是android-15版本，那么就选择低于15的<br>
#####4.指定编译工具链
```
TOOLCHAIN=/home/djia/android/android-ndk-r10/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64
```
#####5.指定编译后的安装目录
```
PREFIX=/root/workspace/ffmpeg_shared_compile/dxjia_ffmpeg_install
```
这个目录是ffmpeg编译后的so的输出目录，会有一个include和lib文件夹生成在这里，这也是我们之后要在android apk中使用的.<br>
<br>
#####build_android.sh示例
可以修改该文件来控制ffmpeg的编译config来达到自己想要的库文件，我这里为了得到动态链接库，--enable-shared，并--disable-static，我开放了所有的编解码器，如果有不需要的，可以通过--disable-coder和--disable-decoder来指定，具体查看ffmpeg文档.


```java
#!/bin/bash
export TMPDIR=/home/djia/tmpdir
NDK=/home/djia/android/android-ndk-r10
SYSROOT=$NDK/platforms/android-16/arch-arm/
TOOLCHAIN=/home/djia/android/android-ndk-r10/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64
function build_one
{
./configure \
--prefix=$PREFIX \
--enable-shared \
--disable-static \
--disable-doc \
--disable-ffmpeg \
--disable-ffplay \
--disable-ffprobe \
--disable-ffserver \
--disable-doc \
--disable-symver \
--enable-small \
--cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
--target-os=linux \
--arch=arm \
--enable-cross-compile \
--sysroot=$SYSROOT \
--extra-cflags="-Os -fpic $ADDI_CFLAGS" \
--extra-ldflags="$ADDI_LDFLAGS" \
$ADDITIONAL_CONFIGURE_FLAG
make clean
make
make install
}
CPU=arm
PREFIX=/root/workspace/ffmpeg_shared_compile/dxjia_ffmpeg_install
ADDI_CFLAGS="-marm"
build_one
```
##Step 3
```
cd source/ffmpeg
./build_andrioid.sh
```

##Step 4
等待一段时间后，会在 $PREFIX 目录下生成 include和lib两个文件夹，将lib文件夹中的 pkgconfig 目录和so的链接文件删除，只保留so文件，然后将include 和lib两个目录一起copy到你的apk jni下去编译，具体请参考我的另外一个项目[ffmpeg-jni-sample](https://github.com/dxjia/ffmpeg-jni-sample)

# Reference & Thanks
  [android-ffmpeg-tutorial](https://github.com/roman10/android-ffmpeg-tutorial)
