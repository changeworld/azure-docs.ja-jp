<properties
	pageTitle="ディスクを Linux VM にアタッチする | Microsoft Azure"
	description="データ ディスクを Linux を実行する Azure 仮想マシンに接続し、初期化して、使用できる状態にする方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="iainfou"/>

# データ ディスクを Linux 仮想マシンに接続する方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager デプロイ モデルを使用してデータディスクを接続する](virtual-machines-linux-add-disk.md)方法を参照してください。

空のディスクと、データが含まれているディスクのどちらも Azure VM に接続できます。どちらの種類のディスクも、Azure ストレージ アカウントの .vhd ファイルです。Linux マシンへのディスクの追加では、ディスクを接続した後、初期化とフォーマットを行って使用できるようにする必要があります。この記事では、空のディスクと、データが含まれているディスクの両方を Azure VM に接続し、新しいディスクを初期化してフォーマットする方法について詳しく説明します。

> [AZURE.NOTE] 仮想マシンのデータを格納するには、1 つ以上の個別のディスクを使用することをお勧めします。Azure の仮想マシンを作成するとき、オペレーティング システム ディスクと一時ディスクが表示されます。**永続データの格納に一時ディスクを使用しないでください。** 名前が示すとおり、D ドライブは一時的なストレージのみを提供します。Azure Storage に配置されていないため、冗長性やバックアップは提供しません。一時ディスクは通常、Azure Linux Agent によって管理され、**/mnt/resource** (または Ubuntu イメージでは **/mnt** )に自動的にマウントされます。一方で、データ ディスクには Linux カーネルによって `/dev/sdc` のような名前が付けられる場合があります。その場合、このリソースをパーティション分割し、フォーマットしてからマウントする必要があります。詳細については、「[Azure Linux エージェント ユーザー ガイド][Agent]」を参照してください。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## Linux での新しいデータ ディスクの初期化

1. VM に SSH 接続します。詳細については、「[Linux を実行する仮想マシンにログオンする方法][Logon]」を参照してください。

2. 次に、データ ディスクの初期化のためにデバイスの ID を検索する必要があります。この作業を実行する 2 つの方法があります。

	a) 次のコマンドのように、ログの SCSI デバイスを検索します。

			$sudo grep SCSI /var/log/messages

	最新の Ubuntu ディストリビューションの場合は、`/var/log/messages` へのログ記録が既定で無効になっていることがあるため、`sudo grep SCSI /var/log/syslog` の使用が必要になる場合があります。

	表示されたメッセージで、最後に追加されたデータ ディスクの識別子を確認できます。

	![ディスク メッセージに取得](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

	または

	b) `lsscsi` コマンドを使用してデバイスの ID を検索します。`lsscsi` は、`yum install lsscsi` (Red Hat ベースのディストリビューション) または `apt-get install lsscsi` (Debian ベースのディストリビューション) のいずれかでインストールできます。検索対象のディスクは、その _LUN_ (**論理ユニット番号**) で検索できます。たとえば、ディスクに割り当てた _LUN_ は、`azure vm disk list <virtual-machine>` から以下のように簡単に確認することができます。

			~$ azure vm disk list TestVM
			info:    Executing command vm disk list
			+ Fetching disk images
			+ Getting virtual machines
			+ Getting VM disks
			data:    Lun  Size(GB)  Blob-Name                         OS
			data:    ---  --------  --------------------------------  -----
			data:         30        TestVM-2645b8030676c8f8.vhd  Linux
			data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
			info:    vm disk list command OK

	このデータを、同じサンプル仮想マシンに対する `lsscsi` の出力と比較します。

			ops@TestVM:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

	各行の組にある最後の数字が _LUN_ です。詳細については、「`man lsscsi`」を参照してください。

3. プロンプトで、次のコマンドを入力して、デバイスを作成します。

		$sudo fdisk /dev/sdc


4. 表示されるプロンプトで「**n**」と入力すると、新しいパーティションが作成されます。


	![デバイスの作成](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. 求められたら、「**p**」と入力して、パーティションをプライマリ パーティションにします。「**1**」と入力して最初のパーティションにし、Enter キーを押してシリンダーの既定値をそのまま使用します。システムによっては、シリンダーではなく、最初と最後のセクターの既定値が表示される場合があります。これらの既定値をそのまま使用することもできます。


	![Create partition](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. 「**p**」を入力すると、パーティション分割されたディスクに関する詳細情報が表示されます。


	![ディスク情報の表示](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. 「**w**」と入力すると、ディスクの設定が書き込まれます。


	![Write the disk changes](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. これで、新しいパーティションにファイル システムを作成できます。パーティション番号をデバイス ID に追加します (次の例では `/dev/sdc1`)。次の例では、ext4 パーティションを /dev/sdc1 に作成します。

		# sudo mkfs -t ext4 /dev/sdc1

	![Create file system](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

	>[AZURE.NOTE] SuSE Linux Enterprise 11 システムでは、ext4 ファイル システムへのアクセスは読み取り専用のみサポートされます。これらのシステムには ext4 ではなく ext3 として新しいファイル システムの書式設定することをお勧めします。


9. 次のように、新しいファイル システムをマウントするディレクトリを作成します。

		# sudo mkdir /datadrive


10. 最後に、次のように、ドライブをマウントできます。

		# sudo mount /dev/sdc1 /datadrive

	これで、データ ディスクを **/datadrive** として使用する準備ができました。
	
	![ディレクトリの作成とディスクのマウント](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. 新しいドライブを /etc/fstab に追加します。

	再起動後にドライブを自動的に再マウントするために、そのドライブを /etc/fstab ファイルに追加する必要があります。また、ドライブを参照する際に、デバイス名 (つまり /dev/sdc1) だけではなく、UUID (汎用一意識別子) を /etc/fstab で使用することもお勧めします。UUID を使用すると、OS が起動中にディスク エラーを検出した場合に、間違ったディスクが特定の場所にマウントされて残りのデータ ディスクにそれらのデバイス ID が割り当てられるのを防ぐことができます。新しいドライブの UUID を確認するには、**blkid** ユーティリティを次のように使用します。

		# sudo -i blkid

	出力は、次のようになります。

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE] **/etc/fstab** ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

	次に、テキスト エディターで **/etc/fstab** ファイルを開きます。

		# sudo vi /etc/fstab

	この例では、前の手順で作成した新しい **/dev/sdc1** デバイスに対して UUID 値を使用し、マウント ポイントとして **/datadrive** を使用します。次の行を **/etc/fstab** ファイルの末尾に追加します。

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	または、SuSE Linux に基づいたシステムでは、わずかに異なる形式を使用する必要がある場合があります。

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults   1   2

	これで、ファイル システムが正しくマウントされるかどうかをテストできます。そのためには、ファイル システムをマウント解除してから、再度マウントします。つまり、前の手順で作成したサンプルのマウント ポイント `/datadrive` を使用します。

		# sudo umount /datadrive
		# sudo mount /datadrive

	`mount` コマンドでエラーが発生した場合、/etc/fstab ファイルの構文が正しいかどうかを確認してください。追加のデータ ドライブやパーティションを作成する場合は、それらも /etc/fstab に個別に入力します。

	次のコマンドを使用して、ドライブを書き込み可能にします。

		# sudo chmod go+w /datadrive

>[AZURE.NOTE] この後、fstab を編集せずにデータ ディスクを削除すると VM は起動できません。これが頻繁に発生する場合、大部分のディストリビューションでは `nofail` または `nobootwait` fstab オプションが提供されます。これによって、起動時にディスクのマウントが失敗してもシステムを起動することができます。これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。

### Azure における Linux の TRIM/UNMAP サポート
一部の Linux カーネルでは、ディスク上の未使用ブロックを破棄するために TRIM/UNMAP 操作がサポートされます。これらの操作は主に、Standard Storage で、削除されたページが無効になり、破棄できるようになったことを Azure に通知するときに役立ちます。ページを破棄すると、サイズの大きいファイルを作成して削除する場合のコストを節約できます。

Linux VM で TRIM のサポートを有効にする方法は 2 通りあります。通常どおり、ご使用のディストリビューションで推奨される方法をお問い合わせください。

- 次のように、`/etc/fstab` で `discard` マウント オプションを使用します。

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- または、`fstrim` コマンドを手動でコマンド ラインから実行するか、crontab に追加して定期的に実行することができます。

	**Ubuntu**

		# sudo apt-get install util-linux
		# sudo fstrim /datadrive

	**RHEL/CentOS**

		# sudo yum install util-linux
		# sudo fstrim /datadrive

## トラブルシューティング
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]


## 次のステップ
以下の記事で、Linux VM の使用方法について詳しい情報を得ることができます。

- [Linux を実行する仮想マシンにログオンする方法][Logon]

- [Linux 仮想マシンからディスクを切断する方法](virtual-machines-linux-classic-detach-disk.md)

- [クラシック デプロイ モデルでの Azure CLI の使用](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md

<!---HONumber=AcomDC_0831_2016-->