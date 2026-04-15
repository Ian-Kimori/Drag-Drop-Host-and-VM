# Drag & Drop Host-and-VM

## Step 1 — Fully Update Kali Linux

This prevents kernel/header mismatches:

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

These MUST be installed before Guest Additions can compile:

```bash
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

---

## Step 3 — Remove any previously installed/failed Guest Additions

Old/broken modules are the #1 cause of DnD failures.

```bash
sudo apt purge virtualbox-guest-x11 virtualbox-guest-utils virtualbox-guest-dkms -y
```
```bash
sudo reboot
```

---

## Step 4 — Enable Clipboard + Drag-and-Drop in VirtualBox settings

**Before booting the VM**, shut it down and open VirtualBox settings:

```
Settings → General → Advanced
```

Set:
- **Shared Clipboard → Bidirectional**
- **Drag and Drop → Bidirectional**

Click OK → start VM.

---

## Step 5 — Ensure you're using X11 (NOT Wayland)

VirtualBox DnD does **not** work on Wayland.

```bash
echo $XDG_SESSION_TYPE
```

If you see `wayland`:
1. Log out
2. On login screen, click the ⚙️ icon
3. Choose **X11** or **Xorg Session**
4. Log in again

---

## Step 6 — Insert the Guest Additions ISO

In the VirtualBox window top menu:

```
Devices → Insert Guest Additions CD Image…
```

Wait a few seconds.

---

## Step 7 — Confirm where the ISO is mounted

```bash
ls /run/media/$USER/
```

You should see:

```
VBox_GAs_7.2.6
```

Now list it:

```bash
ls /run/media/$USER/VBox_GAs_7.2.6
```

You MUST see:

```
VBoxLinuxAdditions.run
autorun.sh
cert/
```

---

## Step 8 — Run the Guest Additions Installer from the CORRECT path

Since your ISO is auto-mounted under `/run/media`, NOT `/mnt`, run:

```bash
sudo bash /run/media/$USER/VBox_GAs_7.2.6/VBoxLinuxAdditions.run
```

If you get a permissions error:

```bash
sudo chmod +x /run/media/$USER/VBox_GAs_7.2.6/VBoxLinuxAdditions.run
```
```bash
sudo /run/media/$USER/VBox_GAs_7.2.6/VBoxLinuxAdditions.run
```

Let it run completely. You should see:

```
✔ Building kernel modules
✔ Installing Guest Additions
✔ No errors
```

---

## Step 9 — Mount and install (if Step 8 fails)

```bash
sudo mkdir /media/cdrom
```
```bash
sudo mount /dev/cdrom /media/cdrom
```
```bash
cd /media/cdrom
```
```bash
sudo sh VBoxLinuxAdditions.run
```

---

## Step 10 — Reboot Kali

```bash
sudo reboot
```

---

## Step 11 — Start DnD/Clipboard Services

Inside Kali, run:

```bash
VBoxClient --clipboard &
```
```bash
VBoxClient --draganddrop &
```

No output = GOOD.

---

## Step 12 — Create Shared Folder in VirtualBox

On Windows:

1. Open VirtualBox
2. Select Kali VM → **Settings**
3. Go to **Shared Folders**
4. Click ➕
5. Choose a Windows folder (e.g. `C:\Users\Ian\Desktop\shared`)
6. Check:
   - ✅ Auto-mount
   - ✅ Make permanent

### (i): Access in Kali

```bash
ls /media
```
Or:
```bash
ls /media/sf_shared
```

### (ii): If permission denied

```bash
sudo usermod -aG vboxsf $USER
```
```bash
reboot
```

---

## Step 13 — USB Method (Fallback)

1. Plug USB
2. Attach it to VM: `Devices → USB → Select device`

Then in Kali:

```bash
lsblk
```
```bash
sudo mount /dev/sdb1 /mnt
```

---

## Step 14 — SCP Method (Advanced / Hacker Style)

If Kali has network access:

### On Kali:
```bash
ip a
```
Get IP (e.g. `192.168.x.x`)

### On Windows (PowerShell):
```powershell
scp kali@192.168.x.x:/home/kali/file.txt C:\Users\Ian\Desktop
```

---

## Step 15 — Test Functionality

### Test copy/paste:
- Copy text from host → paste in VM
- Copy text from VM → paste to host

### Test drag-and-drop:
- Drag a file from host → into VM
- Drag a file from VM → to host desktop

---

**Key changes made:**
- Step 4 (VirtualBox settings) moved before booting — you can't change these while the VM is running
- Step 5 (X11 check) moved before inserting the ISO — no point installing if Wayland will break DnD anyway
- Step 9 (manual mount fallback) placed immediately after Step 8 as a fallback
- Step 11 (start services) moved right after reboot where it logically belongs
- Everything else preserved exactly as written
