<properties 
   pageTitle="FreeBSD VHD の作成と Azure へのアップロード" 
   description="FreeBSD オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します" 
   services="virtual-machines" 
   documentationCenter="" 
   authors="KylieLiang" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services" 
   ms.date="05/19/2015"
   ms.author="kyliel"/>

# FreeBSD VHD の作成と Azure へのアップロード 

ここでは、FreeBSD オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成およびアップロードする方法を示し、それをイメージとして活用して Azure で仮想マシン (VM) を作成できるようにします。

##前提条件##
この記事では、次の項目があることを前提としています。

- **Azure サブスクリプション** - アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure アカウントの作成に関するページ](../php-create-account.md)を参照してください。 

- **Azure PowerShell ツール** - Microsoft Azure PowerShell モジュールをインストールし、サブスクリプションを使用するように構成しておきます。このモジュールをダウンロードするには、[Azure のダウンロード ページ](http://azure.microsoft.com/downloads/)にアクセスしてください。モジュールのインストールと構成のチュートリアルはこちらで入手できます。[Azure のダウンロード](http://azure.microsoft.com/downloads/)のコマンドレットを使用して VHD をアップロードします。

- **.vhd ファイルにインストールされている FreeBSD オペレーティング システム** - サポートされている FreeBSD オペレーティング システムを仮想ハード ディスクにインストールしておきます。.vhd ファイルを作成するツールは複数あります。たとえば、Hyper-V などの仮想化ソリューションにより、.vhd ファイルを作成し、オペレーティング システムをインストールできます。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

> [AZURE.NOTE]新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx) コマンドレットを使用して、ディスクを VHD 形式に変換できます。

このタスクの手順は次のとおり 5 つあります。

## 手順 1. アップロードするイメージを準備する ##

Hyper-V での FreeBSD インストールについては、[こちら](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)にチュートリアルが記載されています。

FreeBSD オペレーティング システムがインストールされた仮想マシンから、次の手順を実行します。

1. **DHCP を有効にする**

		# echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
		# service netif restart

2. **SSH を有効にする**

    SSH は、ディスクからインストールすると、既定で有効になります。有効になっていない場合または FreeBSD VHD を直接使用する場合は、次のように入力します。

		# echo 'sshd_enable="YES"' >> /etc/rc.conf 
		# ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key 
		# ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key 
		# service sshd restart

3. **シリアル コンソールをセットアップする**

		# echo 'console="comconsole vidconsole"' >> /boot/loader.conf
		# echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. **sudo をインストールする**

    Azure では root アカウントが無効であるため、権限のないユーザーで sudo を利用して、管理者特権でコマンドを実行する必要があります。

		# pkg install sudo

5. Azure エージェントの前提条件

    5.1 **Python をインストールする**

		# pkg install python27 py27-asn1
		# ln -s /usr/local/bin/python2.7 /usr/bin/python

    5.2 **wget をインストールする**

		# pkg install wget 

6. **Azure エージェントをインストールする**

    Azure エージェントの最新版は常に [GitHub](https://github.com/Azure/WALinuxAgent/releases) にあります。FreeBSD 10 以降を正式にサポートしているのは、2.0.10 以降のバージョンです。

		# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.10/waagent --no-check-certificate
		# mv waagent /usr/sbin
		# chmod 755 /usr/sbin/waagent
		# /usr/sbin/waagent -install

    **重要**: インストール後に、稼働していることを念のため確認してください。

		# service –e | grep waagent
		/etc/rc.d/waagent
		# cat /var/log/waagent.log

    これで VM を**シャットダウン**できます。手順 7. を実行してからシャットダウンすることもできますが、これは任意です。

7. プロビジョニングの解除は任意です。システムをクリーンアップし、再プロビジョニングに適した状態にする場合に行います。

    以下のコマンドは前回プロビジョニングされたユーザー アカウントおよび関連付けられたデータも削除します。

		# waagent –deprovision+user

## 手順 2. Azure にストレージ アカウントを作成する ##

仮想マシンを作成するために Azure で使用できる .vhd ファイルをアップロードするには、Azure のストレージ アカウントが必要です。Azure の管理ポータルを使用して、ストレージ アカウントを作成できます。

1. Azure の管理ポータルにサインインします。

2. コマンド バーで、**[新規]** をクリックします。

3. **[Data Services]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	![ストレージ アカウントの簡易作成](./media/virtual-machines-freebsd-create-upload-vhd/Storage-quick-create.png)

4. 次のようにフィールドを指定します。
	
	- **[URL]** で、ストレージ アカウントの URL で使用するサブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。
			
	- ストレージ アカウントの**場所またはアフィニティ グループ**を選択します。アフィニティ·グループを使用すると、クラウド サービスとストレージを同じデータセンターに配置できます。
		 
	- ストレージ アカウントの **geo レプリケーション**を使用するかどうかを決定します。Geo レプリケーションは既定で有効です。このオプションでは、ユーザーのコスト負担なしで、データが 2 次拠点にコピーされるため、1 次拠点で大規模な障害が発生した場合に、2 次拠点にストレージをフェールオーバーできます。2 次拠点は自動的に割り当てられ、変更することはできません。法律上の要件または組織のポリシー上、クラウド方式のストレージの場所を厳格に管理する必要がある場合は、Geo レプリケーションを無効にすることができます。ただし、後で Geo レプリケーションを有効に戻すと、既存データを 2 次拠点にコピーするためのデータ転送料金が 1 回だけ発生することに注意してください。Geo レプリケーションなしのストレージ サービスも割引価格で提供されています。ストレージ アカウントの geo レプリケーションを管理する方法の詳細については、[ストレージ アカウントの作成、管理、削除](../storage-create-storage-account/#replication-options)に関するページを参照してください。

	![ストレージ アカウントの詳細の入力](./media/virtual-machines-freebsd-create-upload-vhd/Storage-create-account.png)


5. **[ストレージ アカウントの作成]** をクリックします。作成したアカウントが **[ストレージ]** に表示されます。

	![ストレージ アカウントの作成に成功](./media/virtual-machines-freebsd-create-upload-vhd/Storagenewaccount.png)

6. 次に、アップロードした VHD のコンテナーを作成します。ストレージ アカウント名をクリックし、次に **[コンテナー]** をクリックします。

	![ストレージ アカウントの詳細](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_detail.png)

7. **[コンテナーを作成する]** をクリックします。

	![ストレージ アカウントの詳細](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_container.png)

8. **[名前]** にコンテナーの名前を入力し、**[アクセス ポリシー]** を選択します。

	![コンテナー名](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE]既定では、コンテナーはプライベートであり、アカウント所有者のみがアクセスできます。コンテナー内の BLOB にはパブリック読み取りアクセスを許可し、コンテナーのプロパティやメタデータにはアクセスを許可しない場合は、[パブリック BLOB] オプションを使用します。コンテナーと BLOB に完全パブリック読み取りアクセスを許可するには、[パブリック コンテナー] オプションを使用します。

## 手順 3. Microsoft Azure への接続を準備する ##

.vhd ファイルをアップロードする前に、コンピューターと Azure のサブスクリプションの間にセキュリティで保護された接続を確立する必要があります。接続の確立には、Microsoft Azure Active Directory 方式または証明書方式を使用できます。

<h3>Microsoft Azure AD 方式を使用する</h3>

1. Azure PowerShell コンソールを開きます。

2. 次のコマンドを入力します。`Add-AzureAccount`
	
	このコマンドによりサインイン ウィンドウが開きますので、職場や学校のアカウントを使用してサインインできます。

	![PowerShell ウィンドウ](./media/virtual-machines-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure により資格情報が認証および保存され、ウィンドウが閉じます。

<h3>証明書方式を使用する</h3>

1. Azure PowerShell コンソールを開きます。 

2. 次のコマンドを入力します: `Get-AzurePublishSettingsFile`

3. ブラウザー ウィンドウが開き、.publishsettings ファイルをダウンロードするよう求められます。これには、Microsoft Azure のサブスクリプションについての情報と証明書が含まれています。

	![ブラウザーのダウンロード ページ](./media/virtual-machines-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. .publishsettings ファイルを保存します。

4. 次のコマンドを入力します: `Import-AzurePublishSettingsFile <PathToFile>`

	ここで、`<PathToFile>` は .publishsettings ファイルへの完全なパスです。

   詳細については、「[Microsoft Azure コマンドレットの概要](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)」を参照してください。
	
   PowerShell のインストールと構成の詳細については、[Microsoft Azure PowerShell のインストールおよび構成の方法](../install-configure-powershell.md)に関するページを参照してください。

## ステップ 4: .vhd ファイルをアップロードする ##

.vhd ファイルをアップロードするときは、BLOB ストレージ内であればどこにでも .vhd ファイルを置くことができます。以下のコマンドの例では、**BlobStorageURL** は手順 2 で作成したストレージ アカウントの URL であり、**YourImagesFolder** は BLOB ストレージ内でイメージを格納するコンテナーです。**VHDName** は、仮想ハード ディスクを識別するために管理ポータルに表示されるラベルです。**PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。


1. 前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

		Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>		

## ステップ 5: アップロードした VHD で VM を作成する ##
.vhd をアップロードしたら、サブスクリプションに関連付けられたカスタム イメージの一覧にイメージとして追加し、そのカスタム イメージから仮想マシンを作成できます。

1. 前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

		Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    **重要**: 現時点では OS の種類として Linux を使用してください。これは、現在の Azure PowerShell のバージョンでは、パラメーターとして “Linux” または “Windows” しか使用できないためです。

2. ここまでの手順を完了すると、Azure の管理ポータルで **[イメージ]** タブをクリックしたときに、新しいイメージが一覧に表示されます。

    ![add image](./media/virtual-machines-freebsd-create-upload-vhd/addfreebsdimage.png)

3. ギャラリーから仮想マシンを作成します。これで **[マイ イメージ]** で新しいイメージが使用可能になりました。新しいイメージを選択し、表示される画面でホスト名、パスワード、SSH キーなどを設定します。

	![カスタム イメージ](./media/virtual-machines-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. プロビジョニングが完了したら、Azure での FreeBSD VM の稼働を確認できます。

	![freebsd image in azure](./media/virtual-machines-freebsd-create-upload-vhd/freebsdimageinazure.png)

<!---HONumber=58--> 