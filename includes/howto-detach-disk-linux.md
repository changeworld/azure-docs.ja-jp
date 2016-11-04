仮想マシン (VM) に接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。そうすれば、ディスクは VM から削除されますが、ストレージからは削除されません。再びディスク上の既存のデータを使用する場合は、同じ VM や別の仮想マシンに再接続できます。

> [!NOTE]
> Azure の VM では、オペレーティング システム ディスク、ローカル一時ディスク、オプションのデータ ディスクなど、さまざまな種類のディスクが使用されます。詳細については、「[About Disks and VHDs for Virtual Machines (Virtual Machines 用のディスクと VHD について)](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md)」を参照してください。VM を削除しない限り、オペレーティング システム ディスクを切断することはできません。
> 
> 

## ディスクの特定
VM からディスクを切断するには、まず切断するディスクの識別子である LUN 番号を確認する必要があります。そのためには、次の手順に従います。

1. Azure CLI を開いて、[Azure サブスクリプションに接続](../articles/xplat-cli-connect.md)します。Azure サービス管理モード (`azure config mode asm`) であることを確認します。
2. `azure vm disk list <virtual-machine-name>` を使用して、VM に接続されているディスクを確認します。
   
    $azure vm disk list UbuntuVM
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
3. 切断するディスクの LUN (**論理ユニット番号**) を記録しておきます。

## ディスクへのオペレーティング システム参照の削除
Linux ゲストからディスクを切断する前に、ディスク上に使用されているパーティションがないことを確認します。また、再起動後にオペレーティング システムがパーティションを再マウントしないようにします。次の手順により、ディスクの[接続](../articles/virtual-machines/virtual-machines-linux-classic-attach-disk.md)時に作成された可能性がある構成が元に戻ります。

1. `lsscsi` コマンドを使用してディスク識別子を検出します。`lsscsi` は、`yum install lsscsi` (Red Hat ベースのディストリビューション) または `apt-get install lsscsi` (Debian ベースのディストリビューション) のいずれかでインストールできます。目的のディスク識別子を見つけるには、LUN 番号を使用します。各行の組にある最後の数字が LUN です。次の例では LUN 0 が */dev/sdc* にマップされます
   
            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
            [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
2. `fdisk -l <disk>` を使用して、切断するディスクに関連付けられているパーティションを検出します。
3.          $ sudo fdisk -l /dev/sdc
            Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
            Units = sectors of 1 * 512 = 512 bytes
            Sector size (logical/physical): 512 bytes / 512 bytes
            I/O size (minimum/optimal): 512 bytes / 512 bytes
            Disk label type: dos
            Disk identifier: 0x5a1d2a1a
   
               Device Boot      Start         End      Blocks   Id  System
            /dev/sdc1            2048  2145386495  1072692224   83  Linux
4. ディスクに対して表示されている各パーティションのマウントを解除します。この例では、`$ sudo umount /dev/sdc1` です
5. `blkid` コマンドを使用して、すべてのパーティションの UUID を検出します
   
            $ sudo blkid
            /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
            /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
            /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
            /dev/sdd1: UUID="44444444-4b4b-4c4c-4d4d-4e4e4e4e4e4e" TYPE="ext4
6. 切断するディスクのすべてのパーティションに対する、デバイス パスまたは UUID のいずれかに関連付けられている **/etc/fstab** ファイルのエントリを削除します。この例のエントリは次のようになります。
   
        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   または
   
        /dev/sdc1   /datadrive   ext4   defaults   1   2

## ディスクの切断
ディスクの LUN 番号を見つけて、オペレーティング システム参照を削除したら、切断する準備はできています。

1. `azure vm disk detach
   <virtual-machine-name> <LUN>` コマンドを実行して、選択したディスクを仮想マシンから切断します。
   
    $azure vm disk detach UbuntuVM 0
    info:    Executing command vm disk detach
   
   * Getting virtual machines
   * Removing Data-Disk
     info:    vm disk detach command OK
2. ディスクが切断されたかどうかは、次のコマンドを実行して確認できます。
   
    $azure vm disk list UbuntuVM
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     info:    vm disk list command OK

切断したディスクはストレージに残りますが、仮想マシンには接続されなくなっています。

<!---HONumber=AcomDC_0824_2016-->