## Compile and Install the Linux Kernel


This is a quick tutorial on how to download, compile, and install the Linux kernel from its source code. This can be useful if you're building a custom kernel, want to update or revert back to an older kernel, or plan on writing and installing custom kernel modules (like in my case). I've been learning about how to write kernel modules in C, and some advice I've found online is that there can sometimes be issues with loading custom modules onto a pre-compiled kernel. Compiling and installing the kernel yourself is one way to prevent these issues from arising.

This guide is for people using Ubuntu, Mint, or any other Debian-based distribution. The process should be similar on other distributions like Fedora or Arch, but I can't guarantee it'll work since I haven't tested it on any of those distros.

### Download the kernel source and install packages

Download the kernel version of your preference from [kernel.org](https://www.kernel.org/)
If you're note sure which version to get, go with the latest longterm release.

Right click on the tarball link, copy the link address, and paste it into your terminal like this:
```bash
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.6.22.tar.xz
tar -xf linux-6.6.22.tar.gz
```

### Kernel Configuration

Now "cd" into the directory, then we want to copy the .config file from our current kernel so that we don't have to change the configuration manually.
```bash
cd linux-6.6.22
cp -v /boot/config-$(uname -r) .config
```
I highly recommend using the "localmodconfig" command now, which will only include the necessary kernel modules and drivers needed for your specific machine. This speeds up the build process significantly. If you want to edit your configuration manually you can instead use "menuconfig" to change the configuration with a gui.
```bash
make localmodconfig
```
If the configuration process halts and prompts you for anything, just hit enter each time.
Now you need to make some modifications to the .config file with the following commands:
```bash
scripts/config --disable SYSTEM_TRUSTED_KEYS
scripts/config --disable SYSTEM_REVOCATION_KEYS
scripts/config --set-str CONFIG_SYSTEM_TRUSTED_KEYS ""
scripts/config --set-str CONFIG_SYSTEM_REVOCATION_KEYS ""
```

### Compile

Time to compile the kernel. We can run the following command on a machine that only has 1 CPU core and thread, such as in a virtual machine.
```bash
fakeroot make
```
However, all modern CPU's have multiple cores and threads, which we can take advantage of to speed up the build process. This is the command I used for my machine, which has a CPU with 4 cores and 8 threads.
```bash
fakeroot make -j 8
```
If you're not sure how many threads your CPU has, you can find out with this command:
```bash
lscpu | grep -m 1 "CPU(s)"
```
You can also just type in "lscpu" if you want to nerd out on all the cool CPU details like I did.

The amount of time it takes for the build heavily depends on the clock speed, number of threads, cache size, and thermals of your CPU.
I did my kernel build on my laptop, which has an 11th generation Intel i7 clocked at 2.8 GHz that was probably thermal throttling itself due to being a laptop. A desktop computer is preferred for that reason. My build took about forty-five minutes, slower computers will take longer, faster computers less time.

Once the build finishes, make sure to check that there weren't any errors with this command:
```bash
echo $?
```
This outputs the exit code of the make process. A 0 means everything was successful, no errors. Any non-zero result means there was an error during the build.

### Install

Now install the kernel modules, then the kernel:
```bash
sudo make modules_install
sudo make install
```
Now just reboot the machine.
```bash
sudo reboot
```

### Check the kernel version

Once the machine boots back up with its new kernel, make sure that you're on the new version:
```bash
uname -r
```
If your machine booted up correctly and shows the correct kernel version, then the installation was successful!




