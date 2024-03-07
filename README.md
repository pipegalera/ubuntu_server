# ubuntu_server

Please make sure you have a static IP assigned to your router.

## USB stick installation options

- [X] Search for third-party drivers
- [ ] Set up this disk as an LVM group
- [X] Install OpenSSH server

## First login

```
sudo nano /etc/default/console-setup # Bigger font
      FONTFACE="Terminus"
      FONTSIZE="16x32"
sudo update-initramfs -u
reboot
```


```
sudo apt-get update                                       # Update the system
systemctl disable systemd-networkd-wait-online.service    # Disable the 2 minutes wait that Ubuntu Server spend trying waiting for the
systemctl mask systemd-networkd-wait-online.service       # internet
sudo apt install network-manager                          # Install the network manager for wifi
sudo apt install net-tools
```

Follow: https://ubuntu.com/core/docs/networkmanager/configure-wifi-connections

## Install NVIDIA beta drivers

```
sudo add-apt-repository ppa:graphics-drivers/ppa
ubuntu-drivers devices
sudo ubuntu-drivers install --gpgpu nvidia:550
sudo apt install nvidia-utils-550
```


## Connect via SSH


```
ssh {user}@{IP}
```

## Nano shortcuts

- Copy: `Alt`+`Shift`+`6`
- Dellete all marked: `Ctrl`+`Shift`+`K` 
