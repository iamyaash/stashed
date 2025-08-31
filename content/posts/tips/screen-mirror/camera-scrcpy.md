---
date: '2025-08-22T15:16:36+05:30'
draft: false
title: 'Turn Your Phone Camera Into A PC Webcam'
summary: "If your PC or laptop camera isn't great, using phone camera as a webcam is a strong alternative. With scrcpy, you can use your phone's camera as input device for any application."
tags:
- scrcpy
- utils
params:
    author: "Yashwanth Rathakrishnan"
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowToc: true
    TocOpen: true
    ShowBreadCrumbs: true
cover:
    image: posts/tips/screen-mirror/img/scrcpy.png
    alt: Connection Lost
---
# Prerequisites
- Install 
    - `scrcpy`
    - `v4l2loopback`
    - `modprobe`
- Enable _"Developer Settings"_ -> _"USB Debugging"_ on your phone 

---

## Run scrcpy (follow along!)
### Check the available cameras
```sh
scrcpy --list-cameras
```
_The output would list the cameras available on your android phone_
```sh
INFO: ADB device found:
INFO:     --> (tcpip)  192.168.xx.xx:8888             device  <sensitive-info>
/usr/local/bin/scrcpy-server: 1 file pushed, 0 skipped. 80.7 MB/s (90888 bytes in 0.001s)
[server] INFO: Device: [DevName] DevName <sensitive-info> (Android 14)
[server] INFO: List of cameras:
    --camera-id=0    (back, 4208x3120, fps=[14, 15, 30])
    --camera-id=1    (front, 3264x2448, fps=[15, 30])
    --camera-id=2    (back, 4208x3120, fps=[14, 15, 30])
    --camera-id=3    (front, 3264x2448, fps=[15, 30])
```

2. Run this command after **listing the available cameras**  to stream only the phone camera
    ```sh
    # opens the rear facing camera by default
    scrcpy --video-source=camera
    # opens the front facing camera
    scrcpy --video-source=camera --camera-facing=front
    ```
- Use `--camera-id=<NUM>` to select specific camera on your Android phone.
    ```sh
    scrcpy --video-source=camera --camera-id=1
    ```
- Adjust camera resolution & framerate , using:
    - `--camera-size=1920x1080`
    - `--camera-fps=30`
    ```sh
    scrcpy --video-source=camera --camera-id=1 --camera-size=1920x1080 --camera-fps=30
    ```
    > Use `--list-camera-sizes` to check the supported resolutions of your phone.

---

##  **Optional + Linux Only**:  Use `v4l2loopback` to make the camera available as a webcam device
- The `v4l2loopback` module & it creates a **virtual video device** on your Linux system.
- The below command **loads the v4l2loopback module into the Linux kernel**, creating the virtual webcam device, as `/dev/videoN`.
    ```sh
    sudo modprobe v4l2loopback exclusive_caps=1
    ```
    > `exclusive_caps=1`, improves compatibility (_recommended_)
- This last command captures your phone's camera feed only while ignoring the rest of the phone screen.
    ```sh
    scrcpy --video-source=camera --v4l2-sink=/dev/videoN --no-display
    ```
    - `--v4l2-sink=/dev/videoN`: Routes the video stream into the virtual video device.
    - `--no-playback`: disable the video playback, but a window pop-ups anyway.



## Optimal Usage
```sh
scrcpy \
> --video-source=camera \
> --video-codec=h264 \
> --v4l2-sink=/dev/video0 \
> --camera-facing=front \
> --no-playback \
```
- `--video-source=camera`: Select the source as camera.
- `--video-codec=h264`: Uses the `H264` encoder.
- `camera-facing=front`: Uses the front facing camera.c
- `--v4l2-sink=/dev/video0`: Sends the camera feed into the virtual device.
- `--no-playback`: Disable the video playback, but a window pop-ups anyway.
