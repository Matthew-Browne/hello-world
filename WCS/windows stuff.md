### RAID 1
```
   38  sudo fdisk /dev/sdb
   39  sudo fdisk /dev/sdc
   40  lsblk
   41  sudo mdadm --create /dev/md0 --level 1 --raid-devices 2 /dev/sdb1 /dev/sdc1
   42  cat /proc/mdstat
   43  sudo mkfs.ext4 /dev/md0 -L "PersonalData"
   44  lsblk -f
   45  sudo mkdir /home/matthew/Data-RAID1 -p
   46  sudo mount /dev/md0 /home/matthew/Data-RAID1/
   47  sudo vim /etc/fstab
   48  sudo mdadm --detail --scan
   49  sudo vim /etc/mdadm/mdadm.conf
   50  sudo update-initramfs -u
   51  history
```
//redémarrage, création du dossier et du fichier de test
```
   38  cd Data-RAID1/
   39  sudo mkdir test
   40  sudo chmod 744 ./test
   41  sudo echo "test" > ./test/fich.txt
   42  sudo chmod 744 fich.txt 
   43  ls -l
   44  history
```
// redémarrage, suppression du deuxième disque dans virtualbox
```
   38  cd Data-RAID1/
   39  ls
   40  vim /test/fich.txt
   41  sudo touch /test/fich2.txt
   42  sudo mdadm --manage /dev/md0 --add /dev/sdd1
   43  lsblk
   44  sudo mdadm --detail /dev/md0
   45  history
```
### RAID 0
```
   38  sudo fdisk /dev/sde
   39  sudo fdisk /dev/sdf
   40  lsblk
   41  sudo mdadm --create /dev/md1 --level 0 --raid-devices 2 /dev/sde1 /dev/sdf1
   42  lsblk
   43  cat /proc/mdstat
   44  sudo mkfs.ext4 /dev/md1 -L "SecondaryData"
   45  lsblk -f
   46  sudo mkdir /home/matthew/Data-RAID0 -p
   47  sudo mount /dev/md1 /home/matthew/Data-RAID0/
   48  sudo vim /etc/fstab
   49  sudo mdadm --detail --scan
   50  sudo vim /etc/mdadm/mdadm.conf
   51  sudo update-initramfs -u
   52  history
```
   //redémarrage, vérification finale
```
   38  sudo mdadm --detail --scan
   39  lsblk -f
   40  history
```
