<properties pageTitle="Azure 上での Linux VHD の作成とアップロード" description="Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。" services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/13/2015" ms.author="kathydav, szarkos"/>

# Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード

ここでは、仮想ハードディスク (VHD) を作成およびアップロードし、それをイメージとして活用して Azuere 内で仮想マシンを作成する方法を示します。そのイメージに基づいて複数の仮想マシンを作成できるよう、オペレーティング システムを準備する方法についても説明します。  

> [AZURE.NOTE] この記事内の手順は、Azure VM の使用経験がなくても完了できます。ただし、Azure アカウントが必要です。数分で無料の試用アカウントを作成することができます。詳細については、[Azure アカウントの作成に関するページ](http://www.windowsazure.com/ja-jp/develop/php/tutorials/create-a-windows-azure-account/)を参照してください。 

Azure の仮想マシンでは、仮想マシンの作成時に選択したイメージに基づいてオペレーティング システムが実行されます。イメージはストレージ アカウント内に VHD 形式 (.vid ファイル) で保存されます。Azure でのディスクとイメージの詳細については、「[ディスクおよびイメージの管理](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj672979.aspx)」を参照してください。

仮想マシンを作成するときに、実行するアプリケーションに合わせてオペレーティング システムの一部の設定をカスタマイズすることができます。手順については、「[カスタム仮想マシンの作成方法](/ja-jp/manage/windows/how-to-guides/custom-create-a-vm/)」を参照してください。

**重要**:Azure プラットフォームの SLA は、動作保証済みディストリビューションのいずれか 1 つを[この記事](http://support.microsoft.com/kb/2805216)で指定されている構成で使用した場合にのみ、Linux OS を実行する仮想マシンに適用されます。Azure イメージ ギャラリーにあるすべての Linux ディストリビューションは、必須の構成による動作保証済みディストリビューションです。


##前提条件##
この記事では、次の項目があることを前提としています。

- **管理証明書** - VHD をアップロードするサブスクリプションの管理証明書を作成し、その証明書を .cer ファイルにエクスポートしました。証明書の作成方法の詳細については、「[Azure の管理証明書の作成とアップロード](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)」を参照してください。 

- **.vhd ファイルにインストールされている Linux オペレーティング システム** - サポートされている Linux オペレーティング システムを仮想ハード ディスクにインストールしておきます。.vhd ファイルを作成するツールは複数あります。たとえば、Hyper-V などの仮想化ソリューションにより、.vhd ファイルを作成し、オペレーティング システムをインストールできます。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。 

	**重要**:新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

	動作保証済みディストリビューションの一覧については、「[Azure での動作保証済み Linux ディストリビューション](../linux-endorsed-distributions)」を参照してください。または、この記事の末尾の「[動作保証外のディストリビューションに関する情報](../virtual-machines-linux-create-upload-vhd-generic)」のセクションを参照してください。

- **Linux 用 Azure コマンド ライン ツール** - Linux オペレーティング システムを使用してイメージを作成する場合は、[Mac および Linux 用 Azure コマンド ライン ツール](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409)を使用して VHD ファイルをアップロードします。

- **Azure Powershell ツール** -  `Add-AzureVhd` コマンドレットを使用して、VHD をアップロードすることもできます。Azure Powershell コマンドレットをダウンロードするには、「[Azure Downloads (Azure のダウンロード)](http://azure.microsoft.com/ja-jp/downloads/)」を参照してください。詳細については、[Add-AzureVhd に関するページ](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx)を参照してください。


このタスクの手順は次のとおりです。

- [手順 1.アップロードするイメージを準備する] []
- [手順 2.Azure にストレージ アカウントを作成する] []
- [手順 3.Azure への接続を準備する] []
- [手順 4.Azure にイメージをアップロードする] []

## <a id="prepimage"> </a>手順 1.アップロードするイメージを準備する ##

Microsoft Azure は、さまざまな Linux ディストリビューションをサポートしています (「[Azure での動作保証済み Linux ディストリビューション](../linux-endorsed-distributions)」を参照してください)。次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。

- **[CentOS ベースのディストリビューション](../virtual-machines-linux-create-upload-vhd-centos)**
- **[Oracle Linux](../virtual-machines-linux-create-upload-vhd-oracle)**
- **[SLES と openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](../virtual-machines-linux-create-upload-vhd-ubuntu)**
- **[その他 - 動作保証外のディストリビューション](../virtual-machines-linux-create-upload-vhd-generic)**

Azure で Linux イメージを準備する際のその他のヒントについては、「**[Linux のインストールに関する注記](../virtual-machines-linux-create-upload-vhd-generic/#linuxinstall)**」を参照してください。

上のガイドに説明されている次の手順を行うと、Azure にアップロードする VHD ファイルの準備が整います。


## <a id="createstorage"> </a>手順 2.Azure でストレージ アカウントを作成する ##

ストレージ アカウントは、ストレージ サービスにアクセスするための最高レベルの名前空間を表し、Azure サブスクリプションに関連付けられています。仮想マシンの作成に使用できる .vhd ファイルを Azure にアップロードするには、Azure のストレージ アカウントが必要です。ストレージ アカウントは、Azure の管理ポータルを使って作成できます。

1. Azure 管理ポータルにサインインします。

2. コマンド バーで、**[新規]** をクリックします。

	![Create storage account](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. **[ストレージ アカウント]**、**[簡易作成]** の順にクリックします。

	![Quick create a storage account](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. 次のようにフィールドを指定します。

	![Enter storage account details](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- **[URL]** で、ストレージ アカウントの URL で使用するサブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。
	
- ストレージ アカウントの場所またはアフィニティ グループを選択します。アフィニティ グループを指定することで、ストレージと同じデータ センターにクラウド サービスを配置できます。
 
- ストレージ アカウントの Geo レプリケーションを使用するかどうかを決定します。Geo レプリケーションは既定で有効です。このオプションでは、ユーザーのコスト負担なしで、データが 2 次拠点にコピーされるため、1 次拠点で対処できない大規模な障害が発生した場合に、2 次拠点にストレージをフェールオーバーすることができます。2 次拠点は自動的に割り当てられ、変更することはできません。法律上の要件または組織のポリシー上、クラウド方式のストレージの場所を厳格に管理する必要がある場合は、Geo レプリケーションを無効にすることができます。ただし、後で Geo レプリケーションを有効に戻すと、既存データを 2 次拠点にコピーするためのデータ転送料金が 1 回だけ発生することに注意してください。Geo レプリケーションなしのストレージ サービスも割引価格で提供されています。

5. [ストレージ アカウントの作成] をクリックします。

	作成したアカウントが **[ストレージ アカウント]** に表示されます。

	![Storage account successfully created](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="connect"> </a>手順 3.Azure への接続を準備する ##

.vhd ファイルをアップロードする前に、コンピューターと Azure のサブスクリプションの間にセキュリティで保護された接続を確立する必要があります。 

1. Azure PowerShell ウィンドウを開きます。

2. 次のコマンドを入力します。 

	`Get-AzurePublishSettingsFile`

	このコマンドにより、ブラウザー ウィンドウが開き、Azure サブスクリプションの情報と証明書を含む .publishsettings ファイルが自動的にダウンロードされます。 

3. .publishsettings ファイルを保存します。 

4. 次のコマンドを入力します。

	`Import-AzurePublishSettingsFile <PathToFile>`

	ここで、`<PathToFile>` は .publishsettings ファイルへの完全なパスです。 

	詳細については、「[Azure コマンドレットの概要](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554332.aspx)」を参照してください。 


## <a id="upload"> </a>手順 4.Azure にイメージをアップロードする ##

.vhd ファイルをアップロードするときは、BLOB ストレージ内であればどこにでも .vhd ファイルを置くことができます。以下のコマンドの例では、**BlobStorageURL** は手順 2 で作成したストレージ アカウントの URL であり、**YourImagesFolder** は BLOB ストレージ内でイメージを格納するコンテナーです。**VHDName** は、仮想ハード ディスクを識別するために管理ポータルに表示されるラベルです。**PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。 

次のいずれかを実行します。

- 前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

		`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	詳細については、[Add-AzureVhd に関するページ](http://msdn.microsoft.com/ja-jp/library/windowsazure/dn205185.aspx)を参照してください。

- Linux コマンド ライン ツールを使用してイメージをアップロードします。次のコマンドを使用してイメージをアップロードできます。

		# azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>



[手順 1.アップロードするイメージを準備する]: #prepimage
[手順 2.Azure にストレージ アカウントを作成する]: #createstorage
[手順 3.Azure への接続を準備する]: #connect
[手順 4.Azure にイメージをアップロードする]: #upload



<!--HONumber=42-->
