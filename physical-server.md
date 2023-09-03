# Dislay size of disks
```
df -hT
```

# Mounting drive(s)
- Check all partitions `lsblk`
- If the output looks like the output below, and there are RAID entries in the TYPE column, you have a software RAID running:
```bash
root@rescue ~ # lsblk
NAME    MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
loop0     7:0    0     4G  1 loop
sda       8:0    0 447.1G  0 disk
├─sda1    8:1    0     4G  0 part
│ └─md0   9:0    0     4G  0 raid1
├─sda2    8:2    0   512M  0 part
│ └─md1   9:1    0 511.4M  0 raid1
└─sda3    8:3    0 442.6G  0 part
  └─md2   9:2    0 442.5G  0 raid1
sdb       8:16   0 447.1G  0 disk
├─sdb1    8:17   0     4G  0 part
│ └─md0   9:0    0     4G  0 raid1
├─sdb2    8:18   0   512M  0 part
│ └─md1   9:1    0 511.4M  0 raid1
└─sdb3    8:19   0 442.6G  0 part
  └─md2   9:2    0 442.5G  0 raid1
```

But if the output looks like the one below, there is no software RAID configured on it:

```bash
root@rescue ~ # lsblk
NAME    MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
loop0     7:0    0     4G  1 loop
sda       8:0    0 447.1G  0 disk
├─sda1    8:1    0     4G  0 part
├─sda2    8:2    0   512M  0 part
└─sda3    8:3    0 442.6G  0 part
sdb       8:16   0 447.1G  0 disk
└─sdb1    8:17   0   446G  0 part
```
- Now you can mount the correct partition within an empty folder, for example, using /mnt.

If you have a software RAID, /dev/md2 is usually the system partition. (Enter cat /proc/mdstat to display all RAID partitions):

`mount /dev/md2 /mnt`
Without a software RAID, usually the last or second-to-last partition contains the system:

`mount /dev/sda3 /mnt`

# Configurate RAID (Example RAID1)

## Basic Steps to Create Software RAID 1 on Linux
First you need to have a Linux distribution installed on your hard drive. In this tutorial we will name it /dev/sda.
Then you are going to grab two hard drives which will be named /dev/sdb and /dev/sdc in this post. These two hard drives can be of different sizes. Remember to back up your existing data before formating your hard drives.
Next, we will create special file systems on /dev/sdb and /dev/sdc.
And finally create the RAID 1 array using the mdadm utility.

### Step 1: Format Hard Drive
Insert two hard drives into your Linux computer, then open up a terminal window. Run the following command to check the device name.

`sudo fdisk -l`


You can see mine is /dev/sdb and /dev/sdc.

Then run the following 2 commands to make new MBR partition table on the two hard drives. (Note: this is going to wipe out all existing partitions and data from these two hard drives. Make sure your data is backed up.)

sudo parted /dev/sdb mklabel msdos

sudo parted /dev/sdc mklabel msdos
You can create GPT partition table by replacing msdos with gpt, but for the sake of compatibility, this tutorial will create MBR partition table.

Next, use the fdisk command to create a new partition on each drive and format them as a Linux raid autodetect file system. First do this on /dev/sdb.

sudo fdisk /dev/sdb
Follow these instructions.

Type `n` to create a new partition.
Type `p` to select primary partition.
Type `1` to create /dev/sdb1.
Press `Enter` to choose the default first sector
Press `Enter` to choose the default last sector. This partition will span across the entire drive.
Typing `p` will print information about the newly created partition. By default the partition type is Linux.
We need to change the partition type, so type t.
Enter `fd` to set partition type to Linux raid autodetect.
Type `p` again to check the partition type.
Type `w` to apply the above changes.
software raid 1 linux raid autodetect

Follow the same instruction to create a Linux raid autodetect partition on /dev/sdc.

Now we have two raid devices /dev/sdb1 and /dev/sdc1.

### Step 2: Install mdadm
mdadm is used for managing MD (multiple devices) devices, also known as Linux software RAID.

```
Debian/Ubuntu:     sudo apt install mdadm

CentOS/Redhat:     sudo yum install mdadm

SUSE:              sudo zypper install mdadm

Arch Linux         sudo pacman -S mdadm
Let’s examine the two devices.
```

`sudo mdadm --examine /dev/sdb /dev/sdc`


You can see that both are the type fd (Linux raid autodetect). At this stage, there’s no RAID setup on /dev/sdb1 and /dev/sdc1 which can be inferred with this command.

sudo mdadm --examine /dev/sdb1 /dev/sdc1
md superblock

### Step 3: Create RAID 1 Logical Drive
Execute the following command to create RAID 1. The logical drive will be named /dev/md0.

```
sudo mdadm --create /dev/md0 --level=mirror --raid-devices=2 /dev/sdb1 /dev/sdc1
```


Note: If you see this message: “Device or resource busy”, then you may need to reboot the OS.
Now we can check it with:

cat /proc/mdstat
linux software raid 1 set up

You can see that md0 is active and is a RAID 1 setup. To get more detailed information about /dev/md0, we can use the below commands:

`sudo mdadm --detail /dev/md0`


To obtain detailed information about each raid device, run this command:

`sudo mdadm --examine /dev/sdb1 /dev/sdc1`
linux raid devices

### Step 4: Create File System on the RAID 1 Logical Drive
Let’s format it to ext4 file system.

`sudo mkfs.ext4 /dev/md0`
Then create a mount point /mnt/raid1 and mount the RAID 1 drive.

`sudo mkdir /mnt/raid1`

`sudo mount /dev/md0 /mnt/raid1`
You can use this command to check how much disk space you have.

`df -h /mnt/raid1`


