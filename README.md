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
```

Follow: https://ubuntu.com/core/docs/networkmanager/configure-wifi-connections

## Configure SSH for secure remote connection

Here we make sure that the client have: 

1. An encripted key.
2. The password of the server.

From the server side, we need:

1. The othe side of the encripted key.
2. Security protection.

Since the server will be exposed to the internet outside of your local network, network security protection is essential.

### Key paring

- In the local client
  
```
ssh-keygen -t ed25519 -f ~/.ssh/{whatever key name you what to use}
sh-copy-id -i .ssh/home.pub {hostname}@{ip adress}
```

Also, edit the `.ssh/config` file with the following, so you can access the server quickly and easy from the server.

```
Host {what ever shortcut name you want to use} 
  HostName {ip adress}
  User {hostname}
  IdentityFile ~/.ssh/{whatever key name you used for the key}
  StrictHostKeyChecking no
```

- In the server side:

Make sure the new key is copyed in the server: `nano .ssh/authorized_keys`

```
sudo  nano /etc/ssh/sshd_config
      PermitRootLogin no
      PasswordAuthentication yes
sudo systemctl reload sshd
```


### Fail2ban

To avoid clients entering the server by brute force.

```
sudo apt install fail2ban
cd etc/fail2ban
cp fail2ban.conf fail2ban.local #backup
cp jail.conf jail.local #backup
```

With `nano fail2ban.local` edit the first rows to set the number of max attempts, how much time the ip will be banned, and so forth.

For me, I set 3 fail attempts within 5 min makes the ip be banned 10 hours.

## Packages to install after ssh connection

```
sudo apt install neofetch
sudo apt install net-tools
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo ubuntu-drivers install nvidia:550 # The server version of 550 is essencially the same driver.

```

## Nano shortcuts

- Copy: `Alt`+`Shift`+`6`
- Dellete all marked: `Ctrl`+`Shift`+`K` 
