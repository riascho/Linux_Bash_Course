# Troubleshooting

In order to fix the linux on a computer, we need to boot up another linux from a USB drive

1. download a 'live linux' system (usually an `.iso` image)

> a **'live linux distribution'** is booted from a USB or DVD and saves no information on a disk

2. copy it using the `dd` command to a USB drive (make sure it's bootable!)
3. Be aware that all contents of the USB drive will be overwritten!
4. We can then boot from this external drive -> see here for more help: https://ubuntu.com/tutorials/install-ubuntu-desktop#4-boot-from-usb-flash-drive
5. the hard drive will be mounted automatically
6. open the volume and select `Open in Terminal`
7. with this path we can change the root

```bash
# sets root ('/') to given path
sudo chroot .
```

8. now we can access the actual hard drive linux system

```bash
# e.g. edit the /etc/default/grub file for the bootloader

GRUB_TIMEOUT_STYLE=hidden # uncomment this one to show bootloader
GRUB_TIMEOUT=5 # make sure the bootloader is shown

# refresh
update-grub
```

9. bind the `/dev` files from the USB boot to the hard drive

```bash
mount --bind /dev <hard drive path from USB stick>/dev

mount --bind /proc <hard drive path from USB stick>/prov

mount --bind /sys <hard drive path from USB stick>/sys

# again
update-grub
```

10. change the default boot system in `/etc/default/grub`

```bash
# see the menuentries in /boot/grub/grub.cfg for details

GRUB_DEFAULT="1>Ubuntu, with Linux 6.17.0-35-generic"
```

11. Restart computer

12. kernel safeguards

- We should prevent any cases the 'alternative' kernel that we're using gets removed, such as through an `sudo apt autoremove`.
- we can find the kernel file in `/boot/` and lock them (so they don't get deleted)
- we need to find out which packages create these images

```bash
dpkg -S <file_name>
```

- once we know the package, we can manually instsall it (even though it's already installed)

```bash
sudo apt-get install <package_name>
```

- this marks the package (set to manually installed) and it won't be affected by `autoremove`

13. alternatively, we could also remove the broken linux version package (get the package name with `dpkg -S`)

```bash
sudo apt remove <package-name>
```
