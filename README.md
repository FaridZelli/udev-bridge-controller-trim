# udev-bridge-controller-trim
This is a tried and true list of bridge controllers with [TRIM](https://www.seagate.com/gb/en/blog/what-are-ssd-trim-and-garbage-collection/) support, made available as a udev rule file.

## 💻 Usage
First, check whether TRIM is already available for your storage device:
```
$ lsblk --discard

NAME                                          DISC-ALN DISC-GRAN DISC-MAX DISC-ZERO
sda                                                  0      512B       2G         0
└─sda1                                               0      512B       2G         0
sdb                                                  0        0B       0B         0
└─sdb1                                               0        0B       0B         0
zram0                                                0        4K       2T         0
```
- If your device has non-zero values (such as **sda** in the example), try running TRIM with the `fstrim` utility as root.
```
$ fstrim -v /mnt/drive_mount_point
```

- If not, find your enclosure's bridge controller ID using `lsusb`.
```
$ lsusb

Bus 002 Device 004: ID 174c:1153 ASMedia Technology Inc. ASM1153 SATA 3Gb/s bridge
```

Check [10-bridge-controller-trim.rules](/10-bridge-controller-trim.rules) for the ID. If present, copy the file to `/etc/udev/rules.d/` and reboot.

> [!NOTE]
> For LUKS encrypted drives, you must also enable discard at the dm-crypt layer as follows:  
> `sudo cryptsetup --allow-discards --persistent refresh <mapping_name>`
>   
> Or alternatively, add the `discard` flag in `/etc/crypttab` and regenerate your initramfs.  
> This can also be done using a GUI partition manager such as **GNOME Disks**.  

## 📝 Contributing

If your bridge controller is **not** listed but supports UASP + TRIM, please test it before creating an issue or a pull request.
