
# Ubuntu

## Disable IPv6

To disable IPV6 just run the following commands, this will not persist after rebooting
your machine

```bash
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1
```

One method to make this option persist is modifying `/etc/sysctl.conf`, lets create a
custom file that persist the ipv6 settings event after distro update, you should be
logged in as **sudo**

```bash
cat >> /etc/sysctl.conf/60-custom.conf < EOF

net.ipv6.conf.all.disable_ipv6=1
net.ipv6.conf.default.disable_ipv6=1
net.ipv6.conf.lo.disable_ipv6=1
net.ipv6.conf.<WIFI>.disable_ipv6=1
EOF
```

For the settings to take effect use:

```bash
sudo sysctl -p
sudo systemctl restart procps
```

### Disable IPv6 with GRUB

Access GRUB file

```bash
sudo nano /etc/default/grub
```

Comment the current `GRUB_CMDLINE_LINUX_DEFAULT` and paste the following values:

To Disable IPv6:

```bash
GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1"
```

To Enable IPv6:

```bash
GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=0"
```

Update GRUB config:

```bash
sudo update-grub
```

## Usefull Commands

### List process running on ports

```zsh
sudo lsof -i -P -n
```

### Linux Distribution Info

The `lsb release` utility displays information about the Linux distribution’s LSB - Linux
Standard Base.

```bash
lsb_release -a
```

The `/etc/os-release` file contains operating system identification data and
distribution information.

```bash
cat /etc/os-release
```

### Redirect STDOUT to a File

Redirect stdout (standart output) of a command to a file.

`>`: Overwrites a file with the command's output.

`>>`: Appends the command's output to a file.

> [!TIP]
> In both cases if the file doesn't exists, it will be created

### Ping Pong

Ping tests remote devices connectivity by sending an eco packages of ICMP
(Internet Control Message Protocol) to a destination device

```bash
ping < IP Address | DNS >
```

### Telnet

```bash
telnet < host > < port >
```

The command will lock the prompt, press `CNTRL + ]` to exit

### ln - symlinks

```bash
ln [-s] [source file] [link name]
```

The default behavior is hard links, this will inherit the original path permission and
other actions can have effect on the path linked.

Use `-s` for soft links, have it's own permission and only set a reference to the
original path.
