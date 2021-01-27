---
layout: post
title:  "Arch Linux Configurations"
categories: configurations
---

# Operations

## Kernel Parameters

### GRUB

- Press `e` when the menu shows up and add them on the `linux` line:

- To make the change persistent after reboot, you *could* manually edit `/boot/grub/grub.cfg` with the exact line from above, but the best practice is to:

Edit `/etc/default/grub` and append your kernel options between the quotes in the `GRUB_CMDLINE_LINUX_DEFAULT` line:

```
GRUB_CMDLINE_LINUX_DEFAULT=""
```

And then automatically re-generate the `grub.cfg` file with:

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```



For more information on configuring GRUB, see the [GRUB](https://wiki.archlinux.org/index.php/GRUB) article.

# Configurations

## Beautify

> https://zhuanlan.zhihu.com/p/100656626

```bash
sudo pacman -S papirus-icon-theme # icon theme
```

### alacritty

```bash
# alacritty terminal
sudo pacman -S alacritty
```

`~/.config/alacritty/alacritty.yml`

```yaml
# configure alacritty
window:
  position:
    x: 0
    y: 0
  padding:
    x: 10
    y: 0
# Font configuration (changes require restart)
font:
  # The size to use.
  size: 12
  # The normal (roman) font face to use.
  normal:
    family: monospace
    # Style can be specified to pick a specific face.
    style: Regular

  # The bold font face
  bold:
    family: monospace
    # Style can be specified to pick a specific face.
    # style: Bold

  # The italic font face
  italic:
    family: monospace # Style can be specified to pick a specific face.  # style: Italic # Colors (One Dark) colors: # Default colors primary:
    background: '#282c34'
    foreground: '#abb2bf'

  # Normal colors
  normal:
    # NOTE: Use '#131613' for the `black` color if you'd like to see
    # black text on the background.
    black:   '#282c34'
    red:     '#e06c75'
    green:   '#98c379'
    yellow:  '#d19a66'
    blue:    '#61afef'
    magenta: '#c678dd'
    cyan:    '#56b6c2'
    white:   '#abb2bf'

  # Bright colors
  bright:
    black:   '#5c6370'
    red:     '#e06c75'
    green:   '#98c379'
    yellow:  '#d19a66'
    blue:    '#61afef'
    magenta: '#c678dd'
    cyan:    '#56b6c2'
    white:   '#ffffff'

background_opacity: 0.95 # terminal opacity
```

### 触摸板手势

> https://medium.com/@virajvchavan/how-to-have-mac-like-mouse-gestures-on-your-ubuntu-linux-cc773b38b959

### Logic Anywhere 2S配置

> https://github.com/PixlOne/logiops/wiki/Configuration
>
> https://github.com/PixlOne/logiops/wiki/CIDs
>
> https://github.com/torvalds/linux/blob/master/include/uapi/linux/input-event-codes.h

```bash
aurman -S logiops
```

配置文件：

```
devices: (
          {
name: "Wireless Mobile Mouse MX Anywhere 2S";
smartshift:
{
on: true;
threshold: 30;
};
hiresscroll:
{
hires: true;
invert: false;
target: false;
};
dpi: 1000;

buttons: (
          {
cid: 0x0052;
action =
{
type: "Gestures";
gestures: (
           {
direction: "Up";
mode: "OnRelease";
action =
{
type: "Keypress";
keys: ["KEY_LEFTCTRL","KEY_F8"];
};
},
{
direction: "Down";
mode: "OnRelease";
action =
{
type: "Keypress";
keys: ["KEY_LEFTMETA","KEY_D"];
};
},
{
direction: "Left";
mode: "OnRelease";
      action =
      {
type: "Keypress";
keys: ["KEY_LEFTCTRL","KEY_LEFTALT","KEY_LEFT"];
      };
},
{
direction: "Right";
mode: "OnRelease";
      action =
      {
type: "Keypress";
keys: ["KEY_LEFTCTRL","KEY_LEFTALT","KEY_RIGHT"];
      };

},
{
direction: "None"
             mode: "OnRelease";
           action =
           {
type: "Keypress";
keys: ["KEY_LEFTCTRL","KEY_F10"];
           };
}
);
};
},
{
cid: 0x0053;
# back button
     action =
     {
type: "Keypress";
keys: ["KEY_LEFTCTRL","KEY_LEFTMETA","KEY_RIGHT"];
     };
},
{
cid: 0x0056;
# forward button
     action =
     {
type: "Keypress";
keys: ["KEY_LEFTCTRL","KEY_LEFTMETA","KEY_LEFT"];
     };
}
);
}
);
```



## Vmware

> https://wiki.archlinux.org/index.php/VMware

### Ensure direct RAM access

By default, VMware writes a running guest system's RAM to a file on disk. If you are certain you have enough spare memory, you can ensure the guest OS writes its memory directly to the host's RAM by adding the following to the VM's `.vmx`:

```bash
MemTrimRate = "0"
sched.mem.pshare.enable = "FALSE"
prefvmx.useRecommendedLockedMemSize = "TRUE"
mainmem.backing = "swap"
```

### Paravirtual SCSI adapter

[VMware Paravirtual SCSI (PVSCSI) adapters](http://kb.vmware.com/kb/1010398) are high-performance storage adapters for VMware ESXi that can result in greater throughput and lower CPU utilization. PVSCSI adapters are best suited for environments, where hardware or applications drive a very high amount of I/O throughput.

The SCSI adapter type `VMware Paravirtual` is available in the Virtual Machine settings.

If these settings are not in the virtual machine's configuration, the paravirtual SCSI adapter can still be enabled. Ensure that the paravirtual SCSI adapter is included in the kernel image by modifying the `mkinitcpio.conf`:

```
/etc/mkinitcpio.conf
...
MODULES=(... vmw_pvscsi)
...
```

[Regenerate the initramfs](https://wiki.archlinux.org/index.php/Regenerate_the_initramfs), by

```bash
sudo mkinitcpio -p linux58 # 58 is the kernel version
```



Shut down the virtual machine and change the SCSI adapter: set the `.vmx` to the following:

```
scsi0.virtualDev = "pvscsi"
```

### Paravirtual network adapter

VMware offers [multiple network adapters](http://kb.vmware.com/kb/1001805) for the guest OS. The default adapter used is usually the `e1000` adapter, which emulates an Intel 82545EM Gigabit Ethernet NIC. This Intel adapter is generally compatible with the built-in drivers across most operating systems, including Arch.

For [more performance and additional features](http://rickardnobel.se/vmxnet3-vs-e1000e-and-e1000-part-1/) (such as multiqueue support), the VMware native `vmxnet3` network adapter can be used.

Arch has the `vmxnet3` kernel module available with a default install. Once enabled in [mkinitcpio](https://wiki.archlinux.org/index.php/Mkinitcpio) (or if it is auto-detected; check by running `lsmod | grep vmxnet3` to see if it is loaded), shut down and change the network adapter type in the *.vmx* file to the following:

```
ethernet0.virtualDev = "vmxnet3"
```

After changing network adapters, the network and [dhcpcd](https://wiki.archlinux.org/index.php/Dhcpcd) settings will need to be updated to use the new adapter name and MAC address.

```
# dhcpcd new_interface_name
# systemctl enable dhcpcd@new_interface_name.service
```

The new interface name can be obtained by running `ip link`.

## 声卡驱动

> https://wiki.archlinux.org/index.php/Dell_Inspiron_15_(7590)#Audio

### Use the new sof-hda-dsp driver

**Note:** The internal digital microphone only works with the new driver.

The new driver is integrated into the Linux kernel since version 5.3, however you may need to manually install the [sof-firmware](https://www.archlinux.org/packages/?name=sof-firmware) package. You also need to make sure that package [alsa-ucm-conf](https://www.archlinux.org/packages/?name=alsa-ucm-conf) is newer than version 1.2.3, which ships with a bugfix for this model.

After reboot, you should see a list of audio devices with names include Cannon Lake PCH cAVS. Also, you should be able to see Digital Microphone appearing in the "Recording Devices". If using the new sof-hda-dsp driver does not solve the problem, you will need to follow instructions below to use the legacy driver.

As of July 2020, there exists a bug that the Master device is automatically muted after rebooting. If you can hear no sound from output, install [alsa-utils](https://www.archlinux.org/packages/?name=alsa-utils) and run `alsamixer -c 0`. Then, switch to "Master" device, press "m" to unmute it so that you see "00" instead of "MM" under it. If it has 0% volume, you should also press up arrow key to increase the volume to 100%. After that, the sound should work.

You can use following command to store your current setting, so you don't need to un-mute the device manually after each reboot.

```
# alsactl store
```

**Note:** There is a bug report against the SOF driver for this laptop: https://github.com/thesofproject/linux/issues/1917

### Automatically un-muting Master device after booting

If entering `alsamixer -c 0` and un-muting the device manually after each reboot seems too laborious, you can also automate this process.

Create an executable script with the following commands and make it autostart depending on your environment (see [Autostarting](https://wiki.archlinux.org/index.php/Autostarting) for details).

```
amixer -Dhw:0 cset name='Master Playback Switch' on
amixer -Dhw:0 cset name='Master Playback Volume' 100%
```

### Use legacy HDA-Intel driver

**Note:** Using the old audio driver means that the internal microphone will not be available.

Create the following file:

```
/etc/modprobe.d/audio-fix.conf
blacklist snd-sof-pci
options snd-intel-dspcfg dsp_driver=1
```

It is also possible to provide this as a kernel parameter in GRUB configuration: `snd-intel-dspcfg.dsp_driver=1`.

You could also try setting the `snd_hda_intel.dmic_detect=0` kernel parameter, although this is due to be deprecated in favour of the above method.

# Softwares

## aurman

> https://www.garron.me/en/linux/arch-linux-aur-unkdown-public-key.html

```bash
wget https://aur.archlinux.org/packages/aurman/
tar -xvf aurman...
cd aurman...
makepkg -rsi
# makepkg problem
sudo pacman -S base-devel
# pkg key problem
gpg --keyserver keys.gnupg.net --recv-keys <key>
```

## VMware

> https://communities.vmware.com/thread/642914

```bash
sudo pacman -S linux<VERSION>-headers
```

### systemd services

*(Optional)* Instead of using `/etc/init.d/vmware` (`start|stop|status|restart`) and `/usr/bin/vmware-usbarbitrator` directly to manage the services, you may also use `.service` files (also available in the [vmware-systemd-services](https://aur.archlinux.org/packages/vmware-systemd-services/)AUR package, and also included in [vmware-patch](https://aur.archlinux.org/packages/vmware-patch/)AUR and [vmware-workstation](https://aur.archlinux.org/packages/vmware-workstation/)AUR with a few differences):

```bash
# /etc/systemd/system/vmware.service
[Unit]
Description=VMware daemon
Requires=vmware-usbarbitrator.service
Before=vmware-usbarbitrator.service
After=network.target

[Service]
ExecStart=/etc/init.d/vmware start
ExecStop=/etc/init.d/vmware stop
PIDFile=/var/lock/subsys/vmware
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

```bash
# /etc/systemd/system/vmware-usbarbitrator.service
[Unit]
Description=VMware USB Arbitrator
Requires=vmware.service
After=vmware.service

[Service]
ExecStart=/usr/bin/vmware-usbarbitrator
ExecStop=/usr/bin/vmware-usbarbitrator --kill
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Add this service to enable networking:

```bash
/etc/systemd/system/vmware-networks-server.service
[Unit]
Description=VMware Networks
Wants=vmware-networks-configuration.service
After=vmware-networks-configuration.service

[Service]
Type=forking
ExecStartPre=-/sbin/modprobe vmnet
ExecStart=/usr/bin/vmware-networks --start
ExecStop=/usr/bin/vmware-networks --stop

[Install]
WantedBy=multi-user.target
```

Add this service as well, if you want to connect to your VMware Workstation installation from another Workstation Server Console:

```bash
/etc/systemd/system/vmware-workstation-server.service
[Unit]
Description=VMware Workstation Server
Requires=vmware.service
After=vmware.service

[Service]
ExecStart=/etc/init.d/vmware-workstation-server start
ExecStop=/etc/init.d/vmware-workstation-server stop
PIDFile=/var/lock/subsys/vmware-workstation-server
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

After which you can [enable](https://wiki.archlinux.org/index.php/Enable) them on boot.

## Matlab

> https://wiki.archlinux.org/index.php/MATLAB#Installing_from_the_MATLAB_installation_software
>
> The MATLAB installation software is self contained and does not require any additional packages to install in silent mode. To install with the GUI a working [Xorg](https://wiki.archlinux.org/index.php/Xorg) graphical display is necessary. [Wayland](https://wiki.archlinux.org/index.php/Wayland) is not currently supported yet, at least not for the GUI; however, plotting seems to work. The installation is handled by the `install` script. You can run the script as root to install MATLAB system-wide or your user to install it only for you. On version R2020 the normal `install` script may not work, however, the legacy install script `ISO-DIR/bin/glnxa64/install_unix_legacy` does work

### Add desktop launch

> https://www.mathworks.com/matlabcentral/answers/20-how-do-i-make-a-desktop-launcher-for-matlab-in-linux

To have MATLAB open up from a launcher, you need to add the –desktop flag to the command field. For example:

```bash
/usr/local/matlab/bin/matlab –desktop
```

If that does not work, try changing the launcher type from "Application" to "Application in terminal". If there is a MATLAB startup error, it won’t be displayed unless MATLAB is started with terminal.

## Mailspring

### Could not launch

> https://foundry376.zendesk.com/hc/en-us/articles/115001875571

```bash
sudo pacman -S gnome-keyring
sudo pacman -S seahorse # 解决每次登录需要输入密码的问题
```

## Maya

> https://medium.com/@llama_9851/installing-maya2020-on-arch-linux-e257ffadd52c

```bash
aurman -S maya # install maya from aur lib
```

### adsklicensing not running

```bash
sudo getent group adsklic &>/dev/null || sudo groupadd adsklic

sudo id -u adsklic &>/dev/null || sudo useradd -M -r -g adsklic adsklic -d / -s /usr/bin/nologin 

sudo systemctl enable adsklicensing --quiet

sudo systemctl start adsklicensing
```

Check if maya got added to the list

```
/opt/Autodesk/AdskLicensing/9.2.1.2399/helper/AdskLicensingInstHelper list
```

If you see Maya added great! skip this next step. Personally, I didn’t see Maya added so we have to add it manually, run:

```
sudo /opt/Autodesk/AdskLicensing/9.2.1.2399/helper/AdskLicensingInstHelper register -pk 657L1 -pv 2020.0.0.F -el EN_US -cf /var/opt/Autodesk/Adlm/Maya2020/MayaConfig.pit
```

### Symlinks for some SSL stuff

Maya requires two libraries which we should already have installed from preparing our anus in Step 2. The issue is that Maya is looking them up with a different name. To fix this we add symlinks from the library filenames Maya expects to the library filenames Maya needs:

```
sudo ln -s /usr/lib64/libssl.so.1.0.0 /usr/lib64/libssl.so.10
sudo ln -s /usr/lib64/libcrypto.so.1.0.0 /usr/lib64/libcrypto.so.10
```

### License Service

Originally, this step didn’t exist. But immediately within 24/hrs of writing this I had students contacting me saying they got it installed, but couldn’t activate their student licenses. These next two commands start a service which makes serial number license activation possible:

```bash
sudo /opt/Autodesk/Adlm/FLEXnet/bin/toolkitinstall.sh
sudo /opt/Autodesk/Adlm/FLEXnet/bin/install_fnp.sh /opt/Autodesk/Adlm/FLEXnet/bin/FNPLicensingService
## When it asks for your registration information,
## go to http://www.autodesk.com/education/free-software/maya
## and create a student account. Next, select Maya 2020 for the version,
## Linux as the operating system, and English for the language.

## Then you must to register Maya manually

## Run:

$ sudo /opt/Autodesk/AdskLicensing/9.2.1.2399/helper/AdskLicensingInstHelper register --pk 657L1  --pv 2020.0.0.F --cf /var/opt/Autodesk/Adlm/Maya2020/MayaConfig.pit --el en --sn <serial number>
```

## Homestead

```bash
aurman -S vagrant # install vagrant
# Download and install virtualbox from website
vagrant box add laravel/homestead
```

## Navicat

```bash
# download app image from official website
mkdir navicat
sudo mount -o loop ~/Desktop/navicat15-premium-en.AppImage navicat
cp -r ./navicat ./navicat-patched
sudo umount ./navicat
sudo rm -rf ./navicat
git clone https://github.com/HeQuanX/navicat-keygen-tools
cd navicat-keygen-tools
git checkout linux
make all # build
./bin/navicat-patcher <navicat-patched>
./appimagetool-x86_64.AppImage ~/Desktop/navicat15-premium-en-patched ~/Desktop/navicat15-premium-en-patched.AppImage
./navicat-keygen --text <RegPrivateKey.pem>
```

### Can not connect to mysql

```bash
sudo pacman -S mariadb mariadb-libs
mariadb-install-db
sudo ln -s /var/run/mysqld/mysqld.sock /var/lib/mysql/mysql.sock
sudo chmod 755 /var/lib/mysql -R
```

