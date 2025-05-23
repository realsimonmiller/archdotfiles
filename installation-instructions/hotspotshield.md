# Hotspot Shield VPN on Arch

**Package:** `hotspotshield-bin` (AUR)  
**Exit node default:** IM (Isle of Man)

---

## 1. Install the AUR package

```bash
sudo pacman -S --needed base-devel git
cd /tmp
git clone https://aur.archlinux.org/yay.git
cd yay && makepkg -si
yay -S hotspotshield-bin
```

## 2. Here’s how I’d fold Hotspot Shield into your dotfiles repo and capture the full install quirks in a reusable guide:

# Hotspot Shield VPN on Arch

**Package:** `hotspotshield-bin` (AUR)  
**Exit node default:** IM (Isle of Man)

---

## 1. Install the AUR package

```bash
sudo pacman -S --needed base-devel git
cd /tmp
git clone https://aur.archlinux.org/yay.git
cd yay && makepkg -si
yay -S hotspotshield-bin
````

## 2. Enable TUN device and disable rp\_filter

```bash
sudo modprobe tun
# Create sysctl override so rp_filter stays off:
sudo tee /etc/sysctl.d/99-hotspotshield.conf <<EOF
net.ipv4.conf.all.rp_filter = 0
net.ipv4.conf.default.rp_filter = 0
EOF
sudo sysctl --system
```

## 3. Grant minimal capabilities

```bash
# remove any old caps
sudo setcap -r /usr/bin/hotspotshield
# allow only tunnel+raw sockets
sudo setcap cap_net_admin,cap_net_raw+eip /usr/bin/hotspotshield
```

## 4. Sign in and connect

```bash
hotspotshield account signin
hotspotshield start
hotspotshield connect IM
hotspotshield status
```

## 5. (Optional) Launcher & autostart

* Add `hotspotshield.desktop` to `~/.local/share/applications/` with:

  ```ini
  [Desktop Entry]
  Type=Application
  Name=Hotspot Shield
  Exec=kitty -e bash -ic "hotspotshield start && hotspotshield connect IM && exec bash"
  Icon=hotspotshield
  Categories=Network;System;Security;
  Terminal=false
  ```
* Autostart via XDG:

  ```bash
  mkdir -p ~/.config/autostart
  cp ~/.local/share/applications/hotspotshield.desktop ~/.config/autostart/
  ```

