## **Step 1: Download the Ubuntu Cloud Image**

First, download the official Ubuntu 24.04 minimal cloud image:

```bash
wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img
```

## **Step 2: Create a New VM with Proper Hardware Settings**

Replace `vmbr0` with your bridge name if different, and adjust VMID or name as needed.

```bash
qm create 200 \
  --name ubuntu-2404 \
  --memory 8192 \
  --cores 6 \
  --cpu host \
  --machine q35 \
  --bios ovmf \
  --net0 virtio,bridge=vmbr0 \
  --agent enabled=1 \
  --onboot 1
```

## **Step 3: Import the Cloud Image as a ZFS Disk**

Import the downloaded image as the VM's first disk (thin provisioned on ZFS):

```bash
qm importdisk 200 noble-server-cloudimg-amd64.img local-zfs
```

## **Step 4: Attach Disks (System, EFI, Cloud-Init) and Set Disk Parameters**

Attach imported disk as SCSI (with 50GB size!) and add EFI & Cloud-Init drives:

```bash
qm set 200 \
  --scsihw virtio-scsi-single \
  --scsi0 local-zfs:vm-200-disk-0,discard=on,iothread=1,ssd=1,backup=1,size=50G \ #MAYBE BROKEN THE 50G part?
  --efidisk0 local-zfs:1 \
  --ide2 local-zfs:cloudinit
```
OR
```bash
qm set 200 \
  --scsihw virtio-scsi-single \
  --scsi0 local-zfs:vm-200-disk-0,discard=on,iothread=1,ssd=1,backup=1 \
  --efidisk0 local-zfs:1 \
  --ide2 local-zfs:cloudinit

qm resize 200 scsi0 50G
```

## **Step 5: Set Boot Order**

```bash
qm set 200 --boot c --bootdisk scsi0
```