# ELM2
ELM2 is Petalinux project for Ultrascale+ board (for TIFR).

### Repository clone
`git clone https://github.com/algreshilov88/ELM2`

### Petalinux build
* Open terminal on your host machine
* `cd /path/to/petalinux 2020.1/folder`
* `source settings.sh`
* `cd /path/to/ELM2/project`
* `petalinux-config`
* Enable following `FPGA Manager` by clicking 'Y' button
  - `FPGA Manager`
* Exit from general config menu
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
    - `Xilinx Zynq MPSoC firmware interface`
    - `Xilinx Zynq MPSoC firmware debug APIs`
* Enable following `Device Drivers`
  - `FPGA Configuration Framework`
    - `Xilinx Zynq UltraScale+ MPSoC FPGA `
    - `FPGA debug fs`
* Exit from Kernel config menu
* `petalinux-build`
* `petalinux-package --boot --format BIN --fsbl images/linux/zynqmp_fsbl.elf --u-boot images/linux/u-boot.elf --pmufw images/linux/pmufw.elf --fpga images/linux/*.bit --force`
* Copy boot.scr, BOOT.BIN, image.ub files from ELM2/images/linux to BOOT partition on SD card

### Bitstream update on BOOT partition of SD card
Replace path to `.bit` file with requested one in `petalinux-package ...` command. After first time building of project no need to rebuild it again, only `petalinux-package ...` command needs to be run with a new `.bit` file.

### Load bitstream to FPGA
Set flags for Full Bitstream:
`echo 0 > /sys/class/fpga_manager/fpga0/flags`

Load the Bitstream: 
`mkdir -p /lib/firmware`
`cp (.bit/.bin file) /lib/firmware/`
`echo (.bit/.bin file) > /sys/class/fpga_manager/fpga0/firmware`

### Reboot Linux system on device remotely from userspace
Ultrascale+ doesn't have reboot functionality by default via "reboot" command from userspace because SRST (both PS and PL reset) is handled in the PMU Firmware. 
So was configured kernel so that we have pmu firmware interface in userspace to be able to reset system from linux command line. Use following command: 
`echo pm_system_shutdown 1 > /sys/kernel/debug/zynqmp-firmware/pm`
