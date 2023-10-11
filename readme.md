Enlaces de Interes por Tema

SnapShots LVM

Como funcionan los snapshots en VMWare https://www.computerweekly.com/es/consejo/Los-snapshots-de-VMware-Como-funcionan
Snapshot con LVM en readhat https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_logical_volumes/snapshot-of-logical-volumes_configuring-and-managing-logical-volumes#doc-wrapper

Ver discos actuales:
$ fdisk -l
$ df -h
$ pvs

[~]$ sudo pvs
  PV         VG   Fmt  Attr PSize   PFree
  /dev/sda2  rhel lvm2 a--  <38.90g 7.41g
  /dev/sdb1  rhel lvm2 a--  <80.00g    0
[~]$ sudo pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               rhel
  PV Size               38.90 GiB / not usable 3.00 MiB
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              9958
  Free PE               1897
  Allocated PE          8061
  PV UUID               Ohqc...

  --- Physical volume ---
  PV Name               /dev/sdb1
  VG Name               rhel
  PV Size               <80.00 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              20479
  Free PE               0
  Allocated PE          20479
  PV UUID               OA6...

  $lsblk
  NAME                    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0                       2:0    1    4K  0 disk
sda                       8:0    0   40G  0 disk
├─sda1                    8:1    0  238M  0 part /boot
└─sda2                    8:2    0 38.9G  0 part
  ├─rhel-pool00_tmeta   253:0    0  384M  0 lvm
  │ └─rhel-pool00-tpool 253:2    0 23.6G  0 lvm
  │   ├─rhel-root       253:3    0    3G  0 lvm  /
  │   ├─rhel-usr        253:5    0  5.4G  0 lvm  /usr
  │   ├─rhel-pool00     253:6    0 23.6G  1 lvm
  │   ├─rhel-var        253:7    0  6.7G  0 lvm  /var
  │   ├─rhel-home       253:8    0  840M  0 lvm  /home
  │   ├─rhel-var_log    253:9    0  4.7G  0 lvm  /var/log
  │   ├─rhel-var_crash  253:10   0  4.7G  0 lvm  /var/crash
  │   └─rhel-tmp        253:11   0  956M  0 lvm  /tmp
  ├─rhel-pool00_tdata   253:1    0 23.6G  0 lvm
  │ └─rhel-pool00-tpool 253:2    0 23.6G  0 lvm
  │   ├─rhel-root       253:3    0    3G  0 lvm  /
  │   ├─rhel-usr        253:5    0  5.4G  0 lvm  /usr
  │   ├─rhel-pool00     253:6    0 23.6G  1 lvm
  │   ├─rhel-var        253:7    0  6.7G  0 lvm  /var
  │   ├─rhel-home       253:8    0  840M  0 lvm  /home
  │   ├─rhel-var_log    253:9    0  4.7G  0 lvm  /var/log
  │   ├─rhel-var_crash  253:10   0  4.7G  0 lvm  /var/crash
  │   └─rhel-tmp        253:11   0  956M  0 lvm  /tmp
  ├─rhel-swap           253:4    0  7.5G  0 lvm  [SWAP]
  └─rhel-redis          253:12   0   80G  0 lvm  /var/redis
sdb                       8:16   0   80G  0 disk
└─sdb1                    8:17   0   80G  0 part
  └─rhel-redis          253:12   0   80G  0 lvm  /var/redis
sr0                      11:0    1  4.2G  0 rom

[~]$ sudo pvscan
  PV /dev/sda2   VG rhel            lvm2 [<38.90 GiB / 7.41 GiB free]
  PV /dev/sdb1   VG rhel            lvm2 [<80.00 GiB / 0    free]
  Total: 2 [118.89 GiB] / in use: 2 [118.89 GiB] / in no VG: 0 [0   

