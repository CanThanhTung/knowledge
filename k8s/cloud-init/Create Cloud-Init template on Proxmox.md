# Download the ISO image
You can download the Ubuntu ISO image from [Ubuntu Cloud images](https://cloud-images.ubuntu.com/)

I tested on https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img

# Create Proxmox VM via CLI

Create a new VM with VirtIO SCSI controller
```
qm create 5000 --memory 2048 --core 1 --name ubuntu-cloud --net0 virtio,bridge=vmbr0 --scsihw virtio-scsi-pci
# or
qm create 5000 --machine q35 --bios ovmf --memory 2048 --core 1 --name ubuntu-cloud --net0 virtio,bridge=vmbr0 --scsihw virtio-scsi-pci
```

Import the download disk to the local-lvm storage, attaching it as a SCSI drive
```
qm set 5000 --scsi0 local-btrfs:0,import-from=/var/lib/pve/local-btrfs/template/iso/jammy-server-cloudimg-amd64.img
```

Add Cloud-Init CD-ROM drive
```
qm set 5000 --ide2 local-btrfs:cloudinit
qm set 5000 --boot order=scsi0
qm set 5000 --serial0 socket --vga serial0
```

# Create full clone template
```
qm templete 5000
```

References:
- https://pve.proxmox.com/wiki/Cloud-Init_Support