## Installing / removing amazon-linux-extras packages

[Usage](https://docs.amazonaws.cn/en_us/AWSEC2/latest/UserGuide/amazon-linux-ami-basics.html)

Listing:

```bash
amazon-linux-extras list
```

Installing:

```bash
sudo amazon-linux-extras install <topic>
```

Removing:

```bash
sudo yum remove $(yum list installed | grep amzn2extra-<topic> | awk '{ print $q }')
```

## Mounting a drive

[Attaching & mounting ebs to ec2](https://devopscube.com/mount-ebs-volume-ec2-instance/)

Manual mounting:

```bash
# check devices
lsblk

# check whether volume is empty
sudo file -s /dev/xvdf
# "/dev/xvdf: data" === empty

# make a filesystem on  the volume
sudo mkfs -t ext4 /dev/xvdf

# create a mountpoint
sudo mkdir /mnt/my-apps-data
# set owner of the mountpoint (username:group)
sudo chown ec2-user:ec2-user /mnt/my-apps-data

# mount
sudo mount /dev/xvdf /mnt/my-apps-data

# unmount
umount /dev/xvdf
```


Auto mounting:

```bash
# back up fstab
sudo cp /etc/fstab /etc/fstab.bak

# open /etc/fstab
vim /etc/fstab

# make an entry following the format:
# device_name mount_point file_system_type fs_mntops fs_freq fs_passno
# for example:
# /dev/xvdf       /newvolume   ext4    defaults,nofail        0       0
# check fstab for errors

sudo mount -a
```


Changing ownership of mounted drive:

```sh
# https://superuser.com/questions/320415/mount-device-with-specific-user-rights
# fresh drive with nothing on it
# (username:group) => (ec2-user:ec2-user)
chown -R username:group /mountpoint
find /mountpoint -type d -exec chmod g+ws {} \;
```


### Finding yur own ip adress

```bash
# https://www.cyberciti.biz/faq/how-to-find-public-ip-address-aws-ec2-or-lightsail-vm/

dig +short myip.opendns.com @resolver1.opendns.com
#  3.25.174.224

dig TXT +short o-o.myaddr.l.google.com @ns1.google.com                                                                                     <<<
# "3.25.174.224"

curl http://checkip.amazonaws.com
# 3.25.174.224
```
