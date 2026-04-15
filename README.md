# Drag & Drop Host-and-VM

## Step 1 — Fully Update Kali Linux

```bash
sudo apt update
```
```bash
sudo apt full-upgrade -y
```
```bash
sudo reboot
```

---

## Step 2 — Install all required build tools

```bash
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

---

## Step 3 — Remove any previously installed/failed Guest Additions

```bash
sudo apt purge virtualbox-guest-x11 virtualbox-guest-utils virtualbox-guest-dkms -y
```
```bash
sudo reboot
```

---

## Step 4 — Enable Clipboard + Drag-and-Drop in VirtualBox settings

Shut down the VM first, then:

```
Settings → General → Advanced
```

Set:
- **Shared Clipboard → Bidirectional**
- **Drag and Drop → Bidirectional**

Click OK → start VM.

---

## Step 5 — Ensure you're on X11 (NOT Wayland)

```bash
echo $XDG_SESSION_TYPE
```

Must output `x11`. If it says `wayland`:
1. Log out
2. Click ⚙️ on login screen
3. Select **Xorg Session**
4. Log back in

---

## Step 6 — Check if Guest Additions are already installed

Before touching the ISO, check if they're already present:

```bash
lsmod | grep vboxguest
```
```bash
VBoxClient --version
```

If both return output, **skip Steps 7–10** and go straight to Step 11.

---

## Step 7 — Insert the Guest Additions ISO

```
Devices → Insert Guest Additions CD Image…
```

Wait a few seconds.

---

## Step 8 — Confirm where the ISO mounted

```bash
ls /run/media/$USER/
```

Expected output:
```
VBox_GAs_7.x.x
```

If you get `No such file or directory`, check alternate locations:
```bash
lsblk
```
```bash
findmnt | grep -i vbox
```

If it shows up elsewhere (e.g. `/media/cdrom`), use that path in Step 9.

---

## Step 9 — Run the installer

```bash
sudo bash /run/media/$USER/VBox_GAs_*/VBoxLinuxAdditions.run
```

If that path doesn't exist, fallback:
```bash
sudo mkdir -p /media/cdrom
```
```bash
sudo mount /dev/cdrom /media/cdrom
```
```bash
sudo sh /media/cdrom/VBoxLinuxAdditions.run
```

Expected output:
```
✔ Building kernel modules
✔ Installing Guest Additions
✔ No errors
```

---

## Step 10 — Reboot

```bash
sudo reboot
```

---

## Step 11 — Start DnD/Clipboard Services

```bash
VBoxClient --clipboard &
```
```bash
VBoxClient --draganddrop &
```

No output = GOOD.

---

## Step 12 — Test Functionality

### Copy/paste:
- Copy text from host → paste in VM
- Copy text from VM → paste to host

### Drag-and-drop:
- Drag a file from Windows → into Kali VM window
- Drag a file from Kali → to Windows desktop

---

## Step 13 — Shared Folder Setup

On Windows, open VirtualBox:

1. Select Kali VM → **Settings → Shared Folders**
2. Click ➕
3. Choose a folder (e.g. `C:\Users\Ian\Desktop\shared`)
4. Check ✅ Auto-mount and ✅ Make permanent

### Access in Kali:
```bash
ls /media/sf_shared
```

### If permission denied:
```bash
sudo usermod -aG vboxsf $USER
```
```bash
reboot
```

---

## Step 14 — SCP Method (Hacker Style Alternative)

### On Kali:
```bash
ip a   # get your IP e.g. 192.168.x.x
```

### On Windows PowerShell:
```powershell
scp kali@192.168.x.x:/home/kali/file.txt C:\Users\Ian\Desktop
```

---

## Step 15 — USB Fallback

1. Plug in USB
2. `Devices → USB → Select device`

```bash
lsblk
```
```bash
sudo mount /dev/sdb1 /mnt
```
