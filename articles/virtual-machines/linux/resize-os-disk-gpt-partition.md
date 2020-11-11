---
title: GPT パーティションがある OS ディスクのサイズを変更する |Microsoft Docs
description: この記事では、GPT パーティションがある OS ディスクのサイズを変更する手順について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: baa260e911673ea99b292ab5dc9895840d0098ef
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340309"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>GPT パーティションがある OS ディスクのサイズを変更する

> [!NOTE]
> このシナリオは、GUID パーティション テーブル (GPT) パーティションを持つ OS ディスクにのみ適用されます。

この記事では、Linux で GPT パーティションを含む OS ディスクのサイズを増やす方法について説明します。 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>OS ディスクに MBR または GPT パーティションがあるかどうかを確認する

`parted` コマンドを使用して、ディスク パーティションがマスター ブート レコード (MBR) パーティションまたは GPT パーティションのどちらで作成されているかを特定します。

### <a name="mbr-partition"></a>MBR パーティション

次の出力では、 **Partition Table** に **msdos** の値が示されています。 この値は、MBR パーティションを識別します。

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

次の出力では、 **Partition Table** に **gpt** の値が示されています。 この値は、GPT パーティションを識別します。

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

Ubuntu 16.x および 18.x で OS ディスクのサイズを増やすには、次の操作を実行します。

1. VM を停止します。
1. ポータルから OS ディスクのサイズを増やします。
1. VM を再起動し、 **root** ユーザーとして VM にログインします。
1. OS ディスクに増加したファイル システム サイズが表示されていることを確認します。

次の例に示すように、ポータルから OS ディスクのサイズが 100 GB に変更されています。 **/** にマウントされた **/dev/sda1** ファイル システムには 97 GB と表示されます。

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

VM が再起動されたら、次の手順を実行します。

1. 次のコマンドを使用して、 **ルート** ユーザーとして VM にアクセスします。

   ```
   # sudo -i
   ```

1. 次のコマンドを使用して **growpart** パッケージをインストールします。これはパーティションのサイズ変更に使用されます。

   ```
   # zypper install growpart
   ```

1. `lsblk` コマンドを使用して、ファイル システムのルート ("/") にマウントされているパーティションを見つけます。 この場合、デバイス sda のパーティション 4 が / にマウントされていることがわかります。

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

1. 前の手順で見つかったパーティション番号を使用して `growpart` コマンドを使用し、必要なパーティションのサイズを変更します。

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
   
1. 次のコマンドを使用して、 **df-Th** のファイル システムの増加したサイズを確認します。
   
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

### <a name="rhel-lvm"></a>RHEL LVM

LVM で RHEL 7.x の OS ディスクのサイズを増やすには、次の操作を実行します。

1. VM を停止します。
1. ポータルから OS ディスクのサイズを増やします。
1. VM を起動します。

VM が再起動されたら、次の手順を実行します。

1. 次のコマンドを使用して、 **ルート** ユーザーとして VM にアクセスします。
 
   ```
   #sudo su
   ```

1. **gptfdisk** パッケージをインストールします。これは、OS ディスクのサイズを増やすために必要です。

   ```
   #yum install gdisk -y
   ```

1. ディスクで使用可能な最大セクターを表示するには、次のコマンドを実行します。

   ```
   #sgdisk -e /dev/sda
   ```

1. 次のコマンドを使用して、パーティションを削除せずにサイズを変更します。 **parted** コマンドには **resizepart** という名前のオプションがあり、パーティションを削除せずにサイズを変更できます。 **resizepart** の後の 4 の数字は、4 番目のパーティションのサイズを変更することを示しています。

   ```
   #parted -s /dev/sda "resizepart 4 -1" quit
   ```
    
1. 次のコマンドを実行して、パーティションが増加したことを確認します。

   ```
   #lsblk
   ```

   次の出力は、 **/dev/sda4** パーティションのサイズが 99 GB に変更されたことを示します。

   ```
   [user@myvm ~]# lsblk
   NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   fd0                 2:0    1    4K  0 disk
   sda                 8:0    0  100G  0 disk
   ├─sda1              8:1    0  500M  0 part /boot/efi
   ├─sda2              8:2    0  500M  0 part /boot
   ├─sda3              8:3    0    2M  0 part
   └─sda4              8:4    0   99G  0 part
   ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
   ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
   ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
   ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
   ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
   └─rootvg-rootlv   253:5    0    2G  0 lvm  /
   sdb                 8:16   0   50G  0 disk
   └─sdb1              8:17   0   50G  0 part /mnt/resource
   ```

1. 次のコマンドを使用して、物理ボリューム (PV) のサイズを変更します。

   ```
   #pvresize /dev/sda4
   ```

   次の出力は、PV のサイズが 99.02 GB に変更されたことを示します。

   ```
   [user@myvm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   [user@myvm ~]# pvs
   PV         VG     Fmt  Attr PSize   PFree
   /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
   ```

1. 次の例では、次のコマンドを使用して、 **/dev/mapper/rootvg-rootlv** のサイズを 2 GB から 12 GB (10 GB の増加) に変更しています。 このコマンドでファイル システムのサイズも変更されます。

   ```
   #lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   出力例:

   ```
   [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
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
         
1. 次のコマンドを使用して、 **/dev/mapper/rootvg-rootlv** のファイル システムのサイズが増加しているかどうかを確認します。

   ```
   #df -Th /
   ```

   出力例:

   ```
   [user@myvm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [user@myvm ~]#
   ```

> [!NOTE]
> 同じ手順を使用して他の論理ボリュームのサイズを変更するには、手順 7 で **lv** 名を変更します。

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE]
>OS ディスクのサイズを増やす前に、常に VM のスナップショットを取得してください。

RAW パーティションで RHEL の OS ディスクのサイズを増やすには、次の操作を実行します。

VM を停止します。
ポータルから OS ディスクのサイズを増やします。
VM を起動します。
VM が再起動されたら、次の手順を実行します。

1. 次のコマンドを使用して、 **ルート** ユーザーとして VM にアクセスします。
 
   ```
   sudo su
   ```

1. **gptfdisk** パッケージをインストールします。これは、OS ディスクのサイズを増やすために必要です。

   ```
   yum install gdisk -y
   ```

1.  ディスクで使用可能なすべてのセクターを表示するには、次のコマンドを実行します。
    ```
    gdisk -l /dev/sda
    ```

1. パーティションの種類を通知する詳細が表示されます。 GPT であることを確認します。 ルート パーティションを特定します。 ブート パーティション (BIOS ブート パーティション) とシステム パーティション ('EFI システムパーティション') を変更または削除しないでください

1. 次のコマンドを使用して、パーティション分割を初めて開始します。 
    ```
    gdisk /dev/sda
    ```

1. ここで、次のコマンドを確認するメッセージが表示されます ('Command: ? for help')。 

   ```
   w
   ```

1. 次の警告が表示されます "Warning! Secondary header is placed too early on the disk! Do you want to correct this problem? (Y/N):"。 'Y' を押す必要があります

   ```
   Y
   ```

1. 最後のチェックが完了し、確認を求めるメッセージが表示されます。 'Y' を押します

   ```
   Y
   ```

1. partprobe コマンドを使用して、すべてが正常に動作したかどうかを確認します

   ```
   partprobe
   ```

1. 上記の手順により、セカンダリ GPT ヘッダーが最後に配置されていることを確認しました。 次の手順では、再度 gdisk ツールを使用して、サイズ変更のプロセスを開始します。 次のコマンドを使用します。

   ```
   gdisk /dev/sda
   ```
1. コマンド メニューで [p] をクリックして、パーティションの一覧を表示します。 ルート パーティション (手順では、sda2 がルート パーティションと見なされます) とブート パーティション (手順では、sda3 がブート パーティションと見なされます) を特定します 

   ```
   p
   ```
    ![ルート パーティションとブート パーティション](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. 'd' を押して、パーティションを削除し、ブートに割り当てられているパーティション番号を選択します (この例では '3')
   ```
   d
   3
   ```
1. 'd' を押して、パーティションを削除し、ブートに割り当てられているパーティション番号を選択します (この例では '2')
   ```
   d
   2
   ```
    ![ルート パーティションとブート パーティションの削除](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. サイズを大きくしてルート パーティションを再作成するには、'n' を押し、前に削除したルートのパーティション番号 (この例では '2') を入力して、最初のセクターを '規定値'、最後のセクターを '最後のセクター値 - ブート サイズ セクター' (この場合は 2 MB ブートに対応する '4096')、16 進数コードを '8300' として選択します
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. ブート パーティションを再作成するには、'n' を押し、以前に削除したブートのパーティション番号 (この例では '3') を入力し、最初のセクターを '既定値'、最後のセクターを '既定値'、16 進数コードを 'EF02' として選択します
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. 'W' コマンドを使用して変更を書き込み、確認のために 'Y' を押します
   ```
   w
   Y
   ```
1. コマンド 'partprobe' を実行して、ディスクの安定性を確認します
   ```
   partprobe
   ```
1. VM を再起動すると、ルート パーティションのサイズが増加しています
   ```
   reboot
   ```

   ![新規のルート パーティションとブート パーティション](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. パーティションで xfs_growfs コマンドを実行してサイズを変更します
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS Grow FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>次のステップ

- [ディスクのサイズ変更](expand-disks.md)
