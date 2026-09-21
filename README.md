# Running Linux on Arty A7 (MicroBlaze + PetaLinux 2025.1)

A complete, reproducible guide to building a MicroBlaze SoC with DDR3 RAM, Ethernet, and UART on the Digilent Arty A7-100T, booted into Linux using AMD PetaLinux 2025.1.

---

## Hardware & Software Requirements

* **Board:** [Digilent Arty A7-100T Artix-7 FPGA](https://digilent.com/reference/programmable-logic/arty-a7/reference-manual)
* **EDA Tools:** AMD Vivado 2025.1 & PetaLinux 2025.1
* **Host OS:** Ubuntu Linux (tested on 24.04 LTS)
* **Peripherals:** Micro-USB cable, RJ45 Ethernet cable connected to LAN

---

## 1. Create Vivado Project

Launch Vivado and create a new RTL project.

   ![Create New Project Welcome](assets/create_new_project_00.png)

   ![Create New Project Next](assets/create_new_project_01.png)

   ![Project Name and Location](assets/create_new_project_02.png)

   ![Project RTL](assets/create_new_project_03.png)

   ![Select Arty A7 Board](assets/create_new_project_04.png)

   ![Project Summary](assets/create_new_project_05.png)

---

## 2. Add Board Constraints

Add and link the master XDC constraints file for the Arty A7:

   ![Add Constraints Sources](assets/add_constraints_00.png)

   ![Add Constraints Sources Next](assets/add_constraints_01.png)

   ![Add Constraints Sources Create File](assets/add_constraints_02.png)

   ![Add Constraints Sources OK](assets/add_constraints_03.png)

   ![Add Constraints Sources Finish](assets/add_constraints_04.png)

   [Arty-A7-100 xdc](https://github.com/Digilent/digilent-xdc/blob/master/Arty-A7-100-Master.xdc)

   uncomment eth_ref_clk

   ![Constraints Content](assets/add_constraints_05.png)

---

## 3. Create Block Design & Configure Clocks

Initialize the IP Integrator canvas:

   ![Create Block Design](assets/create_block_design.png)

Add and configure the **Clocking Wizard (`clk_wiz_0`)**:

   Use + and search for clock

   ![Add Clocking Wizard](assets/add_clocking_wizard_00.png)

   Double click `clk_wiz_0`

   ![Configure Clocking Wizard](assets/add_clocking_wizard_01.png)

   Input Clock: 100 MHz system clock.

   ![Clocking Options Input](assets/add_clocking_wizard_02.png)

   Output Clocks:

   `clk_out1`: 100.000 MHz (for MIG `sys_clk_i`)

   `clk_out2`: 200.000 MHz (for MIG `clk_ref_i`)

   `clk_out3`: 25.000 MHz (for external eth_ref_clk)

   Set reset type to Active Low

   ![Configure Output Clocks](assets/add_clocking_wizard_03.png)

---

## 4. Add & Configure Memory Interface Generator (MIG 7-Series)

   Drag DDR3 SDRAM to canvas

   ![MIG 00](assets/add_mig_00.png)

   ![MIG 01](assets/add_mig_01.png)

   Delete clk_ref_i and sys_clk_i

   ![MIG 02](assets/add_mig_02.png)

   Double click on MIG

   ![MIG 03](assets/add_mig_03.png)

   ![MIG 04](assets/add_mig_04.png)

   ![MIG 05](assets/add_mig_05.png)

   ![MIG 06](assets/add_mig_06.png)

   ![MIG 07](assets/add_mig_07.png)

   ![MIG 08](assets/add_mig_08.png)

   ![MIG 09](assets/add_mig_09.png)

   Uncheck Select Additional Clocks

   ![MIG 10](assets/add_mig_10.png)

   Set System Clock to No Buffer

   ![MIG 11](assets/add_mig_11.png)

   ![MIG 12](assets/add_mig_12.png)

   ![MIG 13](assets/add_mig_13.png)

   Validate pin selection

   ![MIG 14](assets/add_mig_14.png)

   ![MIG 15](assets/add_mig_15.png)

   ![MIG 16](assets/add_mig_16.png)

   ![MIG 17](assets/add_mig_17.png)

   ![MIG 18](assets/add_mig_18.png)

   Accept Micron License Agreement

   ![MIG 19](assets/add_mig_19.png)

   ![MIG 20](assets/add_mig_20.png)

   ![MIG 21](assets/add_mig_21.png)

---

## 5. Connect MIG to Clocking Wizard

   Connect `clk_wiz_0/clk_out1` to `mig_7series_0/sys_clk_i` (100MHz)

   ![Connect Clocks 1](assets/connect_clk_wiz_to_mig_00.png)

   Connect `clk_wiz_0/clk_out2` to `mig_7series_0/clk_ref_i` (200MHz)

   ![Connect Clocks 2](assets/connect_clk_wiz_to_mig_01.png)

   Run Connection Automation

   ![Connect Clocks 3](assets/connect_clk_wiz_to_mig_02.png)

---

## 6. Configure `eth_ref_clk`

   Make external `clk_wiz_0/clk_out3` (25MHz)

   ![Make clk_out3 External](assets/make_clk_out3_external.png)

   Rename port to `eth_ref_clk`

   ![Rename to eth_ref_clk](assets/rename_clk_out3_to_eth_ref_clk.png)

---

## 7. MicroBlaze Processor & Peripherals

Add MicroBlaze and run Block Automation:

   Use + and search for micro

   ![Add MicroBlaze IP](assets/add_microblaze_00.png)

   Run Block Automation and Configure MicroBlaze

   ![Run Block Automation](assets/add_microblaze_01.png)

   Run Block Automation again and keep classic microblaze

   ![MicroBlaze Automation Options](assets/add_microblaze_02.png)

   Double click MicroBlaze and Configure

   ![MicroBlaze Core Configuration](assets/add_microblaze_03.png)

   ![Enable MMU and Caches](assets/add_microblaze_04.png)

   ![Review MicroBlaze Layout](assets/add_microblaze_05.png)

   ![Processor Subsystem Ready](assets/add_microblaze_06.png)

Add AXI Peripherals (Ethernet MII, Quad SPI Flash, GPIO leds, USB UART and Timer):

   Drag Peripherals to canvas

   ![Add Peripherals Step 1](assets/add_peripherals_00.png)

   ![Add Peripherals Step 2](assets/add_peripherals_01.png)

   Use + and search for timer

   ![Add AXI Timer](assets/add_timer.png)

Configure Interrupts:

   Double click xlconcat and update number of ports to 4

   ![Set Concat Ports to 4](assets/update_xlconcat_num_ports_to_4.png)

   ![Connect Interrupt ethernetlite](assets/connect_interrupts_00.png)

   ![Connect Interrupt quad_spi](assets/connect_interrupts_01.png)

   ![Connect Interrupt uartlite](assets/connect_interrupts_02.png)

   ![Connect Interrupt timer](assets/connect_interrupts_03.png)

Run Connection Automation to tie all AXI buses:

   Notice ext_spi_clk needs to be the MIG's ui_clk

   ![Run Connection Automation](assets/run_connection_auto.png)

---

## 8. Synthesis, Bitstream & Export Hardware (.xsa)

Create HDL Wrapper:

   ![Create HDL Wrapper 1](assets/create_hdl_wrap_00.png)

   ![Create HDL Wrapper 2](assets/create_hdl_wrap_01.png)

Validate Design:

   ![Validate Design 1](assets/validate_design_00.png)

   ![Validate Design 2](assets/validate_design_01.png)

   ![Validate Design 3](assets/validate_design_02.png)

Generate Bitstream:

   ![Generate Bitstream 1](assets/generate_bitstream_00.png)

   ![Generate Bitstream 2](assets/generate_bitstream_01.png)

   ![Generate Bitstream 3](assets/generate_bitstream_02.png)

   ![Generate Bitstream 4](assets/generate_bitstream_03.png)

Export Hardware Platform with Bitstream (`.xsa`):

   ![Export Hardware 1](assets/export_xsa_00.png)

   ![Export Hardware 2](assets/export_xsa_01.png)

   ![Export Hardware 3](assets/export_xsa_02.png)

   ![Export Hardware 4](assets/export_xsa_03.png)

   ![Export Hardware 5](assets/export_xsa_04.png)

---

## 9. Install Petalinux if needed

Download the installer from AMD `petalinux-v2025.1-*-installer.run`

[Installation instructions from AMD](https://docs.amd.com/r/2025.1-English/ug1144-petalinux-tools-reference-guide/Installation-Requirements)

Worked for me on ubuntu 24.04, in nutshell after you download the installer need to run it and potentially resolve some dependencies issues. Here are the extra steps I did, but highly depends on what your system these steps might need adjustments

```bash
sudo apt install xterm libtool texinfo
sudo ln -sf /usr/lib/x86_64-linux-gnu/libtinfo.so.6 /usr/lib/x86_64-linux-gnu/libtinfo.so.5
sudo ln -sf /usr/lib/x86_64-linux-gnu/libncurses.so.6 /usr/lib/x86_64-linux-gnu/libncurses.so.5
sudo ln -sf /usr/bin/bash /usr/bin/sh
sudo sysctl -w kernel.apparmor_restrict_unprivileged_userns=0
./petalinux-v2025.1-05180714-installer.run
```

Once installation is done all you need to do is to source the settings from the directory where you installed petalinux

![PetaLinux Installation Demo](assets/petalinux_install.gif)

---

## 10. Build image from `.xsa`

```bash
source peta/settings.sh
petalinux-create --type project --template microblaze --name artya7_linux
cd artya7_linux
petalinux-config --get-hw-description=../vivado/soc --silentconfig
sed -i 's/# CONFIG_python3 is not set/CONFIG_python3=y/' project-spec/configs/rootfs_config
petalinux-config -c rootfs --silentconfig
petalinux-build
```

![Build SOC image](assets/soc_image_build.gif)

Notice that without `--silentconfig` it will open menuconfig where you can configure the system as you want.

Omit --silentconfig if you prefer to launch the interactive ncurses menuconfig interface to inspect or modify peripheral, networking, and kernel options manually

---

## 11. Boot Arty A7 with the built image

Open 2 terminals one that connects to FPGA UART with picocom and the other that will load the image on FPGA

To monitor boot and login once boot it done, username is petalinux, on first login you will be requested to change password

```bash
picocom -b 9600 /dev/ttyUSB1
```

To load the image

```bash
source peta/settings.sh
cd artya7_linux
petalinux-boot jtag --kernel
```

![Build SOC image](assets/picocom_boot.gif)

---

## 12. Login to system, check what it can do

SSH from your computer to FPGA

```bash
(env) e271p314@laptop:~$ ssh-copy-id petalinux@192.168.50.211
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/e271p314/.ssh/id_rsa.pub"
The authenticity of host '192.168.50.211 (192.168.50.211)' can't be established.
RSA key fingerprint is SHA256:Xbi5KcxIpXFXgOeSbNureP2kVhM6ShuqyNe5h9PCDEg.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
petalinux@192.168.50.211's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'petalinux@192.168.50.211'"
and check to make sure that only the key(s) you wanted were added.
```

Inspect system basics

```bash
(env) e271p314@laptop:~$ ssh petalinux@192.168.50.211
artya7_linux:~$ uname -a
Linux artya7_linux 6.12.10-xilinx-g0a0f70e531c7 #1 Sat May 17 14:01:06 UTC 2025 microblaze GNU/Linux
artya7_linux:~$ date
Fri Mar  9 12:38:57 UTC 2018
artya7_linux:~$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether 00:0a:35:00:27:41 brd ff:ff:ff:ff:ff:ff
    inet 192.168.50.211/24 brd 192.168.50.255 scope global eth0
       valid_lft forever preferred_lft forever
artya7_linux:~$ python
Python 3.12.9 (main, Feb  4 2025, 14:38:38) [GCC 13.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import random
>>> from datetime import datetime
>>> lst0 = [random.random() for _ in range(1_000)] ; t0 = datetime.now(); lst1 = sorted(lst0); print(f'{datetime.now() - t0} {(lst0 == lst1) = } {(set(lst0) == set(lst1)) = }')
0:00:00.040468 (lst0 == lst1) = False (set(lst0) == set(lst1)) = True
>>> lst0 = [random.random() for _ in range(10_000)] ; t0 = datetime.now(); lst1 = sorted(lst0); print(f'{datetime.now() - t0} {(lst0 == lst1) = } {(set(lst0) == set(lst1)) = }')
0:00:00.537537 (lst0 == lst1) = False (set(lst0) == set(lst1)) = True
>>> lst0 = [random.random() for _ in range(100_000)] ; t0 = datetime.now(); lst1 = sorted(lst0); print(f'{datetime.now() - t0} {(lst0 == lst1) = } {(set(lst0) == set(lst1)) = }')
0:00:07.497517 (lst0 == lst1) = False (set(lst0) == set(lst1)) = True
>>> ^D
artya7_linux:~$ cat /proc/cpuinfo 
CPU-Family:	MicroBlaze
FPGA-Arch:	artix7
CPU-Ver:	11.0, little endian
CPU-MHz:	81.247969
BogoMips:	40.34
HW:
 Shift:		yes
 MSR:		yes
 PCMP:		yes
 DIV:		yes
 MMU:		3
 MUL:		v2
 FPU:		no
 Exc:		op0x0 unal ill iopb dopb zero 
Stream-insns:	privileged
Icache:		64kB	line length:	32B
Dcache:		64kB	line length:	16B
Dcache-Policy:	write-through
HW-Debug:	yes
PVR-USR1:	00
PVR-USR2:	00000000
Page size:	4096
artya7_linux:~$ free -m
              total        used        free      shared  buff/cache   available
Mem:         250136       20340      152384          80       77412      154432
Swap:             0           0           0
artya7_linux:~$ cat /proc/device-tree/model 
Xilinx MicroBlazeartya7_linux:~$ sudo -i

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

For security reasons, the password you type will not be visible.

Password: 
root@artya7_linux:~# cd /home/petalinux/
root@artya7_linux:/home/petalinux# exit
logout
artya7_linux:~$ cat > led_show.sh 
#!/bin/bash

BASE=512
# 4 Green LEDs: LD4, LD5, LD6, LD7
GREEN_LEDS=(512 513 514 515)
# RGB LED channels (LD0 - LD3, R/G/B): 516 to 527
RGB_LEDS=($(seq 516 527))
ALL_LEDS=("${GREEN_LEDS[@]}" "${RGB_LEDS[@]}")

cleanup() {
    echo -e "\nCleaning up GPIOs..."
    for pin in "${ALL_LEDS[@]}"; do
        echo 0 > /sys/class/gpio/gpio${pin}/value 2>/dev/null
        echo ${pin} > /sys/class/gpio/unexport 2>/dev/null
    done
    exit 0
}
trap cleanup SIGINT SIGTERM

echo "Exporting and configuring GPIO pins 512-527..."
for pin in "${ALL_LEDS[@]}"; do
    if [ ! -d "/sys/class/gpio/gpio${pin}" ]; then
        echo ${pin} > /sys/class/gpio/export
    fi
    echo out > /sys/class/gpio/gpio${pin}/direction
    echo 0 > /sys/class/gpio/gpio${pin}/value
done

echo "Running light show on Arty A7! Press Ctrl+C to stop."

while true; do
    # 1. Chaser on 4 Green LEDs
    for pin in "${GREEN_LEDS[@]}"; do
        echo 1 > /sys/class/gpio/gpio${pin}/value
        sleep 0.1
        echo 0 > /sys/class/gpio/gpio${pin}/value
    done

    # 2. Flash RGB LEDs sequentially
    for pin in "${RGB_LEDS[@]}"; do
        echo 1 > /sys/class/gpio/gpio${pin}/value
        sleep 0.05
        echo 0 > /sys/class/gpio/gpio${pin}/value
    done
done
artya7_linux:~$ sudo bash led_show.sh 
Exporting and configuring GPIO pins 512-527...
Running light show on Arty A7! Press Ctrl+C to stop.
^C
Cleaning up GPIOs...
artya7_linux:~$ 
```

![LEDS](assets/artya7_leds.gif)

Write hello world and build with cross compiler

```bash
(env) e271p314@laptop:~$ cat > hello_world.c
#include <stdio.h>
#include <unistd.h>

int main(void) {
    printf("========================================\n");
    printf(" Hello World from MicroBlaze on Arty A7!\n");
    printf(" Running Linux kernel on FPGA soft core \n");
    printf("========================================\n");
    return 0;
}
(env) e271p314@laptop:~$ /tools/Xilinx/2025.1.1/gnu/microblaze/linux_toolchain/lin64_le/bin/microblazeel-xilinx-linux-gnu-gcc hello_world.c -o hello_world
(env) e271p314@laptop:~$ scp hello_world petalinux@192.168.50.211:
hello_world                                                                                                                                                                                       100% 9728   376.2KB/s   00:00    
(env) e271p314@laptop:~$ 
```

Copy the binary output to FPGA and run it

```bash
artya7_linux:~$ ls -l                                    
-rwxr-xr-x    1 petalinu petalinu      9728 Mar  9 13:30 hello_world
-rw-r--r--    1 petalinu petalinu      1240 Mar  9 12:57 led_show.sh
artya7_linux:~$ ./hello_world 
========================================
 Hello World from MicroBlaze on Arty A7!
 Running Linux kernel on FPGA soft core 
========================================
```

---

## 13. References

* [Artix-7 Arty Base Project](https://www.fpgadeveloper.com/2017/11/artix-7-arty-base-project/)
* [PetaLinux for Artix-7 Arty Base Project](https://www.fpgadeveloper.com/2017/11/petalinux-for-artix-7-arty-base-project/)
* [MicroBlaze-DDR3-tutorial](https://github.com/viktor-nikolov/MicroBlaze-DDR3-tutorial/blob/main/README.md)