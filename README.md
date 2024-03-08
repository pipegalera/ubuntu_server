# ubuntu_server

Please make sure you have a static IP assigned to your router.

## USB stick installation options

- [X] Search for third-party drivers
- [ ] Set up this disk as an LVM group
- [X] Install OpenSSH server

## First Server login

```
sudo nano /etc/default/console-setup # Bigger font
      FONTFACE="Terminus"
      FONTSIZE="16x32"
sudo update-initramfs -u
reboot
```

## Initial config 

We'll:

1. Update the system.
2. Avoid the system waiting 2 minutues every booth for the ethernet connection (server will be connected via WiFi).
3. Avoid the system to go to sleep/suspend/hibernate causing ssh to time out.
4. Install a network manager to connect via WiFi. Follow: https://ubuntu.com/core/docs/networkmanager/configure-wifi-connections once installed.

```
sudo apt-get update                                       
systemctl disable systemd-networkd-wait-online.service    
systemctl mask systemd-networkd-wait-online.service
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target     
sudo apt install network-manager                          
```

## Configure SSH for secure remote connection

Before continuing, make sure you can connect to the server with any computer in the network via `ssh {server username}@{server ip}`. You you connect, start preparing the network security. Since the server will be exposed to the internet outside of your local network, network security protection is essential.

For any client to connect to the server, they must: 

1. Have the unique encripted key.
2. Know the port of the server

From the server side, we will protect the network by:

1. Using the same unique encripted key.
2. Limiting the amount of times a client can try to log in (Fail2ban).
3. Changing the default port (Port 22).

Let's start.

### Key paring

- In the local client
  
```
ssh-keygen -t ed25519 -f ~/.ssh/{whatever key name you what to use}
sh-copy-id -i .ssh/home.pub {hostname}@{ip adress}
```

- In the server side:

Make sure the new key is copyed in the server: `nano .ssh/authorized_keys`

```
sudo  nano /etc/ssh/sshd_config
->      PermitRootLogin no
->      PasswordAuthentication no
sudo systemctl reload sshd
```

Keeping `PasswordAuthentication yes` enabled will allow anyone without the public-private key pair to try to guess the password. Which we won't.

### Fail2ban

To avoid clients entering the server by brute force. Fail2Ban scans log files like /var/log/auth.log and bans IP addresses conducting too many failed login attempts. It does this by updating system firewall rules to reject new connections from those IP addresses, for a configurable amount of time. [Source](https://github.com/fail2ban/fail2ban)

Even if we login via key-pair, [fail2ban is still useful because it can still blacklist IPs hammering your server](https://www.reddit.com/r/pihole/comments/uflft8/ssh_keys_fail2ban/)

```
sudo apt install fail2ban
cd /etc/fail2ban
sudo cp fail2ban.conf fail2ban.local #backup
sudo cp jail.conf jail.local
```

In  `fail2ban.local` you can edit the defaults to set the number of max attempts, how much time the ip will be banned, and so forth. I keep the default: 

```
findtime = 10m
maxretry = 5
bantime = 10m
```

### Changing the default Port 22

We'll activate the firewall and only allow the connection via the port we select.  

```
sudo ufw enable # By default, the firewall is inactive (check `sudo ufw status`)
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak #Backup current sshd setup
->           Port {whatever set of 5 digits}
sudo ufw allow {whatever set of 5 digits}/tcp
```

## Configure client access for easy log in

Edit the `.ssh/config` file with the following, so you can access the server quickly and easy from the client.

```
Host {host} 
  HostName {ip adress}
  User {hostname}
  Port {port digits}
  IdentityFile ~/.ssh/{whatever key name you used for the key}
  StrictHostKeyChecking no
```
Setting this file allow to connect from the client simply typing `ssh {host}` instead of `ssh -p {port digits} {hostname}@{ip adress}`

## Allowing connections outside the network: Port Forwarding


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
