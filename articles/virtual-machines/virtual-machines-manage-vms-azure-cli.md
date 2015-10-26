<properties
   pageTitle="CLI を使用した仮想マシンの管理 | Microsoft Azure"
   description="Azure コマンド ライン インターフェイス (CLI) を使用して、Azure リソース マネージャー VM の管理を自動化します。"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/07/2015"
   ms.author="danlep"/>

# Mac、Linux、および Windows 用 Azure CLI を使用したリソース マネージャー仮想マシンの管理


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-how-to-automate-azure-resource-manager.md)。

Mac、Linux、および Windows 用の Azure コマンド ライン インターフェイス (CLI) を使用すると、VM を管理するために日常的に実行する多くのタスクを自動化できます。この記事では、比較的単純なタスクにはコマンド例を示し、より複雑なタスクには、コマンドが記載されている記事へのリンクを示します。

>[AZURE.NOTE]Azure CLI をまだインストールしていない場合は、[こちら](../xplat-cli-install.md)で手順を参照してください。また、[こちら](../xplat-cli-connect.md)でサブスクリプションへの接続方法を確認することもできます。さらに、Azure リソース マネージャー (arm) モードで CLI を構成する必要があります。

## サンプル コマンドを使用する方法
コマンド内のテキストの一部を、使用する環境に適した別のテキストに置き換える必要があります。記号の < and > は置き換える必要があるテキストであることを示しています。テキストを置き換える場合、記号は削除し、引用符はそのままの位置に残します。

> [AZURE.NOTE]プログラムによってコンソール コマンドの出力を格納および操作する場合は、**[jq](https://github.com/stedolan/jq)**、**[jsawk](https://github.com/micha/jsawk)** などの JSON 解析ツール、またはそのタスクに適した言語ライブラリを使用することをお勧めします。

## VM に関する情報の表示

これは頻繁に使用することになる基本的なタスクです。VM に関する情報の取得、VM でのタスクの実行、変数に格納する出力の取得に使用します。

VM に関する情報を取得するには、このコマンドを実行し、引用符内のすべての文字 (< and > を含む) を置き換えます。

     azure vm show -g <group name> -n <virtual machine name>

出力を JSON ドキュメントとして $vm 変数に格納するには、次のように実行します。

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)

または、stdout をファイルにパイプすることができます。

## Linux ベースの仮想マシンへのログオン

通常、Linux マシンは SSH によって接続されます。詳細については、「[Azure 上の Linux における SSH の使用方法](virtual-machines-linux-use-ssh-key.md)」をご覧ください。Azure リソース マネージャーの概要
## VM の停止

次のコマンドを実行します。

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]このパラメーターは、VM がクラウド サービス内の最後の VM である場合に、そのクラウド サービスの仮想 IP (VIP) を保持するために使用します。<br><br> StayProvisioned パラメーターを使用する場合は、その VM に対して引き続き課金されます。

## VM の起動

次のコマンドを実行します: Azure Resource Manager Overview azure vm start <group name> <virtual machine name>

## データ ディスクの接続

新しいディスクとデータを含むディスクのどちらを接続するかについても決める必要があります。新しいディスクの場合、コマンドによって .vhd ファイルが作成され、それが同じコマンドで接続されます。

新しいディスクを接続するには、次のコマンドを実行します。

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

既存のデータ ディスクを接続するには、次のコマンドを実行します。

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

## Linux VM の作成

Linux ベースの VM を新たに作成するには、いくつかの値を用意する必要があります。具体的には、リソース グループ名、場所、イメージ名、VM 名、バックアップの .vhd イメージを格納するためのストレージ アカウントなどです。使用する情報が揃ったら、次のように入力して、Azure CLI でその値の入力を求める対話型セッションを作成することができます。

    azure vm create

既にこれらの値が揃っている場合は、次のように入力して、値を直接渡すための適切なスイッチを検索することができます。`azure help vm create`

## 次のステップ

* Azure リソース マネージャー モードでの Azure CLI のその他の使用例については、「[Azure リソース マネージャーでの、Mac、Linux、および Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)」を参照してください。

* Azure リソースとその概念の詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。

<!---HONumber=Oct15_HO3-->