### Install to a micro SD card

Replace **sdX** in the following instructions with the device name for the SD card as it appears on your computer.

   1. Zero the beginning of the SD card:
       ```
       dd if=/dev/zero of=/dev/sdX bs=1M count=32
       ```

   2. Start fdisk to partition the SD card:
       ```
       fdisk /dev/sdX
       ```

   3. At the fdisk prompt, create the new partition:
       <ol type="a">
         <li>Type <b>o</b>. This will clear out any partitions on the drive.</li>
         <li>Type <b>p</b> to list partitions. There should be no partitions left.</li>
         <li>Type <b>n</b>, then <b>p</b> for primary, <b>1</b> for the first partition on the drive, <b>32768</b> for the first sector, and then press ENTER to accept the default last sector.</li>
         <li>Write the partition table and exit by typing <b>w</b>.</li>
       </ol>

   4. Create the ext4 filesystem:
       ```
       mkfs.ext4 /dev/sdX1
       ```

   5. Mount the filesystem:
       ```
       mkdir root
       mount /dev/sdX1 root
       ```

   6. Download and extract the root filesystem (as root, not via sudo):
       ```
       wget http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz
       bsdtar -xpf ArchLinuxARM-aarch64-latest.tar.gz -C root
       ```

   7. Download and extract the u-boot binaries (as root, not via sudo):
       ```
       wget https://github.com/inindev/archlinuxarm/raw/release/uboot-nanopi-r4s-2021.04-1-aarch64.pkg.tar.xz
       bsdtar -xf uboot-nanopi-r4s-2021.04-1-aarch64.pkg.tar.xz -C root
       ```

   8. Download and install the Nanopi R4s device tree binary:
       ```
       wget https://github.com/inindev/archlinuxarm/raw/release/rk3399-nanopi-r4s.dtb
       install -m 755 rk3399-nanopi-r4s.dtb root/boot/dtbs/rockchip
       ```

   9. Install the U-Boot bootloader:
       ```
       dd if=root/boot/rksd_loader.img of=/dev/sdX seek=64 conv=notrunc
       dd if=root/boot/u-boot.itb of=/dev/sdX seek=16384 conv=notrunc
       ```

   10. Unmount the partition:
       ```
       umount root
       ```

   11. Insert the micro SD card into the Nanopi R4s, connect ethernet, and apply 5V power.

   12. Use the serial console or SSH to the IP address given to the board by your router.
       - Login as the default user _alarm_ with the password _alarm_.
       - The default root password is _root_.

   13. Initialize the pacman keyring and populate the Arch Linux ARM [package signing](https://archlinuxarm.org/about/package-signing) keys:
       ```
       pacman-key --init
       pacman-key --populate archlinuxarm
       ```

   14. Sync the pacman database and update.
       ```
       pacman -Syyu
       ```
