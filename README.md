# kernal-cheat-sheet
This repository contains some common commands used to play around with the linux kernel

## Check Current Kernel

```bash
uname -r
```
sample output
```bash
5.15.0-33-generic
```
## List Installed Kernel on Machine

Debian Based Systems (Ubuntu/Debian/Kali/Rpi)
```bash
dpkg --list | grep linux-image
```

Sample Output
```bash
ii  linux-image-4.19.234.mptcp                    20220311125841-1                        amd64        Linux kernel, version 4.19.234.mptcp
rc  linux-image-5.15.0-23-generic                 5.15.0-23.23                            amd64        Signed kernel image generic
rc  linux-image-5.15.0-25-generic                 5.15.0-25.25                            amd64        Signed kernel image generic
ri  linux-image-5.15.0-27-generic                 5.15.0-27.28                            amd64        Signed kernel image generic
ii  linux-image-5.15.0-33-generic                 5.15.0-33.34                            amd64        Signed kernel image generic
ii  linux-image-generic-hwe-22.04                 5.15.0.33.36                            amd64        Generic Linux kernel image
ii  linux-image-unsigned-5.15.0-23-generic-dbgsym 5.15.0-23.23                            amd64        Linux kernel debug image for version 5.15.0 on 64 bit x86 SMP

```

## Latest Linux Kernel for Ubuntu (Easy Way)
 1. GOTO https://kernel.ubuntu.com/~kernel-ppa/mainline/
 2. Find Latest Kernel (6.0.1 in my case)
 3. Select your architecture (if youre not sure, then most likely you have a amd64 machine)
 4. Download all 4 .deb packages (you can also do this from the terminal as follows)
    ```bash
    mkdir -p v6.0.1 && cd v6.0.1
    wget 'https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.0.1/amd64/linux-headers-6.0.1-060001-generic_6.0.1-060001.202210120833_amd64.deb'
    wget 'https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.0.1/amd64/linux-headers-6.0.1-060001_6.0.1-060001.202210120833_all.deb'
    wget 'https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.0.1/amd64/linux-image-unsigned-6.0.1-060001-generic_6.0.1-060001.202210120833_amd64.deb'
    wget 'https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.0.1/amd64/linux-modules-6.0.1-060001-generic_6.0.1-060001.202210120833_amd64.deb'
    ```
 5. Install downloaded packages
    ```bash
     # IF NO OTHER .deb files are there in your folder simply run
     sudo dpkg -i *.deb
     # ELSE dpkg -i <insert all 4 file names seperated by space> 
     ```
 6. Confim Install is complete by checking list
    ```bash
    dpkg --list | grep 6.0.1
    ``` 
 7. reboot 
 8. On grub screen select advanced options for ubuntu
 9. select your desired kernel

## Building Linux Kernel
 This method has been adapted from [kernel compile notes](https://askubuntu.com/questions/718381/how-to-compile-and-install-custom-mainline-kernel/718662#718662) with few aditions based on personal experience
 1. Download desired kernel from https://kernel.org as tar
 2. Unpack the tar ball
    ```bash
    tar xvf linux-6.0.1.tar.xz
    ```
 3. Update System and Install Necessary packages
    ```bash
    sudo apt-get update
    sudo apt-get dist-upgrade
    sudo apt-get install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison
    ```
 4. Configure kernel and build
    ```bash
    cd linux-6.0.1
    
    # Copy existing configuration from current kernel
    cp /boot/config-$(uname -r) .config
    
    # (OPTIONAL) edit config using GUI menu
    make menuconfig

    # Ubuntu config file has full debugging. 
    # Makes an enormous kernel and takes twice as long to compile
    # (OPTIONAL) We can disable debug_info
    scripts/config --disable DEBUG_INFO

    # We also need to override certificate checking
    scripts/config --disable SYSTEM_TRUSTED_KEYS
    scripts/config --disable SYSTEM_REVOCATION_KEYS

    # Build Kernel and Modules
    # -j option specifies number of parallel jobs you want the process to use
    # nproc shows number of procesing units available
    # time records the time taken to compile it took me 95minutes
    time make -j $(nproc)

    # Install necessary modules
    time sudo make modules_install

    # Install Kernel
    sudo make install
    ```
 ## Modprobe
 modprobe - Add and remove (enable/disable) modules from the Linux Kernel
 Syntax
 ```bash
 # enable
 modprobe <module_name>
 # disable
 modprobe -r <module_name>
 ```
 ## List active modules
 ```bash
 lsmod
 ```