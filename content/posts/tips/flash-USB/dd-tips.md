---
date: '2025-08-31T11:23:37+05:30'
draft: false
title: 'dd: Create Bootable USB'
summary: "Quick guide on how to use dd command to create bootable USB. It comes installed by default in all distributions, so you don't have to worry about download a tool for that."
tags:
- dd
- utils

params:
    author: "Yashwanth Rathakrishnan"
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowToc: true
    TocOpen: true
    ShowBreadCrumbs: true

---
# Identify Your USB Device
```sh
sudo lsblk
#or
sudo fdisk -l
```
```sh
#output from fdisk -l
Disk /dev/sda: 28.65 GiB, 30765219840 bytes, 60088320 sectors
Device     Boot Start      End  Sectors  Size Id Type
/dev/sda1        2048 60088286 60086239 28.7G 83 Linux
```
- Your device will likely be something like `/dev/sd*`. (_copy `dev/sda` only, not `/dev/sda1`_)

### Unmount USB (Depends)
- Unmount the partition in case you have mounted it:
    ```sh
    sudo umount /dev/sda1
    ```

## Format USB (Optional)
- This step is an optional step, mainly because `dd` command writes the ISO bit-for-bit, completely overwriting any existing partitions or filysystems on the USB stick. 
- I'm formatting the USB device in `ext4` format:
    ```sh
    sudo mkfs.ext4 /dev/sda
    ```

# Write ISO to USB using dd
- Locate the `*.iso` file you want to flash
```sh
sudo dd \
> if=/home/$USER/debian-12.11.0-amd64-netinst.iso \
> of=/dev/sda1 \
> bs=4M \
> status=progress \
> oflag=sync
```
- `if=`: Input File | Path to your ISO
- `of=`: Output Path | USB device `/dev/sda` (_not the partition `/dev/sda1`_)
- `bs=4M`: Sets block size for faster copying | Uses 4MB for each operation
- `status=progress`: Shows progress bar
- `oflag=sync`: Ensures data is physically written to the USB before finishing.