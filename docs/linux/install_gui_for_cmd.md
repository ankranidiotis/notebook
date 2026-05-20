# Install GUI on Linux from command line

## Basic Installation
On remote virtual machine, at command line, run:

```
sudo apt install xfce4 xfce4-goodies tightvncserver
```

## Configure VNC Server

Run this command to setup the server.The default port is 5901.

```
vncserver
```

To change the server's password run:

```
vncpasswd
```

Then, to configure which commands VNC will execute when it starts up, first stop the server:

```
vncserver -kill :1
```

Then, modify ~/.vnc/xstartup with nano and add the line:

```
startxfce4 &
```

which launches Xfce automatically. Finally, make the startup file executable:

```
chmod +x ~/.vnc/xstartup
```

## Start VNC server:

```
vncserver -localhost
```

## More tools to install

```
sudo apt install kali-linux -full

or

sudo apt install kali-linux -top10
```

## Additional install

```
sudo apt-get install gnome-core kali-defaults kali-root-login desktop-base
```

## Setup VNC Viewer

```
tightvncserver -geometry 1024x768
```

Check that it runs:
```
netstat -tulpn
```
Run from local machine:
```
ssh -L 5901:localhost:5901 -N -f kali@ip -i keyfile
```