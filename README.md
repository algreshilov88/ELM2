# ELM2
ELM2 is Petalinux project for Ultrascale+ board (UCLA).

### Repository clone
`git clone https://github.com/algreshilov88/ELM2`

### Petalinux build
* Open terminal on your host machine
* `cd /path/to/petalinux/folder`
* `source settings.sh`
* `cd /path/to/ELM2/project`
* `petalinux-build`
* `petalinux-package --boot --format BIN --fsbl images/linux/zynqmp_fsbl.elf --u-boot images/linux/u-boot.elf --pmufw images/linux/pmufw.elf --fpga images/linux/*.bit --force`
* Copy boot.scr, BOOT.BIN, image.ub files from ELM2/images/linux to BOOT partition on SD card

### Bitstream update
Replace path to `.bit` file with requested one in `petalinux-package ...` command.

### Reboot Linux system on device from userspace
Ultrascale+ doesn't have reboot functionality by default via "reboot" from userspace because SRST (both PS and PL reset) is handled in the PMU Firmware. 
So was configured kernel so that we have pmu firmware interface in userspace to be able to reset system from linux command line. Use following command: 
`echo pm_system_shutdown 1 > /sys/kernel/debug/zynqmp-firmware/pm`
