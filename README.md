# Modified Linux Kernel 5.1.5 to echo back a cookie in the timestamp

The cookie will be set in the MSB bits of the timestamp, as given by the MSB bits of the cookie in the ECR.

## Compiling
All features are built-in. Just compile the Kernel as you would normally do. A full tutorial for Ubuntu is available at https://wiki.ubuntu.com/Kernel/BuildYourOwnKernel. But these few steps should guide you through a faster compilation and installation if you use Ubuntu 18.04:
- Install dependencis with `sudo apt install build-essential libncurses5-dev flex bison openssl libssl-dev libelf-dev libudev-dev libpci-dev libiberty-dev autoconf`
- Configure your options, eg with you may copy the config file in /boot/configXXX to .config and then use "make olddefconfig" to upgrade the configuration to 5.1.5: `cp /boot/config-$(uname -r) .config && make olddefconfig`
- `make bzImage && make modules`
- `sudo make modules install && sudo make install`
- `sudo reboot`

To enable the cookie "reflection", set the sysctl parameter as follow:
`echo 1 | sudo tee /proc/sys/net/ipv4/tcp_timestamp_cookie`

Then, any connection established will reflect the 16 MSB of the ECR field of a SYN packets to all subsequent packets, except for the single MSB which is used for versioning of the cookie.
A cookie may be changed, but it must start at the SYN.

Note that this has only works with sequential timestamp, so you need to do `echo 2 | sudo tee /proc/sys/net/ipv4/tcp_timestamps`. Mode 1 could be okay but needs testing. Note the cookie obfuscates the timestamp, so this is not a security thread.
