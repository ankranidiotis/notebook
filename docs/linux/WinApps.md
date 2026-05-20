# Docker Engine + Windows 11 VM (on Linux Mint 22.1) — Cheat Sheet

**Host**: Linux Mint 22.1 (Ubuntu 24.04 base) · **CPU**: AMD (SVM) · **Goal**: Install Docker Engine, and run a Windows 11 VM via Docker (dockurr/windows).

---

## 1) Install Docker Engine (from Docker’s APT repo)
> Your system already has the Docker repo configured. If not, follow Docker’s official instructions for Ubuntu 24.04 first.

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Enable + start
sudo systemctl enable docker
sudo systemctl start docker
```

**User without sudo:** add your user to the `docker` group and re-login.
```bash
sudo usermod -aG docker $USER
newgrp docker    # temporary in current shell; full fix is log out/in
```

**Sanity checks:**
```bash
docker version
docker run --rm hello-world
```

---

## 2) Prepare KVM (hardware acceleration)

**Check CPU virtualization flags (AMD = `svm` expected):**
```bash
egrep -o '(svm|vmx)' /proc/cpuinfo | sort -u
```

**If `/dev/kvm` is missing, enable SVM in BIOS/UEFI:**
- Reboot → BIOS (Del) → *Advanced CPU Settings* → **SVM Mode: Enabled** → Save & exit.

**Load KVM modules and verify:**
```bash
sudo modprobe kvm
sudo modprobe kvm_amd        # Intel would be: kvm_intel
lsmod | grep -E '(^kvm$|kvm_amd)'
ls -l /dev/kvm               # expect: crw-rw---- root kvm
```

If needed (until next reboot):
```bash
sudo chgrp kvm /dev/kvm
sudo chmod 660 /dev/kvm
newgrp kvm
```

Make modules persistent:
```bash
printf "kvm\nkvm_amd\n" | sudo tee /etc/modules-load.d/kvm.conf >/dev/null
```

**Diagnostics if loading fails:**
```bash
dmesg | grep -i kvm
modinfo kvm | head
modinfo kvm_amd | head || sudo apt install -y linux-modules-extra-$(uname -r)
```

---

## 3) Create Windows 11 VM via Docker (dockurr/windows)

**Project folder:**
```bash
mkdir -p ~/win11-vm && cd ~/win11-vm
```

**`compose.yaml` (basic, auto-download Windows 11 Pro):**
```yaml
services:
  windows:
    image: dockurr/windows
    container_name: win11
    environment:
      VERSION: "11"        # 11 Pro; 11l = LTSC
      RAM_SIZE: "8G"       # adjust
      CPU_CORES: "4"
      DISK_SIZE: "128G"
      USERNAME: "Docker"   # optional: first-boot user
      PASSWORD: "admin"
    devices:
      - /dev/kvm
    ports:
      - "8006:8006"        # web console (noVNC)
      - "3389:3389/tcp"    # RDP
      - "3389:3389/udp"
    volumes:
      - ./windows:/storage # persistent VM storage
    restart: unless-stopped
    stop_grace_period: 2m
```

**Use your own ISO (instead of auto-download):** add a line under `volumes`:
```yaml
    volumes:
      - ./Win11_24H2_English_x64.iso:/boot.iso  # your ISO beside compose.yaml
      - ./windows:/storage
```
> When `/boot.iso` is present, `VERSION` is ignored.

**Start the VM:**
```bash
docker compose up -d
docker logs -f win11   # watch progress (Ctrl+C to exit)
```

**Access console (installer):**
- Browser → http://localhost:8006
- Proceed with standard Windows setup (choose **Windows 11 Pro** for RDP support).

**Use RDP for better performance (Linux Mint):**
```bash
sudo apt install -y remmina freerdp3-x11
remmina    # connect to 127.0.0.1:3389 (user/password from above)
```
Inside Windows: Settings → System → Remote Desktop → **Enable** (if needed).

**Lifecycle:**
```bash
docker ps
docker stop win11
docker start win11
docker compose up -d   # apply resource changes after editing compose.yaml
```

---

## 4) Optional: Shared folder & LAN IP

**Share a host folder to the VM (exposed as SMB share inside the container):**
```yaml
    volumes:
      - /home/$USER/WinShare:/data
```
In Windows: `\\host.lan\Data` (or use the container’s IP). Map as a drive if desired.

**Give the VM a dedicated LAN IP (macvlan):**
```bash
# Create network (adjust subnet/gateway/parent NIC)
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  --ip-range=192.168.1.200/29 \
  -o parent=enp4s0 vlan
```
Add to `compose.yaml`:
```yaml
    networks:
      vlan:
        ipv4_address: 192.168.1.200
networks:
  vlan:
    external: true
```

(Optionally add `DHCP: "Y"` and `/dev/vhost-net` to let Windows request its own DHCP IP; see project docs.)

---

## 5) WinApps Prep
Inside Windows:
1) Install apps you want to publish (e.g., Office).  
2) Ensure RDP is enabled and allowed in Firewall.  
3) Create a stable local admin user + password.  
4) Disable sleep/hibernate (Settings → Power → Sleep = **Never**).

Then follow WinApps’ Docker backend steps on Linux to generate launchers that RDP into this VM.

---

## 6) Troubleshooting quick refs

- **`permission denied` on `/var/run/docker.sock`** → add user to `docker` group, re-login, check `ls -l /var/run/docker.sock` (group docker, mode 660).  
- **`context "desktop-linux" not found`** → `docker context use default` (or recreate default).  
- **`docker-credential-desktop` missing** → remove `credsStore`/`credHelpers` from `~/.docker/config.json`.  
- **`/dev/kvm` not found** → enable SVM in BIOS, `modprobe kvm kvm_amd`, verify permissions.  
- **Slow video** → use RDP (`localhost:3389`) instead of the web console.  
- **Change RAM/CPU/DISK** → edit env vars in compose, `docker compose up -d`.  

---

## One-liners you’ll reuse

```bash
# Show context & switch to local
docker context ls
docker context use default

# Basic Docker sanity
docker version && docker info

# KVM checks
ls -l /dev/kvm
lsmod | grep -E '(^kvm$|kvm_amd)'
dmesg | grep -i kvm

# VM lifecycle
docker logs -f win11
docker stop win11 && docker start win11
```