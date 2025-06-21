# Proxmox
For the disk size issue, possible tools:
- clone and then remove snapshots
- fstrim -av, zero empty space

Maybe Ansible + a user on proxmox with only qemu permissions is the way to automate virtual machine deployment

Graphics card, if using gui probably set to SPICE (QXL)
Machine set to q35 (this seems to cause shpchp pci_hp_register failed with error -16)
Check Qemu agent if that is going to be installed
VirtIO SCSI single / SCSI should be the default controller and hdd settings with IO thread
Check ssd emulation and discard (might then need to manually run fstrim -a)
x86-64-v2-AES cpu says support begins with westmere (r710)
Can check numa (Try to have all vCPUs on the same physical socket and only use memory that is physically connected to that socket)
Under options Spice Enhancements allows for folder sharing
Under hardware add usb spice port to allow for spice usb attachement. Add audio device for audio

Default backup location is /var/lib/vz/dump/
The filename matters or restore won't recognize the backup file

Had to do this to get nvr-2 working after a reboot (see firefox bookmark)
```
lvchange -an nvr-2/nvr-2_tdata
lvchange -an nvr-2/nvr-2_tmeta
lvchange -ay nvr-2/nvr-2
```

## Network
/etc/network/interfaces
ifreload -a
See interfaces.supermicro and interfaces.supermicro.switch
### Time
Followed the README
`echo 'server host.address.net iburst prefer' > /etc/chrony/sources.d/local-ntp-server.sources`
`chronyc reload sources`
verify - `chronyc sources`

## Updates
Select the node, go to updates->repositories, disable the enterprise repositories, add the no-subscription repository, then add the ceph no-subscription repsoitory (I choose the version corresponding to the enterprise one that was enabled by default)
Then upgrade via GUI (always uses apt-get dist-upgrade)
### non-free-firmware
vim /etc/apt/sources.list
append `non-free-firmware` (component) to the end of each of the .debian.org repository lines
Enable microcode updates:
- Intel CPUs: apt install intel-microcode
  `grep microcode /proc/cpuinfo | uniq`
  `dmesg | grep microcode`
- AMD CPUs: apt install amd64-microcode
## Misc
Powerpanel is installed with `apt install ./PowerPanel.deb` and should just work. Check with pwrstat -config or -status.
`pwrstat -pwrfail -delay 180 -active on -cmd /etc/pwrstatd-powerfail.sh -duration 1 -shutdown on`

The subscription notices lives in /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js (backup first). Search for "No valid subscription"

# Guests

## Linux Guests
apt install qemu-guest-agent
apt install spice-vdagent
apt install spice-webdavd for folder sharing

## Windows Guests
https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers
https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/?C=M;O=D
- Looks like the virtio installer includes spice and qemu
~~https://www.spice-space.org/download/windows/spice-guest-tools/spice-guest-tools-latest.exe~~