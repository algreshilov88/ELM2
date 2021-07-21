# ELM2
ELM2 is Petalinux project for Ultrascale+ board (for TIFR).

### Repository clone
`git clone https://github.com/algreshilov88/ELM2`

### Petalinux build
* Open terminal on your host machine
* `cd /path/to/petalinux 2020.1/folder`
* `source settings.sh`
* `cd /path/to/ELM2/project`
* `petalinux-config -c kernel`
* Enable following `Power management options` by clicking 'Y' button
  - `Suspend to RAM and standby`
  - `User space wakeup sources interface`
  - `Device power management core functionality`
* Enable following `Kernel hacking`
  - `Compile-time checks and compiler options`
    - `Debug Filesystem`
* Enable following `Firmware Drivers`
  - `Zynq MPSoC Firmware Drivers`
    - `Enable Xilinx Zynq MPSoC firmware interface`
    - `Enable Xilinx Zynq MPSoC firmware debug APIs`
* Exit from Kernel config menu
* `petalinux-build`
* `petalinux-package --boot --format BIN --fsbl images/linux/zynqmp_fsbl.elf --u-boot images/linux/u-boot.elf --pmufw images/linux/pmufw.elf --fpga images/linux/*.bit --force`
* Copy boot.scr, BOOT.BIN, image.ub files from ELM2/images/linux to BOOT partition on SD card

### Bitstream update
Replace path to `.bit` file with requested one in `petalinux-package ...` command. After first time building of project no need to rebuild it again, only `petalinux-package ...` command needs to be run with a new `.bit` file.

### Reboot Linux system on device remotely from userspace
Ultrascale+ doesn't have reboot functionality by default via "reboot" command from userspace because SRST (both PS and PL reset) is handled in the PMU Firmware. 
So was configured kernel so that we have pmu firmware interface in userspace to be able to reset system from linux command line. Use following command: 
`echo pm_system_shutdown 1 > /sys/kernel/debug/zynqmp-firmware/pm`
