<properties
	pageTitle="Linux VHD の作成とアップロード | Microsoft Azure"
	description="Linux オペレーティング システムを格納したクラシック デプロイメント モデルを使用して Azure 仮想ハード ディスク (VHD) を作成してアップロードします。"
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
	ms.date="06/14/2016"
	ms.author="iainfou"/>

# Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

ここでは、仮想ハードディスク (VHD) を作成およびアップロードし、それをイメージとして活用して Azuere 内で仮想マシンを作成する方法を示します。そのイメージに基づいて複数の仮想マシンを作成できるよう、オペレーティング システムを準備する方法についても説明します。

**重要**: Azure プラットフォームの SLA は、動作保証済みディストリビューションのいずれか 1 つを、[Linux on Azure-Endorsed Distributions (Azure で承認されているディストリビューション Linux)](virtual-machines-linux-endorsed-distros.md) の「サポートされているバージョン」で指定されている構成で使用した場合にのみ、Linux OS を実行する仮想マシンに適用されます。Azure イメージ ギャラリーにあるすべての Linux ディストリビューションは、必須の構成による動作保証済みディストリビューションです。


## 前提条件
この記事では、次の項目があることを前提としています。

- **.vhd ファイルにインストールされている Linux オペレーティング システム** - [動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md) (または [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md) を参照してください) を VHD 形式で仮想ディスクにインストールします。.vhd ファイルを作成するツールは複数あります。たとえば、Hyper-V などの仮想化ソリューションにより、.vhd ファイルを作成し、オペレーティング システムをインストールできます。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

	> [AZURE.NOTE] 新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V Manager または `Convert-VHD` コマンドレットを使用して、ディスクを VHDX 形式から VHD 形式に変換できます。Azure では動的 VHD のアップロードはサポートしないため、そのようなディスクは、アップロードする前に静的 VHD に変換する必要があります。動的ディスクの変換には、[Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用できます。

- **Azure コマンド ライン インターフェイス** - 最新の[Azure コマンド ライン インターフェイス](../virtual-machines-command-line-tools.md)をインストールして使用し、VHD をアップロードします。

<a id="prepimage"> </a>
## 手順 1. アップロードするイメージを準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md)」を参照してください)。次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。以下のガイドに説明されている次の手順を行うと、Azure にアップロードする VHD ファイルの準備が整います。

- **[CentOS ベースのディストリビューション](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES と openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[その他 - 動作保証外のディストリビューション](virtual-machines-linux-create-upload-generic.md)**

Azure で Linux イメージを準備する際のその他のヒントについては、「**[Linux のインストールに関する注記](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**」を参照してください。


<a id="connect"> </a>
## 手順 2. Azure への接続を準備する

クラシック デプロイメント モデル (`azure config mode asm`) で Azure CLI を使用していることを確認し、次のように、自分のアカウントにログインします。

```
azure login
```


<a id="upload"> </a>
## 手順 3. Azure にイメージをアップロードする

VHD ファイルをアップロードするストレージ アカウントが必要になります。既存のストレージ アカウントを選択するか、[新しいストレージ アカウントを作成する](../storage/storage-create-storage-account.md)ことができます。

Azure CLI でイメージをアップロードするには、次のコマンドを使用します。

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

前の例を使うと、次のようになります。

- **BlobStorageURL** は、使用する予定のストレージ アカウントの URL です。
- **YourImagesFolder** は、Blob Storage 内でイメージを格納するコンテナーです。
- **VHDName** は、仮想ハード ディスクを識別するためにポータルに表示されるラベルです。
- **PathToVHDFile** は、コンピューター上の .vhd ファイルの完全なパスとファイル名です。

詳細については、[Azure サービス管理の Azure CLI のリファレンス](../virtual-machines-command-line-tools.md)を参照してください。

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0629_2016-->