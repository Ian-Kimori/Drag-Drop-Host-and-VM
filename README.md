# Drag & Drop Host-and-VM

***

## **Step 1 — Fully Update Kali Linux**

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

***

## **Step 2 — Install all required build tools**

These MUST be installed before Guest Additions can compile:

```bash
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

***

## **Step 3 — Remove any previously installed/failed Guest Additions**

Old/broken modules are the #1 cause of DnD failures.

```bash
sudo apt purge virtualbox-guest-x11 virtualbox-guest-utils virtualbox-guest-dkms -y
```
```bash
sudo reboot
```
***

## **Step 4 — Insert the Guest Additions ISO**

In the VirtualBox window top menu:

    Devices → Insert Guest Additions CD Image…

Wait a few seconds.

***

## **Step 5 — Confirm where the ISO is mounted**

Verify:

```bash
ls /run/media/$USER/
```

You should see:

    VBox_GAs_7.2.6

Now list it:

```bash
ls /run/media/$USER/VBox_GAs_7.2.6
```

You MUST see:

    VBoxLinuxAdditions.run
    autorun.sh
    cert/

***

## **Step 6 — Run the Guest Additions Installer from the CORRECT path**

Since your ISO is auto‑mounted under `/run/media`, NOT `/mnt`, run:

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

Let it run completely.

You should see:

✔ Building kernel modules  
✔ Installing Guest Additions  
✔ No errors

***

## **Step 7 — Reboot Kali**

```bash
sudo reboot
```

***

## **Step 8 — Ensure you’re using X11 (NOT Wayland)**

VirtualBox DnD does **not** work on Wayland.

Check:

```bash
echo $XDG_SESSION_TYPE
```

If you see `wayland`:

1.  Log out
2.  On login screen, click the ⚙️ icon
3.  Choose **X11** or **Xorg Session**
4.  Log in again

***

## **Step 9 — Enable Clipboard + Drag‑and‑Drop in VirtualBox settings**

### Shut down the VM → open VirtualBox settings:

    Settings → General → Advanced

Set:

*   **Shared Clipboard → Bidirectional**
*   **Drag and Drop → Bidirectional**

Click OK → start VM.

***

## **Step 10 — Start DnD/Clipboard Services (Important)**

Inside Kali, run:

```bash
VBoxClient --clipboard &
```
```bash
VBoxClient --draganddrop &
```

No output = GOOD.

***

## **Step 11 — TEST FUNCTIONALITY**

### Test copy/paste:

*   Copy text from host → paste in VM
*   Copy text from VM → paste to host

### Test drag-and-drop:

*   Drag a file from host → into VM
*   Drag a file from VM → to host desktop

No more timeout errors.

***
