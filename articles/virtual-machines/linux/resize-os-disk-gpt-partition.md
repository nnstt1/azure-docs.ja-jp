---
title: GPT パーティションがある OS ディスクのサイズを変更する
description: この記事では、Linux で GUID パーティション テーブル (GPT) パーティションがある OS ディスクのサイズを変更する方法について説明します。
ms.topic: how-to
author: kailashmsft
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: ca7a018accb0410c2656d7aeb4282b42f2756e39
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688225"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>GPT パーティションがある OS ディスクのサイズを変更する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

> [!NOTE]
> この記事は、GUID パーティション テーブル (GPT) パーティションを持つ OS ディスクにのみ適用されます。

この記事では、Linux で GPT パーティションを含む OS ディスクのサイズを増やす方法について説明します。 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>OS ディスクに MBR または GPT パーティションがあるかどうかを確認する

`parted` コマンドを使用して、ディスク パーティションがマスター ブート レコード (MBR) パーティションまたは GPT パーティションのどちらで作成されているかを特定します。

### <a name="mbr-partition"></a>MBR パーティション

次の出力では、**Partition Table** に **msdos** の値が示されています。 この値は、MBR パーティションを識別します。

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT パーティション

次の出力では、**Partition Table** に **gpt** の値が示されています。 この値は、GPT パーティションを識別します。

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

仮想マシン (VM) の OS ディスク上に GPT パーティションがある場合、OS ディスクのサイズを増やします。

## <a name="increase-the-size-of-the-os-disk"></a>OS ディスクのサイズを増やす

次の手順は、Linux での動作保証済みディストリビューションに適用されます。

> [!NOTE]
> 続行する前に、VM のバックアップ コピーを作成するか、OS ディスクのスナップショットを取得してください。

### <a name="ubuntu"></a>Ubuntu

Ubuntu 16.*x* および 18.*x* で OS ディスクのサイズを増やすには、次の操作を実行します。

1. VM を停止します。
1. ポータルから OS ディスクのサイズを増やします。
1. VM を再起動し、**ルート** ユーザーとして VM にサインインします。
1. OS ディスクに増加したファイル システム サイズが表示されていることを確認します。

次の例では、ポータルを使用して OS ディスクのサイズが 100 GB に変更されています。 **/** にマウントされた **/dev/sda1** ファイル システムには 97 GB と表示されます。

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Suse 12 SP4、SUSE SLES 12 for SAP、SUSE SLES 15、および SUSE SLES 15 for SAP で OS ディスクのサイズを増やすには、次の操作を実行します。

1. VM を停止します。
1. ポータルから OS ディスクのサイズを増やします。
1. VM を再起動します。

VM が再起動したら、次の手順のようにします。

1. 次のコマンドを使用し、**ルート** ユーザーとして VM にアクセスします。

   ```
   # sudo -i
   ```

1. 次のコマンドを使用して、**growpart** パッケージをインストールします。これを使用してパーティションのサイズを変更します。

   ```
   # zypper install growpart
   ```

1. `lsblk` コマンドを使用して、ファイル システムのルート ( **/** ) にマウントされているパーティションを見つけます。 この場合、デバイス **sda** のパーティション 4 が **/** にマウントされていることがわかります。

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. `growpart` コマンドと、前のステップで特定したパーティション番号を使用して、必要なパーティションのサイズを変更します。

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. `lsblk` コマンドをもう一度実行して、パーティションが増加したかどうかを確認します。

   次の出力は、 **/dev/sda4** パーティションのサイズが 46.5 GB に変更されたことを示します。
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. `-f` フラグを指定した `lsblk` コマンドを使用して、OS ディスク上のファイル システムの種類を識別します。

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. ファイル システムの種類に基づいて、適切なコマンドを使用してファイル システムのサイズを変更します。
   
   **xfs** の場合、次のコマンドを使用します。
   
   ```
   #xfs_growfs /
   ```
   
   出力例:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   **ext4** の場合、次のコマンドを使用します。
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. 次のコマンドを使用して、**df-Th** のファイル システムの増加したサイズを確認します。
   
   ```
   #df -Thl
   ```
   
   出力例:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   前の例では、OS ディスクのファイル システム サイズが増加していることがわかります。

### <a name="rhel-with-lvm"></a>LVM を持つ RHEL

1. 次のコマンドを使用し、**ルート** ユーザーとして VM にアクセスします。

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. `lsblk` コマンドを使用して、ファイル システムのルート ( **/** ) にマウントされている論理ボリューム (LV) を確認します。 この例では、**rootvg-rootlv** が **/** にマウントされていることがわかります。 別のファイル システムが必要な場合は、この記事全体で LV とマウント ポイントに置き換えてください。

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. ルート パーティションが含まれる LVM ボリューム グループ (VG) に空き領域があるかどうかを確認します。 空き領域がある場合は、ステップ 12 に進みます。

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   この例の **Free  PE / Size** の行では、ボリューム グループに空き容量が 38.02 GB あることが示されています。 ボリューム グループに領域を追加する前に、ディスクのサイズを変更する必要はありません。

1. LVM で RHEL 7.*x* の OS ディスクのサイズを増やすには:

   1. VM を停止します。
   1. ポータルから OS ディスクのサイズを増やします。
   1. VM を起動します。

1. VM が再起動されたら、次の手順を実行します。

   - **cloud-utils-growpart** パッケージをインストールして、**growpart** コマンドを使えるようにします。これは、OS ディスクおよび GPT ディスク レイアウト用の gdisk ハンドラーのサイズを増やすために必要です。 これらのパッケージは、ほとんどの Marketplace イメージにプレインストールされています。

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. `pvscan` コマンドを使用して、**rootvg** という名前のボリューム グループ内の LVM 物理ボリューム (PV) が保持されているディスクとパーティションを特定します。 角かっこ ( **[** と **]** ) の間に表示されているサイズと空き領域を記録しておきます。

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. `lsblk` を使用して、パーティションのサイズを確認します。 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. `growpart`、デバイス名、パーティション番号を使用して、この PV が含まれるパーティションを拡張します。 これにより、デバイス上のすべての連続した空き領域を使用するように、指定したパーティションが拡張されます。

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. 再び `lsblk` コマンドを使用して、パーティションのサイズが予想されるサイズに変更されたことを確認します。 この例では、**sda4** が 63 GB から 95 GB に変更されていることにご注意ください。

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. 新しく拡張されたパーティションの残りの部分を使用するように、PV を拡張します。

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. PV の新しいサイズが予想されるサイズであることを確認し、元の **[サイズ/空き]** の値と比較します。

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. 対象の論理ボリューム (LV) を必要な量だけ拡大します。 量はボリューム グループ内のすべての空き領域でなくてもかまいません。 次の例では、 **/dev/mapper/rootvg-rootlv** のサイズを 2 GB から 12 GB (10 GB の増加) に変更しています。 このコマンドでファイル システムのサイズも変更されます。

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   出力例:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. `lvresize` コマンドにより、LV 内のファイル システムに対して適切なサイズ変更コマンドが自動的に呼び出されます。 次のコマンドを使用して、 **/** にマウントされている **/dev/mapper/rootvg-rootlv** のファイル システムのサイズが増加しているかどうかを調べます。

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   出力例:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> 同じ手順を使用して他の論理ボリュームのサイズを変更するには、ステップ 12 で LV の名前を変更します。


### <a name="rhel-raw"></a>RHEL RAW

RHEL の RAW パーティションで OS ディスクのサイズを増やすには:

1. VM を停止します。
1. ポータルから OS ディスクのサイズを増やします。
1. VM を起動します。

VM が再起動したら、次の手順のようにします。

1. 次のコマンドを使用して、**ルート** ユーザーとして VM にアクセスします。

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. VM が再起動されたら、次の手順を実行します。

   - **cloud-utils-growpart** パッケージをインストールして、**growpart** コマンドを使えるようにします。これは、OS ディスクおよび GPT ディスク レイアウト用の gdisk ハンドラーのサイズを増やすために必要です。 このパッケージは、ほとんどの Marketplace イメージにプレインストールされています。

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. **lsblk -f** コマンドを使用して、ルート ( **/** ) パーティションを保持しているパーティションおよびファイルシステムの種類を確認します。

   ```bash
   [root@vm-dd-cent7 ~]# lsblk -f
   NAME    FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1  xfs          2a7bb59d-6a71-4841-a3c6-cba23413a5d2 /boot
   ├─sda2  xfs          148be922-e3ec-43b5-8705-69786b522b05 /
   ├─sda14
   └─sda15 vfat         788D-DC65                            /boot/efi
   sdb
   └─sdb1  ext4         923f51ff-acbd-4b91-b01b-c56140920098 /mnt/resource
   ```

1. 確認のために、まず **gdisk** を使用して sda ディスクのパーティション テーブルを一覧表示します。 この例では、29.0 GiB のパーティション 2 を持つ 48 GB のディスクがあることがわかります。 このディスクは、Azure portal で 30 GB から 48 GB に拡張されています。

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 62914526
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 6076 sectors (3.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048        62912511   29.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. **growpart** コマンドを使用して、ルートのパーティション (この場合は sda2) を拡張します。 このコマンドを使用すると、パーティションが拡張され、ディスク上のすべての連続した領域が使用されます。

   ```bash
   [root@vm-dd-cent7 ~]# growpart /dev/sda 2
   CHANGED: partition=2 start=2050048 old: size=60862464 end=62912512 new: size=98613214 end=100663262
   ```

1. 次に、**gdisk** を使用して新しいパーティション テーブルを再度出力します。  パーティション 2 が 47.0 GiB に拡張されていることに注目してください。

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 100663262
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 4062 sectors (2.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048       100663261   47.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. **xfs_growfs** を使用して、パーティション上のファイルシステムを拡張します。これは、Marketplace で生成された標準の RedHat システムに適しています。

   ```bash
   [root@vm-dd-cent7 ~]# xfs_growfs /
   meta-data=/dev/sda2              isize=512    agcount=4, agsize=1901952 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=7607808, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3714, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7607808 to 12326651
   ```

1. 次のように、**df** コマンドを使用して、新しいサイズが反映されていることを確認します。

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
