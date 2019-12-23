# What is this?
A quick run down of how to build and install a headless Synergy server setup on Armbian. Tested with Buster Linux 5.3.9-sunxi on OrangePiZero (https://dl.armbian.com/orangepizero/Buster_current).

# Why?
For someone with a bunch of retro machines that *do not* have a Synergy server port (and may not be near a machine that does or near one that I'd rather not run a Synergy server on) but *do have* a Synergy client port, using a headless Synergy server on a tiny SBC like the OrangePiZero makes for a slick, no frills setup to share a single mouse and keyboard among them.

##### How do I use this?
I've currently got it connect across my Amiga 1000, Pegasos II running AmigaOS 4.1 FE and Pegasos II running MorphOS.

##### A bit more info
For headless Synergy, we can't use xvfb (a virtual framebuffer) since it doesn't take input from physical devices.
We can however use Xorg with a dummy device, which is similar to xvfb, but has the benefit of being able to use physical input devices.
The below steps will go through the setup process and enabling a persistent Synergy server from the moment the system boots up.

# Setup steps
1- apt update
2- apt upgrade
3- Compile synergy (see below)
4- Copy bins to /usr/bin/
5- apt install xorg xserver-xorg-video-dummy
6- Change hostname to beautify synergy config definition. Edit /etc/hostname accordingly
7- Create Synergy config (synergy is the screen name of the server or don't change hostname and add the hostname as an alias to synergy) and copy it to /etc/synergy/synergy.conf
8- Run xorg and Synergy server: X -config <path_to_your_dummy_config> && synergys -c <your_synergy_config> -f

# Start headless Synergy on boot
1- Copy xorg_dummy.config to /etc/X11/xorg.conf.d/ or whatever your distro uses
2- Copy x_dummy.service and synergy.service service files to /etc/systemd/system/ or wherever your systemd config resides - chown both to root:root
3- Reload systemd: systemctl daemon-reload
4- Start X dummy: systemctl start x_dummy
5- Check its status: systemctl status x_dummy
6- Start synergy server: systemctl start synergy
7- Check its status: systemctl status synergy
8- Enable both services to start persistently: systemctl enable x_dummy.service; systemctl enable synergy.service
9- Reboot

# To build Synergy
```
git clone https://github.com/symless/synergy-core.git
cd synergy-core/
sudo apt -y install qtcreator qtbase5-dev cmake make g++ xorg-dev libssl-dev libx11-dev libsodium-dev libgl1-mesa-glx libegl1-mesa libcurl4-openssl-dev libavahi-compat-libdnssd-dev qtdeclarative5-dev libqt5svg5-dev libsystemd-dev
mkdir build
cd build
cmake ..
make
```

Once built, copy build/bin/ to /usr/bin/
