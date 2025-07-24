# Administrators

## Helper Scripts

Type `sudo script-` and use tab completion to access the following helper programs.  

* `sudo script-user-add <username>`
* `sudo script-user-remove <username>`
  * Optionally backup the user home directory to the array before removal.
* `sudo script-user-reset-password <username>`
  * Reset a user's password if forgotten.
* `sudo script-user-reset-x2go <username>`
  * Completely reset the X2Go state for the user (destroys active/paused sessions).
* `sudo script-user-unban <ip_address>`
  * Temporarily unban an IP blocked by fail2ban.
* `sudo script-files-permissions-set <username> <password> [PATH ...]`
  * Set sane permissions on one or more paths, or the current directory if none provided.
* `sudo script-files-permissions-reset [PATH ...]`
  * Reset permissions on `/mnt/data` if no path is provided.
  * Use as a last resort to reset original permissions for shared data.
* `sudo script-system-scheduled-restart <OnCalendar>`
  * If `<OnCalendar>` not provided, defaults to `*-*-* 01:30:00` (1:30 AM).
  * See [Calendar Events](https://www.freedesktop.org/software/systemd/man/systemd.time.html) for formatting.
  * Alerts users via `notify-send` (X2Go), `wall` (SSH), and adds a reminder to the MOTD.
* `sudo script-user-reset-desktop <username>`
  * Reset a user’s desktop (MATE configuration) to default.
  * Can also be run in user mode (without `sudo`) for personal accounts.
* `sudo script-system-update`
  * Update the server using the system package manager.
  * Best to run prior to scheduled reboot.

## Cockpit Server Administration

Graphical system settings tool for monitoring and performing common tasks.

In an X2Go session, via a web browser at [`http://localhost:9090`](http://localhost:9090)

## Deploying `stow` server packages

Server scripts and configs are organized using [GNU Stow](https://www.gnu.org/software/stow/manual/stow.html) packages and can be deployed from [the root directory](../).

* Deploy system-wide MATE layout and themes: `sudo stow --adopt -R -t / theme`
* Deploy system-wide scripts: `sudo stow --adopt -R -t / scripts`
* Deploy system config files: `sudo stow --adopt -R -t / config`

## Service management

Login via `ssh` or `cat /etc/motd` to view current service statuses.

* Start service: `sudo systemctl start <service>`
* Stop service: `sudo systemctl stop <service>`
* Enable service at boot: `sudo systemctl enable <service>`
* Disable service at boot: `sudo systemctl disable <service>`
* Restart service: `sudo systemctl restart <service>`
* Reload systemd daemon: `sudo systemctl daemon-reload`
* Read service file: `sudo systemctl cat <service>`

## Virtual Machine Management

### Windows VMs

* Use `virt-manager` to create a new VM, you will be asked for your credentials in the GUI.
  * Optionally copy an existing Windows `.qcow2` image to avoid reinstalling Windows and virtio drivers.
  * If creating a new VM, Windows [virtio](https://fedoraproject.org/wiki/Windows_Virtio_Drivers) virtualization drivers available are at `/usr/share/virtio-win`.
* Activate Windows using the UAB license in elevated PowerShell:

  ```powershell
  slmgr -skms itis-msls.ad.uab.edu
  slmgr -ato
  ```

* Add the UAB DNS servers (`138.26.5.2`, `138.26.5.66`) to the Windows network config to access UAB resources.

#### Enable Access to Samba Share (Windows Bug Workaround)

1. Open `C:\Windows\system32\drivers\etc\hosts` and copy its contents.
2. Paste into a new text document and add the appropriate `blazerid` and server IP lines.
3. Save as `hosts` (no extension).
4. Copy the new hosts file to `C:\Windows\system32\drivers\etc\` (overwrite existing).
5. The user can now map/access their samba shares at `\\blazerid\data` and `\\blazerid\blazerid`.

#### Make an Existing Windows Account User an Administrator

1. Log in as the Azure AD user you want to make a local admin.
2. Log out and log in as a local admin.
3. In elevated PowerShell: `net localgroup administrators AzureAD\blazerid@uab.edu /add`

#### Enlarge VM Disk Space

* Add 20 GB to the Windows VM: `sudo qemu-img resize /var/lib/libvirt/images/win11-5900.qcow2 +20G`
* Add GParted ISO as boot device and expand the working partition.

## Fixing no local display

Periodically the GPU hardware resets and crashes the local display manager.

To fix, login via ssh and run: `sudo systemctl restart lightdm`
