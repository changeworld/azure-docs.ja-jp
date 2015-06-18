<properties
	pageTitle="Azure 上で Linux を実行する仮想マシンの作成"
	description="Azure のイメージを使用して、Linux を実行する Azure 仮想マシン (VM) を作成する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/22/2015"
	ms.author="rasquill"/>

# Linux を実行する仮想マシンの作成

コマンド ラインやポータルを使用すると、Linux を実行する Azure 仮想マシン (VM) を簡単に作成できます。このチュートリアルでは、Mac、Linux、および Windows (Azure CLI) 用の Azure コマンド ライン インターフェイスを使用して、Azure で実行する Ubuntu Server VM を簡単に作成し、**ssh** を使用してその VM に接続してから、新しいディスクを作成してマウントする方法を説明します (このトピックでは Ubuntu Server VM を使用しますが、[テンプレートとして独自のイメージ](virtual-machines-linux-create-upload-vhd.md)を使用して Linux VM を作成することもできます)。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Azure CLI のインストール

まず、[Azure CLI をインストール](../xplat-cli-install.md)します。

インストールが完了したら、「`azure config mode arm`」と入力して、リソース管理モードになっているかどうかを確認します。

さらに、「`azure login`」と入力して画面の指示に従って、職場または学校の ID でログインします。

> [AZURE.NOTE]ログイン時にエラーが発生した場合は、[個人用の Microsoft アカウントから職場または学校の ID を作成する](resource-group-create-work-id-from-personal.md)ことが必要になる可能性があります。

## Azure VM の作成

「`azure group create <my-group-name> westus`」と入力して、_&lt;my-group-name&gt;_ を一意のグループ名に置き換えます (必要に応じて別のリージョンを使用できます)。次のように表示されます。

	azure group create myuniquegroupname westus
	info:    Executing command group create
	+ Getting resource group myuniquegroupname
	+ Creating resource group myuniquegroupname
	info:    Created resource group myuniquegroupname
	data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
	data:    Name:                myuniquegroupname
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags:
	data:
	info:    group create command OK

「`azure vm quick-create`」と入力して VM を作成すると、残りのパラメーターを入力するように求める画面が表示されます。先ほど作成したリソース グループの名前を使用します。**ImageURN** 値に `canonical:ubuntuserver:14.04.2-LTS:latest` を使用すると、次のようになります。

	azure vm quick-create
	info:    Executing command vm quick-create
	Resource group name: myuniquegroupname
	Virtual machine name: myuniquevmname
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
	User name: ops
	Password: *********
	Confirm password: *********
	+ Looking up the VM "myuniquevmname"
	info:    Using the VM Size "Standard_D1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
	+ Looking up the storage account cli3c0464f24f1bf4f014323
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	info:    Preparing to create new virtual network and subnet
	/ Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	+ Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
	info:    Found public ip parameters, trying to setup PublicIP profile
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
	+ Creating public ip "myuni-westu-1432328437727-pip"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	+ Creating NIC "myuni-westu-1432328437727-nic"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Creating VM "myuniquevmname"
	+ Looking up the VM "myuniquevmname"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
	data:    ProvisioningState               :Succeeded
	data:    Name                            :myuniquevmname
	data:    Location                        :westus
	data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_D1
	data:
	data:    Storage Profile:
	data:      Image reference:
	data:        Publisher                   :canonical
	data:        Offer                       :ubuntuserver
	data:        Sku                         :14.04.2-LTS
	data:        Version                     :latest
	data:
	data:      OS Disk:
	data:        OSType                      :Linux
	data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :myuniquevmname
	data:      User Name                     :ops
	data:      Linux Configuration:
	data:        Disable Password Auth       :false
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-31-55-31
	data:          Provisioning State        :Succeeded
	data:          Name                      :myuni-westu-1432328437727-nic
	data:          Location                  :westus
	data:            Private IP alloc-method :Dynamic
	data:            Private IP address      :10.0.1.4
	data:            Public IP address       :191.239.51.1
	data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	info:    vm quick-create command OK

VM は稼働していて、接続するまで待機しています。

## VM への接続

Linux VM では、通常 **ssh** を使用して接続します。このトピックでは、ユーザー名とパスワードを使用して VM に接続します。公開キーおよび秘密キーのペアを使用して VM と通信するには、「[Azure 上の Linux における SSH の使用方法](virtual-machines-linux-use-ssh-key.md)」を参照してください。

**ssh** を使用した接続に慣れていない場合は、`ssh <username>@<publicdnsaddress> -p <the ssh port>` 形式のコマンドを使用します。ここでは、前の手順のユーザー名およびパスワードと、既定の **ssh** ポートであるポート 22 を使用します。

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
	The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
	ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
	ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Fri May 22 21:02:32 UTC 2015

	  System load: 0.37              Memory usage: 2%   Processes:       207
	  Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.



	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@myuniquevmname:~$

これで VM に接続されたので、ディスクを接続する準備ができました。

## ディスクの接続とマウント

新しいディスクには簡単に接続できます。「`azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>`」と入力するだけで、VM に新しい GB のディスクが作成され、接続されます。これは、次のようになります。

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


ここで、`dmesg | grep SCSI` を使用してディスクを探してみましょう (新しいディスクの探索に使用する方法は異なる場合があります)。この場合、次のようになります。

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

このトピックで必要なのは `sdc` ディスクです。使用しているディスクを `sdc` と想定して、`sudo fdisk /dev/sdc` でディスクをパーティション分割します。それをパーティション 1 上のプライマリ ディスクにして、それ以外は既定値をそのまま使用します。

	sudo fdisk /dev/sdc
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0x2a59b123.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.

	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-10485759, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
	Using default value 10485759

プロンプトで「`p`」と入力して、パーティションを作成します。

	Command (m for help): p

	Disk /dev/sdc: 5368 MB, 5368709120 bytes
	255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x2a59b123

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdc1            2048    10485759     5241856   83  Linux

	Command (m for help): w
	The partition table has been altered!

	Calling ioctl() to re-read partition table.
	Syncing disks.

**mkfs** コマンドを使用し、ファイル システムの種類とデバイス名を指定して、パーティションにファイル システムを作成します。このトピックでは、前の内容から `ext4` と `/dev/sdc1` を使用しています。

	sudo mkfs -t ext4 /dev/sdc1
	mke2fs 1.42.9 (4-Feb-2014)
	Discarding device blocks: done
	Filesystem label=
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	327680 inodes, 1310464 blocks
	65523 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=1342177280
	40 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks:
		32768, 98304, 163840, 229376, 294912, 819200, 884736

	Allocating group tables: done
	Writing inode tables: done
	Creating journal (32768 blocks): done
	Writing superblocks and filesystem accounting information: done

次に、`mkdir` を使用して、ファイル システムをマウントするディレクトリを作成します。

	sudo mkdir /datadrive

`mount` を使用して、ディレクトリをマウントします。

	sudo mount /dev/sdc1 /datadrive

これで、データ ディスクを `/datadrive` として使用する準備ができました。

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE]識別用の SSH キーを使用して Linux 仮想マシンに接続することもできます。詳細については、「[Azure 上の Linux における SSH の使用方法](virtual-machines-linux-use-ssh-key.md)」を参照してください。

## 次のステップ

通常、新しいディスクは、[fstab](http://en.wikipedia.org/wiki/Fstab) ファイルにその情報を書き込まない限り、再起動しても VM で使用できないことに注意してください。

Azure 上の Linux の詳細については、次の内容を参照してください。

- [Azure での Linux とオープン ソース コンピューティング](virtual-machines-linux-opensource.md)

- [Mac および Linux 用 Azure コマンド ライン ツールの使用方法](../virtual-machines-command-line-tools.md)

- [Linux 向けに Azure カスタム スクリプト拡張機能を使って LAMP アプリをデプロイする](virtual-machines-linux-script-lamp.md)

- [Azure VM 構成設定について](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Azure の Linux 用 Docker 仮想マシン拡張機能](virtual-machines-docker-vm-extension.md)

<!---HONumber=58--> 