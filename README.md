SM-N9005 Kernel with usbtv module
---------------------------------

Based on the [Blaze™ Kernel -2](http://forum.xda-developers.com/showthread.php?t=2758405) + [backported usbtv module](https://github.com/mwelchuk/usbtv)

Tested with the [Echoe Rom v.13](http://forum.xda-developers.com/showthread.php?t=2495688)

## How to build: ([also usable](http://forum.xda-developers.com/showthread.php?t=2583732))
1. Create new virtual machine with `minimal ubuntu 14.04 x86_64` and go into through ssh
2. Install requirements:

  ```sh
  sudo apt-get install git ccache build-essential libncurses5-dev:i386 g++-multilib zlib1g-dev:i386 libreadline6-dev:i386 lzop
  ```
3. Create folders & clone repos - kernel & tools:

  ```sh
  mkdir -p ~/src/boot ~/toolchain
  git clone https://github.com/rabits/kernel_n9005.git ~/src/kernel_n9005
  git clone https://github.com/rabits/mkbootimg_tools ~/src/bootimg-tools
  ```
4. Get best toolchain from linaro project:

  ```sh
  wget -P ~/toolchain/ http://releases.linaro.org/13.04/components/toolchain/binaries/gcc-linaro-arm-linux-gnueabihf-4.7-2013.04-20130415_linux.tar.bz2
  tar xf ~/toolchain/gcc-linaro-arm-linux-gnueabihf-4.7-2013.04-20130415_linux.tar.bz2 -C ~/toolchain/
  ```
5. Make kernel:

  ```sh
  cd ~/src/kernel_n9005
  make ARCH=arm VARIANT_DEFCONFIG=msm8974_sec_hlte_eur_defconfig msm8974_sec_defconfig SELINUX_DEFCONFIG=selinux_defconfig CONFIG_CROSS_COMPILE=$HOME/toolchain/gcc-linaro-arm-linux-gnueabihf-4.7-2013.04-20130415_linux/bin/arm-linux-gnueabihf-
  make -j4 ARCH=arm
  ```
6. Create dt.img:

  ```sh
  cd ~/src/kernel_n9005
  scripts/dtbTool -s 2048 -o arch/arm/boot/dt.img -p scripts/dtc/ arch/arm/boot/
  ```
7. Get original blaze kernel zip and unpack boot.img into ~/src/boot
8. Build new boot.img:

  ```sh
  cd ~/src/boot
  ~/src/bootimg-tools/mkboot boot.img ./mod/
  cp ~/src/kernel_n9005/arch/arm/boot/zImage ~/src/kernel_n9005/arch/arm/boot/dt.img ./mod/
  ~/src/bootimg-tools/mkboot ./mod/ boot_mod.img
  ```
9. Zip boot_mod.img into original zip archive as boot.img and TWRP or CWM it!
