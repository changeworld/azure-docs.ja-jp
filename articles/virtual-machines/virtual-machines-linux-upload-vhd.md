<properties
	pageTitle="Linux VHD の作成とアップロード | Microsoft Azure"
	description="Resource Manager デプロイメント モデルを使用して仮想ハード ディスク (VHD) を作成し、Azure にアップロードします。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="iainfou"/>

# 仮想ハード ディスクのアップロード

この記事では、Resource Manager デプロイメント モデルを使用して仮想ハード ディスク (VHD) をアップロードする方法について説明します。この機能によって、要件に合った Linux ディストリビューションをインストールして構成し、その VHD を使用して Azure 仮想マシン (VM) をすばやく作成することができます。

## クイック コマンド
[Azure CLI](../xplat-cli-install.md) でログインし、Resource Manager モードを使用していることを確認します (`azure config mode arm`)。

まず、リソース グループを作成します。

```bash
azure group create TestRG --location "WestUS"
```

仮想ディスクを保持するストレージ アカウントを作成します。

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name LRS
```

作成したストレージ アカウントのストレージ キーを一覧表示し、`key1` を書き留めます。

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

次のように出力されます。

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

取得したストレージ キーを使用してストレージ アカウント内にコンテナーを作成します。

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

最後に、今作成したコンテナーに VHD をアップロードします。

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

これでアップロード済みの仮想ディスクから VM を作成することができます。VM を作成するには、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)を使用するか、次のように CLI から対象ディスクの URI を指定します。

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

ただし、この場合でも、`azure vm create` コマンドで求められるその他のパラメーター (仮想ネットワーク、パブリック IP アドレス、ユーザー名、SSH キーなど) は依然として必要なので注意してください。リソース マネージャーの CLI から利用できるパラメーターについては、[こちら](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)を参照してください。

## 必要条件
以上の手順を実行するためには、次の要件を満たす必要があります。

- **.vhd ファイルにインストールされている Linux オペレーティング システム** - [動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md) (または[動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md)を参照してください) を VHD 形式で仮想ディスクにインストールします。VM と VHD を作成するツールはいくつかあります。
	- [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](http://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。必要であれば `qemu-img convert` を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
	- [Windows 10 上](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)または [Windows Server 2012/2012 R2 上](https://technet.microsoft.com/library/hh846766.aspx)の Hyper-V を使用することもできます。

> [AZURE.NOTE] 新しい VHDX 形式は、Azure ではサポートされていません。VM を作成する際は、オリジナルの VHD 形式を指定してください。必要であれば、[`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) の PowerShell コマンドレットを使用して VHDX を VHD 形式に変換できます。Azure では動的 VHD のアップロードはサポートしないため、そのようなディスクは、アップロードする前に静的 VHD に変換する必要があります。Azure へのアップロード プロセス中、動的ディスクの変換には、[Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用できます。


<a id="prepimage"> </a>
## アップロードするイメージを準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md)」を参照してください)。次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。仮想ディスクをアップロードする前に、適切な準備作業を行ってください。

- **[CentOS ベースのディストリビューション](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES と openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[その他 - 動作保証外のディストリビューション](virtual-machines-linux-create-upload-generic.md)**

Azure で Linux イメージを準備する際のその他のヒントについては、「**[Linux のインストールに関する注記](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**」を参照してください。

> [AZURE.NOTE] [Azure プラットフォームの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) は、動作保証済みディストリビューションのいずれか 1 つを、「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md)」の "サポートされているバージョン" で指定されている構成で使用した場合にのみ、Linux を実行する VM に適用されます。

## 次のステップ
カスタム仮想ディスクを作成してアップロードしたら、[リソース マネージャーとテンプレートの使用](../resource-group-overview.md)について学習しましょう。必要であれば、新しい VM に[データ ディスクを追加](virtual-machines-linux-add-disk.md)することもできます。アクセスする必要のあるアプリケーションが VM 上で実行されている場合、[ポートとエンドポイント](virtual-machines-linux-nsg-quickstart.md)を開放する必要があります。

<!---HONumber=AcomDC_0713_2016-->