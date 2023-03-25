---
title: "Auto-Switching your Bluetooth headset using SystemD"
seoTitle: "Set Bluetooth headset as default on Linux"
seoDescription: "This article shows you how to change the output as soon as you connect the Bluetooth headset to your Linux system"
datePublished: Sat Jun 04 2022 18:00:36 GMT+0000 (Coordinated Universal Time)
cuid: cl406l2r801ip34nvfs0n19dm
slug: auto-switching-your-bluetooth-headset-using-systemd
tags: linux, linux-basics

---

I've been struggling with this for quite some time, but today I wanted to fix this somehow.

I use Pipewire which has a `pipewire-pulse` package on Archlinux that interfaces with PulseAudio applications.

However, I had to manually switch the `sink` to make my Bluetooth device the default.

Usually, one can do 
```
pactl list sinks short
```
Then 
```
pactl set-default-sink <sink-name>
```

This will set it, which is what I do via the system tray option. But I wanted an automated solution.

At first, I tried to use `udev` but I couldn't figure it out, but `SystemD` made it easy.

So I created a file under `/home/leon/.config/systemd/user/switch_bt.service` with the following content
```
[Unit]
Description=Switch sink to the Bluetooth device
After=sys-subsystem-bluetooth-devices-hci0:256.device
After=sys-devices-pci0000:00-0000:00:14.0-usb1-1\x2d7-1\x2d7:1.0-bluetooth-hci0-hci0:256.device
BindsTo=sys-subsystem-bluetooth-devices-hci0:256.device
BindsTo=sys-devices-pci0000:00-0000:00:14.0-usb1-1\x2d7-1\x2d7:1.0-bluetooth-hci0-hci0:256.device

[Service]
ExecStartPre=/bin/sleep 10
Type=oneshot
ExecStart=/home/leon/.config/switch_bt.sh

[Install]
WantedBy=sys-subsystem-bluetooth-devices-hci0:256.device
WantedBy=sys-devices-pci0000:00-0000:00:14.0-usb1-1\x2d7-1\x2d7:1.0-bluetooth-hci0-hci0:256.device
```
To find the PCI device you can do `systemctl --all -t device`, you can search for the `hci `service.

Then I simply `enabled` the `systemctl --user` service.
```
$ systemctl --user daemon-reload
$ systemctl --user enable switch_bt.service
$ systemctl --user switch_bt.service
```

The contents of `switch_bt.sh` is 
```
#!/bin/sh

pactl list sinks short
pactl set-default-sink bluez_output.EB_06_EF_5E_E6_77.a2dp-sink

/usr/bin/notify-send 'Changing Default Sink' --expire-time=4000
```

So whenever I connect the Bluetooth device it waits for some time and then auto switches the sink to my Bluetooth device.