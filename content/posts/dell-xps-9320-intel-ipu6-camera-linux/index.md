---
title: "Dell XPS-9320 Intel IPU6 Camera Driver on Linux"
date: 2025-05-02T23:29:44+00:00
authors: ["kn"]
tags: ["linux", "dell", "xps", "archlinux", "kernel", "kernel module"]
keywords: ["linux", "dell", "xps", "archlinux"]
description: "How to make intel ipu6 camera work with linux on dell xps 9320"
showFullContent: false
cover: "posts/dell-xps-9320-intel-ipu6-camera-linux/cover.png"
---

After 5 days of seeking solution for making Intel IPU6 camera on Dell xps 9320 to work on recent kernel module (without using OEM Ubuntu driver - ofc not running on Ubuntu), I finally found a way to make it works (though the image quality is not very good), here is how I debug and the instruction

## Getting IPU6 Camera Working on Linux (OVTI01A0 sensor)

### Check if you have the right camera sensor:

```bash
sudo dmesg | grep -i ipu6
```

Example output:
```
[    4.254150] intel-ipu6 0000:00:05.0: enabling device (0000 -> 0002)
[   15.476130] pci 0000:00:05.0: deferred probe pending: intel-ipu6: IPU6 bridge init failed
[   17.186083] intel-ipu6 0000:00:05.0: Sending BOOT_LOAD to CSE
[   17.204162] intel-ipu6 0000:00:05.0: Sending AUTHENTICATE_RUN to CSE
[   17.274647] intel-ipu6 0000:00:05.0: CSE authenticate_run done
[   17.274671] intel-ipu6 0000:00:05.0: IPU6-v3[a75d] hardware version 5
[   17.347317] intel_ipu6_psys.psys intel_ipu6.psys.40: pkg_dir entry count:8
[   17.347676] intel_ipu6_psys.psys intel_ipu6.psys.40: psys probe minor: 0
```

So the ID for the camera is:
`0000:00:05.0`

### Check the sensor type:

```bash
sudo dmesg | grep "pci 0000:00:05.0"
```

Look for this line:
```
[   15.915105] intel-ipu6 0000:00:05.0: Found supported sensor OVTI01A0:00
```

The sensor is `OVTI01A0`.

### Driver Status Check:

This sensor is not included in the standard kernel yet.

Check installed drivers:

```bash
yay -Ql linux-zen | grep "/drivers/media/i2c/ov"
```

Intel repo has the driver:
`ov2c10.c`  
https://github.com/intel/ipu6-drivers/tree/master/drivers/media/i2c

### Install the AUR driver:

```bash
yay -S intel-ipu6-dkms-git
```

Flush old cache to ensure latest version:

```bash
rm -rf ~/.cache/yay/intel-ipu6-dkms-git/
```

Then check the module:

```bash
ls -l /var/lib/dkms/ipu6-drivers/r2*/6.*/x86_64/module/
```

### Load the driver:

```bash
sudo modprobe -v ov02c1
```

### Test the camera:

```bash
cam -l
```

Example output:
```
Available cameras:
[0:13:03.010443621] [4961] ERROR IPAModule ipa_module.cpp:171 Symbol ipaModuleInfo not found
[0:13:03.010461160] [4961] ERROR IPAModule ipa_module.cpp:291 v4l2-compat.so: IPA module has no valid info
[0:13:03.010477659] [4961]  INFO Camera camera_manager.cpp:326 libcamera v0.5.0
[0:13:03.018153510] [4962]  WARN CameraSensorProperties camera_sensor_properties.cpp:463 No static properties available for 'ov01a10'
[0:13:03.018171368] [4962]  WARN CameraSensorProperties camera_sensor_properties.cpp:465 Please consider updating the camera sensor properties database
[0:13:03.018179990] [4962]  WARN CameraSensor camera_sensor_legacy.cpp:501 'ov01a10 15-0036': No sensor delays found in static properties. Assuming unverified defaults.
[0:13:03.019108094] [4962]  WARN IPAProxy ipa_proxy.cpp:177 Configuration file 'ov01a10.yaml' not found for IPA module 'simple', falling back to 'uncalibrated.yaml'
[0:13:03.019128813] [4962]  WARN IPASoft soft_simple.cpp:103 IPASoft: Failed to create camera sensor helper for ov01a10
Available cameras:
1: Internal front camera (\_SB_.PC00.LNK1)
```

Some IPA-related errors may appear — they are usually safe to ignore if the camera shows up.

### Firefox Fix:

In `about:config` set:
```
media.webrtc.camera.allow-pipewire = true
```

Restart Firefox.

### Chrome Fix:

In `chrome://flags`, enable:
```
#enable-webrtc-pipewire-camera
```

Then test webcam — should show an image.


### Note for electron app like discord, slack:

enable the flag
`--enable-features=WebRTCPipeWireCapturer`

### Performance:

- Firefox: 19 FPS
- Chrome: 27 FPS

Camera is working successfully!