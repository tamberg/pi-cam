# Pi Cam
A Raspberry Pi based time lapse camera, initiated by Hsinyi Lin.

> Note: We assume your computer runs MacOS.

![pi-cam](https://github.com/user-attachments/assets/3493b081-e213-402c-ba55-5b4b4fe3a4fe)

## Take pictures
- Plug the power cable into the Pi and a socket
- Wait for the Pi to start up (can take a few minutes)
- Wait for the camera to take a picture (every 1 minute)

## Browse pictures
On your computer.

> Note: Your computer has to be in the same Wi-Fi network.

- Open http://raspberrypi.local:8080/
- Press F5 or refresh the page
- Browse the directories

## Download pictures
On your computer.

> Note: Your computer has to be in the same Wi-Fi network.

- Open the _Terminal_ app
- Install the _brew_ tool
    ```bash
    $ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```
- Install the _wget_ tool
    ```bash
    $ brew install wget
    ```
- Create a directory
    ```bash
    $ mkdir ~/Desktop/pi-cam
    ```
- Change directory
    ```bash
    $ cd ~/Desktop/pi-cam
    ```
- Sync picture files
    ```bash
    $ wget --mirror http://raspberrypi.local:8080/
    ```
- Open in _Finder_
    ```bash
    $ open ~/Desktop/pi-cam
    ```

## Log into the Pi
On your computer, optional.

> Note: Your computer has to be in the same Wi-Fi network.

- Open the _Terminal_ app
- Connect to the Pi with _ssh_
    ```bash
    $ ssh pi@raspberrypi.local
    ```

## Work on the Pi
On the Pi, optional.

- List files on the disk with _tree_
    ```bash
    $ tree /mnt/elements
    ```
- Delete all files on the disk with _rm_
    ```bash
    $ rm -rf /mnt/elements/*
    ```
- See text file content with _cat_
    ```bash
    $ cat photo.sh
    ```  
- Change a text file with _nano_
    ```bash
    $ nano my.txt
    ```
    Save with _CTRL-X-Y ENTER_

# Make your own
## Get the hardware
- [Raspberry Pi 3 B+](https://www.pi-shop.ch/raspberry-pi-3-model-b) (CHF 34)
- [Kingston SD card 16 GB](https://www.pi-shop.ch/kingston-microsdhc-karte-industrial-uhs-i-16-gb) (CHF 23)
- [ArduCam 64 MP Autofocus](https://www.pi-shop.ch/arducam-1-1-32-64mp-auto-focus-camera-module-for-raspberry-pi) (CHF 56)
- [Samsung SSD Disk 1000 GB](https://www.digitec.ch/en/s1/product/samsung-portable-t7-red-1000-gb-external-ssd-13199901) (CHF 137)
- [Micro USB power adapter](https://www.pi-shop.ch/raspberry-pi-12-5w-micro-usb-power-supply-2255) (CHF 12)

## Set up the SD card
On your computer.

- Install _Pi Imager_ from https://www.raspberrypi.com/software/
- Plug in the SD card
- Select device _Pi 3_
- Select _Pi OS (other)_ > _Pi OS Lite (64-bit)_
- Select SD card storage (compare size)
- Next > Edit
    - Wi-Fi credentials, e.g. _MY_SSID_, _MY_PASSWORD_
    - Computer name _raspberrypi_
    - User _pi_
    - Password, e.g. _PI_PASSWORD_
    - Enable SSH
    - ...
- Wait for the SD card to be written
- Plug the SD card into the Pi
- Try to [log into the Pi](#log-into-the-pi)

## Set up the disk
On the Pi, as suggested by [@cspindler](https://github.com/cspindler).

- Plug in the disk via USB
- Install BTRFS file system tools
    ```bash
    $ sudo apt install -y btrfs-progs
    ```
- Format the disk and label it as _capture_
    ```bash
    $ sudo mkfs.btrfs -L capture /dev/sda1
    ```
- Add a mount point to the operating system
    ```bash
    $ echo '/dev/disk/by-label/capture /mnt/elements btrfs  defaults,auto,user  0 0' \
    | sudo tee --append /etc/fstab
    ```
- Mount the disk and change its owner to _pi_
    ```bash
    $ mount /mnt/elements
    $ sudo chown pi /mnt/elements
    ```
- Install the _tree_ command to list files
    ```bash
    $ sudo apt install tree
    $ tree /mnt/elements
    ```

## Set up the Web UI service
On the Pi.
- Create _pi-cam-webui.service_
    ```bash
    $ sudo nano /lib/systemd/system/pi-cam-webui.service
    ```
    Paste this
    ```
    [Unit]
    Description=Pi Cam Web UI service
    Requires=mnt-elements.mount
    After=mnt-elements.mount
    
    [Service]
    User=pi
    WorkingDirectory=/mnt/elements
    ExecStart=/usr/bin/python -m http.server 8080
    Restart=on-failure
    
    [Install]
    WantedBy=multi-user.target
    ```
    Save with _CTRL-X-Y ENTER_
- Enable the service
    ```bash
    $ sudo systemctl enable pi-cam-webui.service
    ```
- Start the service
    ```bash
    $ sudo systemctl start pi-cam-webui.service
    ```

## Set up the camera
On the Pi, based on [this guide](https://docs.arducam.com/Raspberry-Pi-Camera/Native-camera/64MP-Hawkeye/#software-guide), debugged with [@Fede85](https://github.com/Fede85).

- Connect the camera to the Pi
- Download _install_pivariety_pkgs.sh_
    ```bash
    $ wget -O install_pivariety_pkgs.sh https://github.com/ArduCAM/Arducam-Pivariety-V4L2-Driver/releases/download/install_script/install_pivariety_pkgs.sh
    ```
- Make it executable
    ```bash
    $ chmod +x install_pivariety_pkgs.sh
    ```
- Install _libcamera_dev_
    ```bash
    $ ./install_pivariety_pkgs.sh -p libcamera_dev
    ```
- Install _libcamera_apps_
    ```bash
    $ ./install_pivariety_pkgs.sh -p libcamera_apps
    ```
- List cameras
    ```bash
    $ libcamera-hello --list-cameras
    ```
    e.g.
    ```
    Available cameras
    -----------------
    0 : arducam_64mp [9248x6944 10-bit RGGB] (/base/soc/i2c0mux/i2c@1/arducam_64mp@1a)
        Modes: 'SRGGB10_CSI2P' : 1280x720 [120.09 fps - (2064, 2032)/5120x2880 crop]
            1920x1080 [60.04 fps - (784, 1312)/7680x4320 crop]
            2312x1736 [30.00 fps - (0, 0)/9248x6944 crop]
            3840x2160 [20.00 fps - (784, 1312)/7680x4320 crop]
            4624x3472 [10.00 fps - (0, 0)/9248x6944 crop]
            8000x6000 [3.00 fps - (624, 472)/9248x6944 crop]
            9152x6944 [2.70 fps - (0, 0)/9248x6944 crop]
    ```
- Create _photo.sh_
    ```bash
    $ nano ~/photo.sh
    ```
    Paste this
    ```
    #!/bin/bash
    libcamera-jpeg --width 4624 --height 3472 -o /mnt/elements/"`date +"%Y-%m-%d"`"/image-"`date +"%Y-%m-%dT%H-%M-%SZ"`".jpg
    ```
    Save with _CTRL-X-Y ENTER_
- Try to take a picture
    ```bash
    $ ./photo.sh
    ```
- [Browse pictures](#browse-pictures)

## Set up the photo service
On the Pi.

- Create _pi-cam-photo.service_
    ```bash
    $ sudo nano /lib/systemd/system/pi-cam-photo.service
    ```
    Paste this
    ```
    [Unit]
    Description=Pi cam photo service
    Requires=mnt-elements.mount
    After=mnt-elements.mount
    
    [Service]
    User=pi
    WorkingDirectory=/home/pi
    ExecStart=/usr/bin/bash photo.sh
    ```
    Save with _CTRL-X-Y ENTER_
- Create _pi-cam-photo.timer_
    ```bash
    $ sudo nano /lib/systemd/system/pi-cam-photo.timer
    ```
    Paste this
    ```
    [Unit]
    Description=Pi cam photo timer
    
    [Timer]
    OnBootSec=0min
    OnCalendar=*:0/1
    Unit=pi-cam-photo.service
    
    [Install]
    WantedBy=multi-user.target
    ```
    Save with _CTRL-X-Y ENTER_
    
- Configure the timer, e.g. every 1 minute
    ```
    OnCalendar=*:0/1
    ```
- Enable the service and timer
    ```bash
    $ sudo systemctl enable pi-cam-photo.service
    $ sudo systemctl enable pi-cam-photo.timer
    ```
- Start the timer
    ```bash
    $ sudo systemctl start pi-cam-photo.timer
    ```

## Errors
- On `zsh: command not found: $` try to remove the leading `$` character when copying the above commands.

## More
- https://www.raspberrypi.com/documentation/computers/getting-started.html
- https://www.raspberrypi.com/documentation/computers/camera_software.html
- https://docs.arducam.com/Raspberry-Pi-Camera/Native-camera/64MP-Hawkeye/
