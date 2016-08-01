<properties
	pageTitle="カスタム Linux イメージの作成とアップロード | Microsoft Azure"
	description="Resource Manager デプロイメント モデルを使用して、仮想ハード ディスク (VHD) とカスタム Linux イメージを作成し、Azure にアップロードします。"
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
	ms.date="07/15/2016"
	ms.author="iainfou"/>

# カスタム ディスク イメージをアップロードして VM を作成する

この記事では、Resource Manager デプロイメント モデルを使用して仮想ハード ディスク (VHD) をアップロードし、そのカスタム イメージから VM を作成する方法について説明します。この機能によって、要件に合った Linux ディストリビューションをインストールして構成し、その VHD を使用して Azure 仮想マシン (VM) をすばやく作成することができます。

## クイック コマンド
[Azure CLI](../xplat-cli-install.md) でログインし、Resource Manager モードを使用していることを確認します (`azure config mode arm`)。

まず、リソース グループを作成します。

```bash
azure group create TestRG --location "WestUS"
```

仮想ディスクを保持するストレージ アカウントを作成します。

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

作成したストレージ アカウントのアクセス キーを一覧表示し、`key1` を書き留めます。

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
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

これでアップロード済みの仮想ディスクから VM を作成することができます。VM を作成するには、[Resource Manager テンプレートを使用する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)か、次のように CLI から対象ディスクの URI を指定します。

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

目標のストレージ アカウントは、仮想ディスクのアップロード先と同じである必要があります。また、`azure vm create` コマンドで求められるその他のパラメーターすべて (仮想ネットワーク、パブリック IP アドレス、ユーザー名、SSH キーなど) を指定するか、プロンプトで回答する必要もあります。詳細については、[Resource Manager の CLI から利用できるパラメーター](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)に関するトピックをご覧ください。


## 詳細な手順
カスタム Linux イメージの準備と、Azure へのそのイメージのアップロードに必要な手順は多数あります。この記事の残りの部分で、これまでに使用したクイック コマンドの各手順について詳しく説明します。


## 必要条件
以上の手順を実行するためには、次の要件を満たす必要があります。

- **.vhd ファイルにインストールされている Linux オペレーティング システム** - [動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md) (または[動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md)を参照してください) を VHD 形式で仮想ディスクにインストールします。VM と VHD を作成するツールはいくつかあります。
	- [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) または [KVM](http://www.linux-kvm.org/page/RunningKVM) をインストールして構成します。その際、イメージ形式として VHD を使用します。必要であれば `qemu-img convert` を使用して[イメージを変換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)できます。
	- [Windows 10 上](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)または [Windows Server 2012/2012 R2 上](https://technet.microsoft.com/library/hh846766.aspx)の Hyper-V を使用することもできます。

> [AZURE.NOTE] 新しい VHDX 形式は、Azure ではサポートされていません。VM を作成するときに、形式として VHD を指定します。必要であれば、[`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) または [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) の PowerShell コマンドレットを使用して VHDX ディスクを VHD に変換できます。Azure では動的 VHD のアップロードはサポートしないため、そのようなディスクは、アップロードする前に静的 VHD に変換する必要があります。Azure へのアップロード プロセス中、動的ディスクの変換には、[Azure VHD Utilities for GO (GO 用の Azure VHD Utilities)](https://github.com/Microsoft/azure-vhd-utils-for-go) などのツールを使用できます。

- カスタム イメージから作成する VM は、イメージ自体と同じストレージ アカウント内に存在する必要があります。
	- ストレージ アカウントと、カスタム イメージおよび作成した VM の両方を保持するコンテナーを作成します。
	- VM をすべて作成したら、イメージを安全に削除することができます。


<a id="prepimage"> </a>
## アップロードするイメージを準備する

Azure は、さまざまな Linux ディストリビューションをサポートしています (「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md)」を参照してください)。次の記事では、Azure でサポートされる以下のさまざまな Linux ディストリビューションを準備する方法について説明します。

- **[CentOS ベースのディストリビューション](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES と openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[その他 - 動作保証外のディストリビューション](virtual-machines-linux-create-upload-generic.md)**

Azure で Linux イメージを準備する際のその他のヒントについては、**[Linux のインストールに関する注記](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**をご覧ください。

> [AZURE.NOTE] [Azure プラットフォームの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) は、動作保証済みディストリビューションのいずれか 1 つを、「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md)」の "サポートされているバージョン" で指定されている構成で使用した場合にのみ、Linux を実行する VM に適用されます。


## リソース グループの作成
リソース グループは、仮想ネットワークやストレージ記憶域など、仮想マシンをサポートするすべての Azure リソースを論理的にまとめます。Azure リソース グループの詳細については[こちら](../resource-group-overview.md)をご覧ください。カスタム ディスク イメージをアップロードして VM を作成する前に、まずリソース グループを作成する必要があります。

```bash
azure group create TestRG --location "WestUS"
```

## ストレージ アカウントの作成
VM は、ストレージ アカウント内にページ BLOB として保存されます。Azure BLOB ストレージの詳細については[こちら](../storage/storage-introduction.md#blob-storage)をご覧ください。カスタム ディスク イメージおよび VM 用に、ストレージ アカウントを作成する必要があります。カスタム ディスク イメージから作成する VM は、必ずそのイメージと同じストレージ アカウント内に存在する必要があります。

先ほど作成したリソース グループ内にストレージ アカウントを作成します。

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

## ストレージ アカウント キーの一覧表示
Azure では、ストレージ アカウントごとに 2 つの 512 ビット アクセス キーが生成されます。これらのアクセス キーは、書き込み操作を実行する場合など、ストレージ アカウントを認証するときに使用します。ストレージへのアクセス管理の詳細については[こちら](../storage/storage-create-storage-account.md#manage-your-storage-account)をご覧ください。`azure storage account keys list` コマンドを実行すると、アクセス キーを表示できます。

作成したストレージ アカウントのアクセス キーは次のようにして表示します。

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
以降の手順でストレージ アカウントとのやり取りに使用するため、`key1` を書き留めます。

## ストレージ コンテナーを作成する
ローカル ファイル システムを論理的に整理するためにさまざまなディレクトリを作成するのと同様に、ストレージ アカウント内にコンテナーを作成して仮想ディスクとディスク イメージを整理します。ストレージ アカウントには、任意の数のコンテナーを含めることができます。

前の手順で取得したアクセス キーを指定して、新しいコンテナーを作成します。

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

## VHD をアップロードする
これで、カスタム ディスク イメージを実際にアップロードできるようになりました。VM で使用するすべての仮想ディスクと同様に、カスタム ディスク イメージをアップロードし、ページ BLOB として保存します。

アクセス キー、前の手順で作成したコンテナー、ローカル コンピューター上のカスタム ディスク イメージへのパスの順に指定する必要があります。

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

## カスタム イメージから VM を作成する
カスタム ディスク イメージから VM を作成する場合は、ディスク イメージへの URI を指定し、目標のストレージ アカウントとカスタム ディスク イメージの保存場所が一致している必要があります。Azure CLI または Resource Manager JSON テンプレートを使用して VM を作成することができます。


### Azure CLI を使用して VM を作成する
`--image-urn` (または単に `-Q`) パラメーターを `azure vm create` コマンドで指定して、カスタム ディスク イメージをポイントします。`--storage-account-name` (または `-o`) は、カスタム ディスク イメージが保存されているストレージ アカウントと必ず一致させます。カスタム ディスク イメージと同じコンテナーを使用して VM を保存する必要はありませんが、カスタム ディスク イメージをアップロードする前に、必ず前の手順と同様にして追加のコンテナーを作成してください。

次のようにカスタム ディスク イメージから VM を作成します。

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
	-o testuploadedstorage
```

`azure vm create` コマンドで求められるその他のパラメーターすべて (仮想ネットワーク、パブリック IP アドレス、ユーザー名、SSH キーなど) を指定するか、プロンプトで回答する必要があるので注意してください。詳細については、[リソース マネージャーの CLI から利用できるパラメーター](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)に関するトピックをご覧ください。

### JSON テンプレートを使用して VM を作成する
Azure Resource Manager のテンプレートは、ビルドする環境を定義する JavaScript Object Notation (JSON) ファイルです。このテンプレートは、コンピューティングやネットワークなどのさまざまなリソース プロバイダーに分類されます。既存のテンプレートを使用することも独自に作成することもできます。詳細については、[Resource Manager とテンプレートの使用方法](../resource-group-overview.md)に関する記事をご覧ください。

テンプレートの `Microsoft.Compute/virtualMachines` プロバイダー内に、VM の構成の詳細を格納する `storageProfile` ノードが作成されます。編集が必要な 2 つの主なパラメーターが `image` URI と `vhd` URI です。これらでそれぞれカスタム ディスク イメージと新しい VM の仮想ディスクをポイントします。以下に、カスタム ディスク イメージを使用する場合の JSON の例を示します。

```bash
"storageProfile": {
          "osDisk": {
            "name": "TestVM",
            "osType": "Linux",
            "caching": "ReadWrite",
			"createOption": "FromImage",
            "image": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd"
            },
            "vhd": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

[この既存のテンプレート使用してカスタム イメージから VM を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)か、[独自の Azure Resource Manager テンプレートの作成](../resource-group-authoring templates.md) に関するページを参照することができます。

テンプレートを構成したら、`azure group deployment create` コマンドを使用して VM を作成します。`--template-uri` パラメーターを使用して JSON テンプレートの URI を指定します。

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-uri https://uri.to.template/yourtemplate.json
```

JSON ファイルをコンピューターにローカルでに保存している場合は、代わりに `--template-file` パラメーターを使用することができます。

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-file /path/to/yourtemplate.json
```


## 次のステップ
カスタム仮想ディスクを準備してアップロードしたら、[リソース マネージャーとテンプレートの使用](../resource-group-overview.md)について学習しましょう。必要であれば、新しい VM に[データ ディスクを追加](virtual-machines-linux-add-disk.md)することもできます。VM 上で実行するアプリケーションがあり、これにアクセスする必要がある場合は、必ず[ポートとエンドポイント](virtual-machines-linux-nsg-quickstart.md)を開放してください。

<!---HONumber=AcomDC_0720_2016-->