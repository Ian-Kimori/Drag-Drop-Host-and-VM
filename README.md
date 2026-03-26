# Drag & Drop Host-and-VM

***

# **STEP 1 — Fully Update Kali Linux**

This prevents kernel/header mismatches:

```bash
sudo apt update
sudo apt full-upgrade -y
sudo reboot
```

***

# **STEP 2 — Install all required build tools**

These MUST be installed before Guest Additions can compile:

```bash
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

You already have them — good.

***

# **STEP 3 — Remove any previously installed/failed Guest Additions**

Old/broken modules are the #1 cause of DnD failures.

```bash
sudo apt purge virtualbox-guest-x11 virtualbox-guest-utils virtualbox-guest-dkms -y
sudo reboot
```

***

# **STEP 4 — Insert the Guest Additions ISO**

In the VirtualBox window top menu:

    Devices → Insert Guest Additions CD Image…

Wait a few seconds.

***

# **STEP 5 — Confirm where the ISO is mounted**

Your system already showed this:

    /run/media/$USER/VBox_GAs_7.2.6

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

# **STEP 6 — Run the Guest Additions Installer from the CORRECT path**

Since your ISO is auto‑mounted under `/run/media`, NOT `/mnt`, run:

```bash
sudo bash /run/media/$USER/VBox_GAs_7.2.6/VBoxLinuxAdditions.run
```

If you get a permissions error:

```bash
sudo chmod +x /run/media/$USER/VBox_GAs_7.2.6/VBoxLinuxAdditions.run
sudo /run/media/$USER/VBox_GAs_7.2.6/VBoxLinuxAdditions.run
```

Let it run completely.

You should see:

✔ Building kernel modules  
✔ Installing Guest Additions  
✔ No errors

***

# **STEP 7 — Reboot Kali**

```bash
sudo reboot
```

***

# **STEP 8 — Ensure you’re using X11 (NOT Wayland)**

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

# **STEP 9 — Enable Clipboard + Drag‑and‑Drop in VirtualBox settings**

### Shut down the VM → open VirtualBox settings:

    Settings → General → Advanced

Set:

*   **Shared Clipboard → Bidirectional**
*   **Drag and Drop → Bidirectional**

Click OK → start VM.

***

# **STEP 10 — Start DnD/Clipboard Services (Important)**

Inside Kali, run:

```bash
VBoxClient --clipboard &
VBoxClient --draganddrop &
```

No output = GOOD.

***

# **STEP 11 — TEST FUNCTIONALITY**

### Test copy/paste:

*   Copy text from host → paste in VM
*   Copy text from VM → paste to host

### Test drag-and-drop:

*   Drag a file from host → into VM
*   Drag a file from VM → to host desktop

No more timeout errors.

***

# **YOU ARE DONE**

If you followed all 11 steps, you now have:

✔ Drag & Drop working  
✔ Clipboard working  
✔ Guest Additions correctly installed  
✔ No VERR\_TIMEOUT  
✔ No “command not found”

***
```bash
lsmod | grep vbox
VBoxClient --version
echo $XDG_SESSION_TYPE
```

I will tell you EXACTLY what is wrong and fix it.
