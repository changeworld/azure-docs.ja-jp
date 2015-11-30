<properties
	pageTitle="Linux VHD の作成とアップロード | Microsoft Azure"
	description="Linux オペレーティング システムを格納したクラシック デプロイ モデルを使用して Azure 仮想ハード ディスク (VHD) を作成してアップロードします。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="dkshir"/>

# Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


ここでは、仮想ハードディスク (VHD) を作成およびアップロードし、それをイメージとして活用して Azuere 内で仮想マシンを作成する方法を示します。そのイメージに基づいて複数の仮想マシンを作成できるよう、オペレーティング システムを準備する方法についても説明します。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Azure の仮想マシンでは、仮想マシンの作成時に選択したイメージに基づいてオペレーティング システムが実行されます。イメージはストレージ アカウント内に VHD 形式 (.vhd ファイル) で保存されます。詳細については、「[Azure でのディスク](virtual-machines-disks-vhds.md)」および「[Azure でのイメージ](virtual-machines-images.md)」を参照してください。

仮想マシンを作成するときに、実行するアプリケーションに合わせてオペレーティング システムの一部の設定をカスタマイズすることができます。手順については、「[カスタム仮想マシンの作成方法](virtual-machines-create-custom.md)」を参照してください。

**重要**: Azure プラットフォームの SLA は、動作保証済みディストリビューションのいずれか 1 つを、[Linux on Azure-Endorsed Distributions (Azure で承認されているディストリビューション Linux)](virtual-machines-../linux-endorsed-distributions.md) の「サポートされているバージョン」で指定されている構成で使用した場合にのみ、Linux OS を実行する仮想マシンに適用されます。Azure イメージ ギャラリーにあるすべての Linux ディストリビューションは、必須の構成による動作保証済みディストリビューションです。


## 前提条件
この記事では、次の項目があることを前提としています。

- **管理証明書** - VHD をアップロードするサブスクリプションの管理証明書を作成し、その証明書を .cer ファイルにエクスポートした。証明書の作成方法の詳細については、「[Azure の証明書の概要](../cloud-services/cloud-services-certs-create.md)」を参照してください。

- **.vhd ファイルにインストールされている Linux オペレーティング システム** - サポートされている Linux オペレーティング システムを仮想ハード ディスクにインストールしておきます。.vhd ファイルを作成するツールは複数あります。たとえば、Hyper-V などの仮想化ソリューションにより、.vhd ファイルを作成し、オペレーティング システムをインストールできます。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

	**重要**: 新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

	動作保証済みディストリビューションの一覧については、「[Azure での動作保証済み Linux ディストリビューション](../linux-endorsed-distributions.md)」を参照してください。Linux ディストリビューションの総目録については、「[動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-vhd-generic.md)」を参照してください。

- **Azure コマンド ライン インターフェイス** - Linux オペレーティング システムを使用してイメージを作成する場合は、[Azure コマンド ライン インターフェイス](../virtual-machines-command-line-tools.md)を使用して VHD をアップロードします。

- **Azure Powershell ツール** - `Add-AzureVhd` コマンドレットを使用して、VHD をアップロードすることもできます。Azure Powershell コマンドレットをダウンロードするには、「[Azure Downloads (Azure のダウンロード)](http://azure.microsoft.com/downloads/)」を参照してください。詳細については、[Add-AzureVhd に関するページ](https://msdn.microsoft.com/library/azure/dn495173.aspx)を参照してください。

<a id="prepimage"> </a>
## 手順 1. アップロードするイメージを準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (「[Azure での動作保証済み Linux ディストリビューション](../linux-endorsed-distributions.md)」を参照してください)。次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。

- **[CentOS ベースのディストリビューション](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)**
- **[SLES と openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[その他 - 動作保証外のディストリビューション](virtual-machines-linux-create-upload-vhd-generic.md)**

Azure で Linux イメージを準備する際のその他のヒントについては、「**[Linux のインストールに関する注記](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)**」を参照してください。

上のガイドに説明されている次の手順を行うと、Azure にアップロードする VHD ファイルの準備が整います。

<a id="connect"> </a>
## 手順 2. Azure への接続を準備する

.vhd ファイルをアップロードする前に、コンピューターと Azure のサブスクリプションの間にセキュリティで保護された接続を確立する必要があります。


### Azure CLI を使用する場合

最新の Azure CLI は既定でリソース マネージャーのデプロイ モデルになっているので、必ず次のコマンドを使用してクラシック デプロイ モデルにしてください。

		azure change mode asm  

次に、以下のログイン方法のいずれかを使用して Azure サブスクリプションに接続します。

ログインに Azure AD のメソッドを使用します。

1. Azure CLI ウィンドウを開きます。

2. 次のコマンドを入力します。

	`azure login`

	メッセージが表示されたら、ユーザー名とパスワードを入力します。

**または**、PublishSettings ファイルを使用します。

1. Azure CLI ウィンドウを開きます。

2. 次のコマンドを入力します。

	`azure account download`

	このコマンドにより、ブラウザー ウィンドウが開き、Azure サブスクリプションの情報と証明書を含む .publishsettings ファイルが自動的にダウンロードされます。

3. .publishsettings ファイルを保存します。

4. 次のコマンドを入力します。

	`azure account import <PathToFile>`

	ここで、`<PathToFile>` は .publishsettings ファイルへの完全なパスです。

	詳細については、「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../xplat-cli-connect.md)」をご覧ください。


### Azure PowerShell を使用する場合

ログインに Azure AD のメソッドを使用します。

1. Azure PowerShell ウィンドウを開きます。

2. 次のコマンドを入力します。

	`Add-AzureAccount`

	メッセージが表示されたらは、組織のユーザー ID とパスワードを入力します。

**または**、代わりに PublishSettings ファイルを使用します。

1. Azure PowerShell ウィンドウを開きます。

2. 次のコマンドを入力します。

	`Get-AzurePublishSettingsFile`

	このコマンドにより、ブラウザー ウィンドウが開き、Azure サブスクリプションの情報と証明書を含む .publishsettings ファイルが自動的にダウンロードされます。

3. .publishsettings ファイルを保存します。

4. 次のコマンドを入力します。

	`Import-AzurePublishSettingsFile <PathToFile>`

	ここで、`<PathToFile>` は .publishsettings ファイルへの完全なパスです。

	詳細については、「[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)」を参照してください。

> [AZURE.NOTE]Azure サブスクリプションへのログインには、Azure CLI や Azure PowerShell のいずれかから、新しい Azure Active Directory メソッドを使用することをお勧めします。

<a id="upload"> </a>
## 手順 3. Azure にイメージをアップロードする

### Azure CLI を使用する場合

Azure CLI を使用して、イメージをアップロードします。次のコマンドを使用してイメージをアップロードできます。

		azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>

### PowerShell を使用する場合

VHD ファイルをアップロードするストレージ アカウントが必要になります。既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成することができます。ストレージ アカウントを作成するには、[「ストレージ アカウントの作成」](../storage-create-storage-account.md)を参照してください。

.vhd ファイルをアップロードするときは、BLOB ストレージ内であればどこにでも .vhd ファイルを置くことができます。以下のコマンドの例では、**BlobStorageURL** は使用する予定のストレージ アカウントの URL であり、**YourImagesFolder** は BLOB ストレージ内でイメージを格納するコンテナーです。**VHDName** は、仮想ハード ディスクを識別するために[管理ポータル](http://manage.windowsazure.com)に表示されるラベルです。**PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。

前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

詳細については、[Add-AzureVhd に関するページ](https://msdn.microsoft.com/library/azure/dn495173.aspx)を参照してください。

> [AZURE.NOTE] [Azure Powershell 1.0 Preview バージョン](https://azure.microsoft.com/zh-CN/blog/azps-1-0-pre/)では、クラシック デプロイメント モデルとリソース マネージャーのデプロイメント モデル用のコマンドレットを処理する方法が大幅に変更されます。この記事ではまだ Preview バージョンを使用していません。


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=Nov15_HO4-->