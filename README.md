## Aenigma Vagrant Boxes

Ready-to-run virtual images for Aenigma, built for both **VirtualBox** and **libvirt/QEMU**.

### Step 1: Install Vagrant

Download and install Vagrant from the official page for your operating system:
https://developer.hashicorp.com/vagrant/downloads

### Step 2: Install a virtualization provider

You only need **one** of the following, depending on your operating system and preference.

#### Option A: VirtualBox (easiest — works on Windows, macOS, and Linux)

1. Go to: https://www.oracle.com/virtualization/technologies/vm/downloads/virtualbox-downloads.html
2. Download the installer for your operating system.
3. Run the installer and follow the on-screen instructions (default options are fine).

#### Option B: QEMU/libvirt (Linux only)

If you're on Ubuntu or Debian, open a terminal and run:

```bash
sudo apt update
sudo apt install -y qemu-system-x86 libvirt-daemon-system libvirt-clients bridge-utils virt-manager libvirt-dev
```

> Use `qemu-system-arm` instead of `qemu-system-x86` if you're on an ARM64 machine
> (e.g. Apple Silicon running Linux). On newer Debian/Ubuntu releases, the older
> `qemu-kvm` package name no longer works directly — it now points to one of these
> architecture-specific packages, so you need to pick the right one yourself.

> `libvirt-dev` is required to install the `vagrant-libvirt` plugin below — without it,
> the plugin install fails with a missing-library error. On Fedora/RHEL-based distros
> the equivalent package is called `libvirt-devel` instead.

This also installs **virt-manager**, a graphical tool for managing your VMs
(start/stop/pause, view the VM's screen, check resource usage) if you'd rather not use
the terminal after setup. You can open it any time by running `virt-manager` or finding
"Virtual Machine Manager" in your applications menu.

Then add yourself to the `libvirt` group so you don't need `sudo` for every command,
and apply it:

```bash
sudo usermod -aG libvirt $(whoami)
newgrp libvirt
```

> If you're not on Ubuntu/Debian (e.g. Fedora, Arch), install the equivalent packages
> using your distro's package manager (`dnf`, `pacman`, etc.) — search "install libvirt
> qemu-kvm \<your distro\>" if unsure.

Finally, install the Vagrant plugin that lets Vagrant talk to libvirt (Vagrant doesn't
support it out of the box like it does VirtualBox):

```bash
vagrant plugin install vagrant-libvirt
```

### Step 3: Get the box running

You have two options — pick whichever is easier for you.

#### Option A: Clone the repository (simplest)

Download the repository using this
[link](https://github.com/m3sserschmitt/aenigma-boxes/archive/refs/heads/gh-pages.zip)

or, using git:

```bash
git clone https://github.com/m3sserschmitt/aenigma-boxes.git
cd aenigma-boxes
vagrant up
```

The repository already includes a ready-made `Vagrantfile`, so this is all you need to
do.

#### Option B: Create your own project folder

1. Create a new empty folder anywhere on your computer.
2. Inside it, create a file named exactly `Vagrantfile` (no file extension) with this
content:

    ```ruby
      Vagrant.configure("2") do |config|
        config.vm.box     = "m3sserschmitt/aenigma5"
        config.vm.box_url = "https://boxes.aenigma.ro/metadata.json"
        config.vm.define  "aenigma5"

        config.vm.provider "virtualbox" do |vb|
          vb.name   = "aenigma5"
          vb.memory = 2048
          vb.cpus   = 2
        end

        config.vm.provider "libvirt" do |lv|
          lv.default_prefix = ""
          lv.memory = 2048
          lv.cpus   = 2
        end

        config.vm.synced_folder ".", "/vagrant", disabled: true
      end
    ```

    > `vb.name` / `lv.default_prefix` + `config.vm.define` control the name shown in
    > VirtualBox Manager or virt-manager — without these, the VM name defaults to
    > something like your project folder name plus "default", which isn't very
    > descriptive.

3. Open a terminal in that folder and run:

    ```bash
    vagrant up
    ```

Either way, Vagrant will automatically detect whichever provider you installed
(VirtualBox or libvirt) and download the matching image the first time you run it —
this may take a few minutes depending on your internet connection.

### Connecting to the VM

Once `vagrant up` finishes, you can log into the VM's command line with:

```bash
vagrant ssh
```

This drops you into a shell running inside the VM, as if you'd SSH'd into a remote
server. From there you can run commands, inspect files, start services, etc. —
everything happens inside the isolated VM, not on your actual computer.

To leave the VM's shell and return to your own terminal, either run `exit` or press
`Ctrl+D`. This does **not** stop the VM — it keeps running in the background so you can
`vagrant ssh` back in any time.

A few other commands you'll likely want:

```bash
vagrant halt      # shut the VM down (keeps it on disk for later)
vagrant up        # start it again
vagrant destroy   # delete the VM entirely (removes its disk too)
```

> Run these from the same folder as the `Vagrantfile` — Vagrant uses that folder to
> know which VM you're referring to.

### Updating to a newer version later

```bash
vagrant box update
```

### Troubleshooting tips

- If you installed **both** VirtualBox and libvirt, you can force which one to use:
  ```bash
  vagrant up --provider=virtualbox
  # or
  vagrant up --provider=libvirt
  ```
- On Linux, if `vagrant up` complains about permissions with libvirt, make sure you ran
`newgrp libvirt` (or log out and back in) after the `usermod` step above.

- **virt-manager fails to connect to `qemu:///system` the first time you open it** —
this usually happens because your user's `libvirt` group membership (from the `usermod`
step) hasn't been picked up by your desktop session yet, even if it worked in a terminal
via `newgrp`. Logging out and back in (or rebooting) refreshes your full session's group
membership and typically resolves it. If it persists after that, try:
  - Confirming the `libvirtd` service is actually running: `sudo systemctl status
  libvirtd`
  - Manually adding the connection in virt-manager via **File → Add Connection…**,
  selecting **QEMU/KVM**, and leaving "Connect to remote host" unchecked
  - Double-checking your user is really in the group: `groups $(whoami)` should list
  `libvirt`

### License

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository is licensed under the MIT License. Feel free to copy, modify and
distribute it - see the [LICENSE](LICENSE) file for details.

### Contact

You can report errors or suggest improvements at [contact@aenigma.ro](mailto:contact@aenigma.ro)
