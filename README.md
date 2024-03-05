# ubuntu_server

## USB stick installation options

- [X] Search for third-party drivers
- [ ] Set up this disk as an LVM group
- [X] Install OpenSSH server

## First login

```
sudo apt-get update                                       # Update the system
systemctl disable systemd-networkd-wait-online.service    # Disable the 2 minutes wait that Ubuntu Server spend trying waiting for the
systemctl mask systemd-networkd-wait-online.service       # internet
sudo apt install network-manager                          # Install the network manager for wifi
```

Follow: https://ubuntu.com/core/docs/networkmanager/configure-wifi-connections

## Install NVIDIA beta drivers

```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo ubuntu-drivers
nvidia-driver-550
```

[Source](https://askubuntu.com/questions/819117/how-can-i-get-autologin-at-startup-working-on-ubuntu-server-16-04-1)
