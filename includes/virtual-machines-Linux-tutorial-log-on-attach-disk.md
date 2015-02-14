

## <a id="logon"> </a>仮想マシンを作成後、ログオンする方法 ##

仮想マシンとそのマシン上で実行されるアプリケーションの設定を管理するには、SSH クライアントを使用できます。そのためには、仮想マシンへのアクセスに使用するコンピューターに SSH クライアントをインストールする必要があります。SSH クライアント プログラムの選択肢は多数あります。たとえば、次のプログラムを選択できます。

- Windows オペレーティング システムが実行されているコンピューターを使用している場合は、PuTTY などの SSH クライアントを使用できます。詳細については、[PuTTY のダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)を参照してください。
- Linux オペレーティング システムが実行されているコンピューターを使用している場合は、OpenSSH などの SSH クライアントを使用できます。詳細については、「[OpenSSH (英語)](http://www.openssh.org/)」を参照してください。

このチュートリアルでは、PuTTY プログラムを使用して仮想マシンにアクセスする手順を示します。

1. 管理ポータルで**ホスト名**と**ポート情報**を検索します。仮想マシンのダッシュボードから必要な情報を見つけることができます。仮想マシン名をクリックし、ダッシュボードの **[概要]** セクションで **[SSH の詳細]** を探します。

	![Find SSH details](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/SSHdetails.png)

2. PuTTY プログラムを開きます。

3. ダッシュボードから収集した**ホスト名**と**ポート情報**を入力し、**[開く]** をクリックします。

	![Enter the host name and port information](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/putty.png)

4. 仮想マシンの作成時に追加した NewUser1 アカウントを使用して仮想マシンにログオンします。

	![Log on to the new virtual machine](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/sshlogin.png)

	これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。


## <a id="attachdisk"> </a>新しい仮想マシンにデータ ディスクを接続する方法 ##

アプリケーションによってはデータの保存が必要になる場合があります。そのためには、前の手順で作成した仮想マシンにデータ ディスクを接続します。最も簡単な方法は、仮想マシンに空のデータ ディスクを接続することです。

Linux では通常、リソース ディスクは Azure Linux エージェントによって管理され、**/mnt/resource** (または Ubuntu イメージでは **/mnt**) に自動的にマウントされます。一方、Linux では、データ ディスクはカーネルによって、 `/dev/sdc` という名前が付けられる場合があります。その場合、ユーザーはこのリソースをパーティション分割し、フォーマットした上で、マウントする必要があります。詳細については、「[Azure Linux エージェント ユーザー ガイド](http://www.windowsazure.com/ja-jp/manage/linux/how-to-guides/linux-agent-guide/)」を参照してください。

>[AZURE.NOTE] データをリソース ディスクに格納しないでください。このディスクはアプリケーションとプロセス用の一時的なストレージであり、スワップ ファイルなど、保持する必要のないデータが格納されます。データ ディスクは、ページ BLOB の .vhd ファイルの形で Azure Storage に存在し、データ保護のためにストレージの冗長性を提供します。詳細については、[ディスクおよびイメージの管理](http://msdn.microsoft.com/ja-jp/library/jj672979.aspx)」を参照してください。

1. まだサインインしていない場合は、Azure の管理ポータルにサインインします。

2. **[仮想マシン]** をクリックし、前の手順で作成した **MyTestVM1** 仮想マシンを選択します。

3. コマンド バーで、**[ディスクの接続]**、**[空のディスクの接続]** の順にクリックします。
	
	**[空のディスクの接続]** ダイアログ ボックスが表示されます。

	![Define disk details](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachnewdisklinux.png)

4. **[仮想マシン名]**、**[ストレージの場所]**、**[ファイル名]** の値は既に定義されています。必要なのは、ディスクのサイズを入力することだけです。**[サイズ]** ボックスに「**5**」と入力します。

	**注:** すべてのディスクは Azure ストレージ内の VHD ファイルから作成されます。ストレージに追加される VHD ファイルに名前を付けることができます。ただし、ディスクの名前は自動的に生成されます。

5. チェック マークをクリックして、仮想マシンにデータ ディスクを接続します。

6. ダッシュボードを表示すると、データ ディスクが仮想マシンに正しく接続されたかを確認できます仮想マシンの名前をクリックすると、ダッシュボードが表示されます。

	仮想マシンに接続されているディスクの数が現在 "2" であることがわかります。**[ディスク]** ボックスの一覧には、接続されているディスクが表示されています。

	![Attach disk success](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachemptysuccess.png)


仮想マシンに接続したばかりのデータ ディスクはオフラインで、追加後も初期化されません。データ ディスクを使用してデータを保存するには、仮想マシンにログオンし、ディスクを初期化する必要があります。

1. 上にリストされた「**仮想マシンを作成後、ログオンする方法**」の手順を使用して仮想マシンに接続します。


2. SSH のウィンドウで次のコマンドを入力し、アカウントのパスワードを入力します。

	`sudo grep SCSI /var/log/messages`

	表示されたメッセージで、最後に追加されたデータ ディスクの識別子を確認できます。

	![Identify disk](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskmessages.png)


3. SSH のウィンドウで、次のコマンドを入力して新しいデバイスを作成し、アカウントのパスワードを入力します。

	`sudo fdisk /dev/sdc`

	>[AZURE.NOTE] この例では、/sbin または /usr/sbin が `$PATH` にない場合、一部のディストリビューションで  `sudo -i` の使用が必要になる場合があります。


4. 「**n**」を入力すると、新しいパーティションが作成されます。

	![Create new device](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskpartition.png)


5. パーティションをプライマリ パーティションにするには「**p**」を、最初のパーティションにするには「**1**」を、シリンダーの既定値をそのまま使用するには Enter キーを押します。

	![Create partition](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskcylinder.png)


6. 「**p**」を入力すると、パーティション分割されたディスクに関する詳細情報が表示されます。

	![List disk information](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskinfo.png)


7. 「**w**」と入力すると、ディスクの設定が書き込まれます。

	![Write the disk changes](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskwrite.png)


8. 新しいパーティションにファイル システムを作成する必要があります。たとえば、次のコマンドを入力してファイル システムを作成し、アカウントのパスワードを入力します。

	`sudo mkfs -t ext4 /dev/sdc1`

	![Create file system](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskfilesystem.png)

	>[AZURE.NOTE]  SUSE Linux Enterprise 11 では、システムは ext4 ファイル システムに読み取り専用でアクセスすることに注意してください。これらのシステムについては、ext4 ではなく ext3 として新しいファイル システムをフォーマットすることをお勧めします。


9. 新しいファイル システムをマウントするディレクトリを作成します。たとえば、次のコマンドを入力し、アカウントのパスワードを入力します。

	`sudo mkdir /datadrive`


10. 次のコマンドを入力してドライブをマウントします。

	`sudo mount /dev/sdc1 /datadrive`

	これで、データ ディスクを **/datadrive** として使用する準備ができました。


11. 新しいドライブを /etc/fstab に追加します。

	再起動後にドライブを自動的に再マウントするために、そのドライブを /etc/fstab ファイルに追加する必要があります。また、ドライブを参照する際に、デバイス名 (つまり /dev/sdc1) だけではなく、UUID (汎用一意識別子) を /etc/fstab で使用することもお勧めします。新しいドライブの UUID を確認するには、**blkid** ユーティリティを次のように使用します。
	
		`sudo -i blkid`

	次のような出力が表示されます。

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[AZURE.NOTE] blkid では、すべての場合に sudo アクセスが必要なわけではありませんが、ディストリビューションによっては  `sudo -i` を使用した方が簡単に実行できる可能性があります (sbin または /usr/sbin が $PATH にない場合)。

	**注意事項:** /etc/fstab ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

	テキスト エディターを使用して、/etc/fstab ファイルの最後の部分に新しいファイル システムに関する情報を入力します。この例では、前の手順で作成した新しい **/dev/sdc1** デバイスに対して UUID 値を使用し、マウント ポイントとして **/datadrive** を使用します。

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	または、SUSE Linux に基づいたシステムでは、わずかに異なる形式を使用する必要がある場合があります。

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	追加のデータ ドライブやパーティションを作成する場合、それらも /etc/fstab に個別に入力する必要があります。

	これで、ファイル システムが正しくマウントされるかどうかをテストできます。そのためには、ファイル システムをマウント解除してから、再度マウントします。つまり、前の手順で作成したサンプルのマウント ポイント  `/datadrive` を使用します。 

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	2 番目のコマンドでエラーが発生した場合、/etc/fstab ファイルの構文が正しいかどうかを確認してください。

	>[AZURE.NOTE] この後、fstab を編集せずにデータ ディスクを削除すると VM は起動できません。これが頻繁に発生する場合、大部分のディストリビューションでは  `nofail` または  `nobootwait` fstab オプションが提供されます。これによって、ディスクがなくてもシステムを起動することができます。これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。


<!--HONumber=42-->
