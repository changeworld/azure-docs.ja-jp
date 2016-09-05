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

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Azure Resource Manager を使用してカスタム ディスク イメージをアップロードする](virtual-machines-linux-upload-vhd.md)こともできます。

ここでは、仮想ハードディスク (VHD) を作成およびアップロードし、それをイメージとして活用して Azuere 内で仮想マシンを作成する方法を示します。そのイメージに基づいて複数の仮想マシンを作成できるよう、オペレーティング システムを準備する方法についても説明します。


## 前提条件
この記事では、次の項目があることを前提としています。

- **.vhd ファイルにインストールされている Linux オペレーティング システム** - [動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md) (または [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md) を参照してください) を VHD 形式で仮想ディスクにインストールします。VM と VHD を作成するツールはいくつかあります。
	- [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](http://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。必要であれば `qemu-img convert` を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
	- [Windows 10 上](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)または [Windows Server 2012/2012 R2 上](https://technet.microsoft.com/library/hh846766.aspx)の Hyper-V を使用することもできます。

> [AZURE.NOTE] 新しい VHDX 形式は、Azure ではサポートされていません。VM を作成するときに、形式として VHD を指定します。必要であれば、[`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) の PowerShell コマンドレットを使用して VHDX ディスクを VHD に変換できます。Azure では動的 VHD のアップロードはサポートしないため、そのようなディスクは、アップロードする前に静的 VHD に変換する必要があります。Azure へのアップロード プロセス中、動的ディスクの変換には、[Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用できます。

- **Azure コマンド ライン インターフェイス** - 最新の[Azure コマンド ライン インターフェイス](../virtual-machines-command-line-tools.md)をインストールして使用し、VHD をアップロードします。

<a id="prepimage"> </a>
## 手順 1. アップロードするイメージを準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md)」を参照してください)。次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。以下のガイドで説明されている手順を完了すると、Azure にアップロードする VHD ファイルの準備が整います。

- **[CentOS ベースのディストリビューション](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES と openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[その他 - 動作保証外のディストリビューション](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] Azure プラットフォームの SLA は、動作保証済みディストリビューションのいずれか 1 つを、[Linux on Azure-Endorsed Distributions (Azure で承認されているディストリビューション Linux)](virtual-machines-linux-endorsed-distros.md) の「サポートされているバージョン」で指定されている構成で使用した場合にのみ、Linux OS を実行する仮想マシンに適用されます。Azure イメージ ギャラリーにあるすべての Linux ディストリビューションは、必須の構成による動作保証済みディストリビューションです。

Azure で Linux イメージを準備する際のその他のヒントについては、**[Linux のインストールに関する注記](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**をご覧ください。


<a id="connect"> </a>
## 手順 2. Azure への接続を準備する

クラシック デプロイメント モデル (`azure config mode asm`) で Azure CLI を使用していることを確認し、次のように、自分のアカウントにログインします。

```
azure login
```


<a id="upload"> </a>
## 手順 3. Azure にイメージをアップロードする

VHD ファイルをアップロードするストレージ アカウントが必要です。既存のストレージ アカウントを選択することも、[新しいストレージ アカウントを作成する](../storage/storage-create-storage-account.md)こともできます。

Azure CLI でイメージをアップロードするには、次のコマンドを使用します。

```bash
azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>
```

前の例を使うと、次のようになります。

- **BlobStorageURL** は、使用する予定のストレージ アカウントの URL です。
- **YourImagesFolder** は、イメージを格納する Blob Storage 内のコンテナーです。
- **VHDName** は、仮想ハード ディスクを識別するためにポータルに表示されるラベルです。
- **PathToVHDFile** は、コンピューター上の .vhd ファイルの完全なパスとファイル名です。

完全な例を次に示します。

```bash
azure vm image create UbuntuLTS --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd --os Linux /home/ahmet/UbuntuLTS.vhd
```

## 手順 4: イメージから VM を作成する
通常の VM と同じように、`azure vm create`使用して VM を作成します。前の手順でイメージに付けた名前を指定します。次の例では、前の手順で指定したイメージの名前 **UbuntuLTS** を使用します。

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh --location "West US" "DeployedUbuntu" UbuntuLTS
```

独自の VM を作成するには、独自のユーザー名とパスワード、場所、DNS 名、およびイメージ名を指定します。

## 次のステップ

詳細については、[Azure クラシック デプロイメント モデルの Azure CLI リファレンス](../virtual-machines-command-line-tools.md)を参照してください。

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0824_2016-->