#データ ディスクを仮想マシンに接続する方法




- [概念](#concepts)
- [方法: 既存のディスクの接続](#attachexisting)
- [方法: 空のディスクの接続](#attachempty)
- [方法: Windows Server 2008 R2 での新しいデータ ディスクの初期化](#initializeinWS)
- [方法: Linux での新しいデータ ディスクの初期化](#initializeinlinux)



##<a id="concepts"></a>概念



データ ディスクを仮想マシンに接続してアプリケーション データを保存できます。データ ディスクは仮想ハード ディスク (VHD) で、自分のコンピューターでローカルに作成することも、Windows Azure を使ってクラウドに作成することもできます。仮想マシンでデータ ディスクを管理する方法は、オフィス内のサーバーで管理する方法と同じです。

管理ポータルを使用してデータを格納しているデータ ディスクをアップロードして仮想マシンに接続することも、仮想マシンで使用しているのと同じストレージ アカウントから空のディスクを接続することもできます。この記事では、次のプロセスについて説明します。別のストレージ アカウントに存在する空のディスクを接続するには、Windows Azure PowerShell モジュールで利用できる [Add-AzureDataDisk](http://go.microsoft.com/fwlink/p/?LinkId=391661) コマンドレットを使用します。このモジュールをダウンロードするには、[ダウンロード](http://www.windowsazure.com/ja-jp/downloads/) ページにアクセスしてください。

ディスク追加のために仮想マシンが停止することはありません。仮想マシンに接続できるディスクの数は、仮想マシンのサイズに基づいています。仮想マシンとディスク サイズの詳細については、「[Windows Azure の仮想マシンおよびクラウド サービスのサイズ](http://go.microsoft.com/FWLink/p/?LinkID=294683)」を参照してください。

> [WACOM.NOTE] 
> Windows Azure のストレージでは最大 1 TB の BLOB がサポートされています。これは、最大仮想サイズが 999 GB の VHD に対応します。ただし、Hyper-V を使用して新しい VHD を作成する場合は、指定するサイズは仮想サイズを表します。Windows Azure で VHD を使用するには、999 GB 以下のサイズを指定してください。

**データ ディスクとリソース ディスク**
データ ディスクは Windows Azure のストレージ内に存在し、ファイルとアプリケーション データの永続的なストレージとして使用できます。

各仮想マシンには、一時的なローカル *リソース ディスク*も接続されています。リソース ディスク上のデータには、再起動を行った場合の持続性がないため、多くの場合は仮想マシン上で実行されているアプリケーションとプロセスが、このディスクを過渡的および一時的なストレージとして使用します。また、オペレーティング システムのページ ファイルやスワップ ファイルを格納する目的でも使用されます。

Windows では、リソース ディスクに **D:** ドライブというラベルが付いています。Linux では通常、リソース ディスクは Windows Azure Linux エージェントによって管理され、**/mnt/resource** (または Ubuntu イメージでは **/mnt**) に自動的にマウントされます。詳細については、「[Windows Azure Linux Agent User Guide (Windows Azure Linux エージェント ユーザー ガイド)](http://www.windowsazure.com/ja-jp/manage/linux/how-to-guides/linux-agent-guide/)」を参照してください。

データ ディスクの使い方の詳細については、[ディスクとイメージの管理に関するページ](http://go.microsoft.com/fwlink/p/?LinkId=391660)を参照してください。

##<a id="attachexisting"></a>方法: 既存のディスクの接続


1. まだサインインしていない場合は、[Windows Azure の管理ポータル](http://manage.windowsazure.com)にサインインします。

2. **[仮想マシン]** をクリックし、ディスクの接続先の仮想マシンを選択します。

3. コマンド バーで **[接続]** をクリックし、**[ディスクの接続]** を選択します。


	![データ ディスクの接続](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)

	**[ディスクの接続]** ダイアログ ボックスが表示されます。



	![データ ディスク詳細の入力](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

3. 仮想マシンに接続するデータ ディスクを選択します。
4. チェック マークをクリックして、仮想マシンにデータ ディスクを接続します。
  
 
	データ ディスクが仮想マシンのダッシュボードに表示されます。


	![データ ディスクの接続に成功](./media/howto-attach-disk-window-linux/AttachSuccess.png)

##<a id="attachempty"></a>方法: 空のディスクの接続

空のディスクとして使用するために .vhd ファイルを作成してアップロードした後、そのディスクを仮想マシンに接続できます。.vhd ファイルをストレージ アカウントにアップロードするには、[Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) コマンドレットを使用します。

1. **[仮想マシン]** をクリックし、データ ディスクの接続先の仮想マシンを選択します。



2. コマンド バーで **[接続]** をクリックし、**[空のディスクの接続]** を選択します。


	![空のディスクの接続](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)



	**[空のディスクの接続]** ダイアログ ボックスが表示されます。



	![新しい空のディスクの接続](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

 
3. **[ファイル名]** ボックスで、自動的に生成された名前を受け入れるか、保存する VHD ファイルに使用する名前を入力します。VHD ファイルから作成されたデータ ディスクでは必ず、自動的に生成された名前が使用されます。



4. **[サイズ]** ボックスに、データ ディスクのサイズを入力します。



5. チェック マークをクリックして、空のデータ ディスクを接続します。

	データ ディスクが仮想マシンのダッシュボードに表示されます。


	![空のデータ ディスクの接続に成功](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)


> [WACOM.NOTE] 
> データ ディスクを追加した後、仮想マシンがストレージとしてそのディスクを使用できるように、仮想マシンにログオンしてディスクを初期化する必要があります。



##<a id="initializeinWS"></a>方法: Windows Server での新しいデータ ディスクの初期化

1. 「[Windows Server を実行する仮想マシンにログオンする方法][logon]」に示された手順に従って仮想マシンに接続します。



2. マシンにログオンしたら、左側のウィンドウで**サーバー マネージャー**を開き、**[ストレージ]** を展開して、**[ディスクの管理]** をクリックします。



	![サーバー マネージャーを開く](./media/howto-attach-disk-window-linux/ServerManager.png)



3. **[ディスク 2]** を右クリックして、**[ディスクの初期化]** をクリックします。



	![ディスクの初期化](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4. **[OK]** をクリックして初期化処理を開始します。



5. ディスク 2 のスペース割り当て領域を右クリックして、**[新しいシンプル ボリューム]** をクリックし、既定値を使ってウィザードを終了します。
 

	![ボリュームの初期化](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)



	これでディスクがオンラインになり、新しいドライブ文字が使用できるようになりました。



	![ボリュームの初期化に成功](./media/howto-attach-disk-window-linux/InitializeSuccess.png)




##<a id="initializeinlinux"></a>方法: Linux での新しいデータ ディスクの初期化



1. 「[Linux を実行する仮想マシンにログオンする方法][logonlinux]」に示された手順に従って仮想マシンに接続します。



2. SSH のウィンドウで、次のコマンドを入力し、仮想マシンを管理するために作成したアカウントのパスワードを入力します。

	`sudo grep SCSI /var/log/messages`

	表示されたメッセージで、最後に追加されたデータ ディスクの識別子を確認できます。



	![ディスク メッセージに取得](./media/howto-attach-disk-window-linux/DiskMessages.png)



3. SSH のウィンドウで、次のコマンドを入力して新しいデバイスを作成し、アカウントのパスワードを入力します。

	`sudo fdisk /dev/sdc`



4. 「**n**」を入力すると、新しいパーティションが作成されます。


	![新しいデバイスの作成](./media/howto-attach-disk-window-linux/DiskPartition.png)

5. パーティションをプライマリ パーティションにするには「**p**」を、最初のパーティションにするには「**1**」を、シリンダーの既定値をそのまま使用するには Enter キーを押します。


	![パーティションの作成](./media/howto-attach-disk-window-linux/DiskCylinder.png)



6. 「**p**」を入力すると、パーティション分割されたディスクに関する詳細情報が表示されます。


	![ディスク情報の表示](./media/howto-attach-disk-window-linux/DiskInfo.png)



7. 「**w**」と入力すると、ディスクの設定が書き込まれます。


	![ディスクの変更の書き込み](./media/howto-attach-disk-window-linux/DiskWrite.png)

8. 新しいパーティションにファイル システムを作成する必要があります。次のコマンドを入力してファイル システムを作成し、アカウントのパスワードを入力します。

	`sudo mkfs -t ext4 /dev/sdc1`


	![ファイル システムの作成](./media/howto-attach-disk-window-linux/DiskFileSystem.png)

9. 次のコマンドを入力してドライブをマウントするディレクトリを作成し、アカウントのパスワードを入力します。

	`sudo mkdir /mnt/datadrive`



10. 次のコマンドを入力してドライブをマウントします。

	`sudo mount /dev/sdc1 /mnt/datadrive`

	これで、データ ディスクを **/mnt/datadrive** として使用する準備ができました。



11. 新しいドライブを /etc/fstab に追加します。

	再起動後にドライブを自動的に再マウントするために、そのドライブを /etc/fstab ファイルに追加する必要があります。また、ドライブを参照する際に、デバイス名 (つまり /dev/sdc1) だけではなく、UUID (汎用一意識別子) を /etc/fstab で使用することもお勧めします。新しいドライブの UUID を確認するには、**blkid** ユーティリティを次のように使用します。
	
	`sudo -i blkid`

	次のような出力が表示されます。

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[WACOM.NOTE] blkid では、すべての場合で sudo アクセスは必要ありませんが、/sbin または /usr/sbin が "$PATH" にない場合は、一部のディストリビューションで "sudo -i" を使用した方が簡単に実行できる可能性があります。

	**注意事項:** /etc/fstab ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

	テキスト エディターを使用して、/etc/fstab ファイルの最後の部分に新しいファイル システムに関する情報を入力します。この例では、前の手順で作成した新しい **/dev/sdc1** デバイスに対して UUID 値を使用し、マウント ポイントとして **/mnt/datadrive** を使用します。

	`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e    /mnt/datadrive    ext4    defaults    1    1`

	追加のデータ ドライブやパーティションを作成する場合、それらも /etc/fstab に入力する必要があります。

	これで、ファイル システムが正しくマウントされるかどうかをテストできます。そのためには、ファイル システムをマウント解除してから、再度マウントします。つまり、前の手順で作成したサンプルのマウント ポイント "/mnt/datadrive" を使用します。

		`sudo umount /mnt/datadrive`
		`sudo mount /mnt/datadrive`

	2 番目のコマンドでエラーが発生した場合、/etc/fstab ファイルの構文が正しいかどうかを確認してください。





[logon]: ../virtual-machines-log-on-windows-server/
[logonlinux]: ../virtual-machines-linux-how-to-log-on/



