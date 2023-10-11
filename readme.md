Enlaces de Interes por Tema

SnapShots LVM

Como funcionan los snapshots en VMWare https://www.computerweekly.com/es/consejo/Los-snapshots-de-VMware-Como-funcionan
Snapshot con LVM en readhat https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_logical_volumes/snapshot-of-logical-volumes_configuring-and-managing-logical-volumes#doc-wrapper
BOOM:administracion  de snapshots de forma automática https://www.redhat.com/en/blog/boom-booting-rhel-lvm-snapshots

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

[~]$ sudo vgdisplay
  --- Volume group ---
  VG Name               rhel
  System ID
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  26
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                10
  Open LV               9
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               118.89 GiB
  PE Size               4.00 MiB
  Total PE              30437
  Alloc PE / Size       28540 / 111.48 GiB
  Free  PE / Size       1897 / 7.41 GiB
  VG UUID               tDH...


[~]$ sudo lvdisplay
  --- Logical volume ---
  LV Name                pool00
  VG Name                rhel
  LV UUID                fgj...
  LV Write Access        read/write (activated read only)
  LV Creation host, time localhost, 2019-03-11 15:31:19 -0300
  LV Pool metadata       pool00_tmeta
  LV Pool data           pool00_tdata
  LV Status              available
  # open                 8
  LV Size                <23.59 GiB
  Allocated pool data    61.53%
  Allocated metadata     6.03%
  Current LE             6038
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:2

  --- Logical volume ---
  LV Path                /dev/rhel/root
  LV Name                root
  VG Name                rhel
  LV UUID                yuo...
  LV Write Access        read/write
  LV Creation host, time localhost, 2019-03-11 15:31:19 -0300
  LV Pool name           pool00
  LV Status              available
  # open                 1
  LV Size                <3.02 GiB
  Mapped size            98.14%
  Current LE             773
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:3
.
.
.

[~]$ sudo lvscan
  ACTIVE            '/dev/rhel/pool00' [<23.59 GiB] inherit
  ACTIVE            '/dev/rhel/root' [<3.02 GiB] inherit
  ACTIVE            '/dev/rhel/var' [<6.66 GiB] inherit
  ACTIVE            '/dev/rhel/home' [840.00 MiB] inherit
  ACTIVE            '/dev/rhel/var_log' [<4.66 GiB] inherit
  ACTIVE            '/dev/rhel/var_crash' [<4.66 GiB] inherit
  ACTIVE            '/dev/rhel/usr' [<5.42 GiB] inherit
  ACTIVE            '/dev/rhel/tmp' [956.00 MiB] inherit
  ACTIVE            '/dev/rhel/swap' [<7.52 GiB] inherit
  ACTIVE            '/dev/rhel/redis' [80.00 GiB] inherit

