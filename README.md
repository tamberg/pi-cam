# Pi Cam
A Raspberry Pi based time lapse camera, work in progress.

## See pictures
- Plug in the power
- Wait for the Pi to start up
- Wait for the camera to start taking pictures
- Open http://raspberrypi.local:8080 to see them (F5 to refresh)

> Note: Your computer has to be in the same Wi-Fi network

## Hardware
- [Raspberry Pi 3 B+](https://www.pi-shop.ch/raspberry-pi-3-model-b)
- [Kingston SD card 16 GB](https://www.pi-shop.ch/kingston-microsdhc-karte-industrial-uhs-i-16-gb)
- [ArduCam 64 MP Autofocus](https://www.pi-shop.ch/arducam-1-1-32-64mp-auto-focus-camera-module-for-raspberry-pi)
- [Samsung SSD Disk 500 GB](https://www.digitec.ch/en/s1/product/samsung-portable-t7-red-1000-gb-external-ssd-13199901)
- [Micro USB power adapter](https://www.pi-shop.ch/raspberry-pi-12-5w-micro-usb-power-supply-2255)

## Set up disk
- Plug in SSD Disk via USB
- Format the disk and label it as "capture"
    ```$ sudo apt install -y btrfs-progs
    $ sudo mkfs.btrfs -L capture /dev/sda1```

- Then add a mount point to the operating system
    ```$ echo '/dev/disk/by-label/capture /mnt/elements btrfs  defaults,auto,user  0 0' \
 | sudo tee --append /etc/fstab```

- Then mount the disk (and set user pi)

    ```$ mount /mnt/elements
$ sudo chown pi /mnt/elements```

## Optional
- Install the tree command to list files
    ```$ sudo apt-get install tree```
