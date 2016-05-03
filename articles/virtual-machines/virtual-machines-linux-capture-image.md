<properties
	pageTitle="テンプレートとして使用する Linux VM をキャプチャする | Microsoft Azure"
	description="Azure リソース マネージャー デプロイ モデルで作成された Linux ベースの Azure 仮想マシン (VM) のイメージをキャプチャする方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="danlep"/>


# リソース マネージャーのテンプレートとして使用する Linux 仮想マシンをキャプチャする方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-linux-classic-capture-image.md)。


Linux を実行する Azure 仮想マシンを Azure コマンド ライン インターフェイス (CLI) を使用してキャプチャし、それを Azure Resource Manager テンプレートとして使用して他の仮想マシンを作成します。このテンプレートでは、仮想マシンに接続された OS ディスクやデータ ディスクが指定されています。このテンプレートには、Azure リソース マネージャー VM を作成する際に必要な仮想ネットワーク リソースは含まれていないので、ほとんどの場合は、テンプレートを使用して別の仮想マシンを作成する前に、目的の仮想ネットワーク リソースを別途セットアップする必要があります。

## 開始する前に

これらの手順では、既に Azure リソース マネージャー デプロイ モデルによって Azure 仮想マシンが作成され、データ ディスクの接続やその他のカスタマイズ (たとえば、アプリケーションのインストールなど) を含むオペレーティング システムの構成が完了していると仮定します。この操作を行う方法は、いくつかあります。たとえば、Azure CLI を使用する方法などです。この作業をまだ完了していない場合は、Azure CLI を Azure リソース マネージャー モードで使用するための次の手順を参照してください。

- [Azure リソース マネージャー テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](virtual-machines-linux-cli-deploy-templates.md)

たとえば、*MyResourceGroup* という名前のリソース グループを米国中部リージョンで作成するとします。次のような **azure vm quick-create** コマンドを使用して、Ubuntu 14.04 LTS VM をリソース グループにデプロイします。

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:latest -u <your-user-name> -p <your-password>

VM をプロビジョニングし実行したら、データ ディスクを接続しマウントすることができます。手順については、[こちら](virtual-machines-linux-add-disk)を参照してください。


## VM をキャプチャする

1. VM をキャプチャする準備ができたら、SSH クライアントを使用して VM に接続します。

2. SSH のウィンドウで、次のコマンドを入力します。**waagent** からの出力はこのユーティリティのバージョンによって多少異なる場合があることに注意してください。

	`sudo waagent -deprovision+user`

	このコマンドは、システムをクリーンアップし、再プロビジョニングに適した状態にします。この操作では次のタスクが実行されます。

	- すべての SSH ホスト キーの削除 (構成ファイルで Provisioning.RegenerateSshHostKeyPair が 'y' の場合)
	- /etc/resolv.conf 内のネームサーバー構成の消去
	- /etc/shadow の `root` ユーザーのパスワードの削除 (構成ファイルで Provisioning.DeleteRootPassword が 'y' の場合)
	- キャッシュされた DHCP クライアントのリースの削除
	- ホスト名を localhost.localdomain にリセット
	- (/var/lib/waagent から取得した) 前回プロビジョニングされたユーザー アカウントおよび関連データの削除

	>[AZURE.NOTE] プロビジョニング解除では、イメージを "一般化" する目的でファイルとデータが削除されます。このコマンドはイメージとしてキャプチャする VM に対して実行するだけとします。プロビジョニング解除により、イメージからすべての機密情報が削除されることや、イメージが第三者への再配布に適した状態になることが保証されるわけではありません。

3. 「**y**」と入力して続行します。**-force** パラメーターを追加すると、この確認手順を省略できます。

4. 「**exit**」と入力して、SSH クライアントを閉じます。

	>[AZURE.NOTE] 次の手順は、クライアント コンピューターに既に [Azure CLI がインストールされている](../xplat-cli-install.md)ことを前提としています。

5. クライアント コンピューターから Azure CLI を開き、Azure サブスクリプションにログインします。詳細については、「[Connect to an Azure subscription from the Azure CLI (Azure CLI から Azure サブスクリプションへの接続)](../xplat-cli-connect.md)」を参照してください。

6. 次のコマンドを使用して、リソース マネージャー モードになっていることを確認します。

	`azure config mode arm`

7. 次のコマンドを使用して、既にプロビジョニング解除されている VM を停止します。

	`azure vm stop –g <your-resource-group-name> -n <your-virtual-machine-name>`

8. 次のコマンドを使用して VM を汎用化します。

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. ここで、次のコマンドを使用してイメージとローカル ファイル テンプレートをキャプチャします。

	`azure vm capture <your-resource-group-name>  <your-virtual-machine-name> <your-vhd-name-prefix> -t <your-template-file-name.json>`

	このコマンドは、VM ディスクに対して指定された VHD 名のプレフィックスを使用して、汎用化された OS イメージを作成します。イメージ VHD ファイルは、既定では、元の VM が使用していたのと同じストレージ アカウントに作成されます。**-t** オプションを指定すると、イメージから新しい VM を作成する際に使用できるローカル JSON ファイル テンプレートが作成されます。

>[AZURE.TIP] イメージの場所を見つけるには、JSON ファイル テンプレートを開きます。**storageProfile** で、**システム** コンテナー内に位置する**イメージ**の **uri** を見つけます。たとえば、OS ディスク イメージの uri は `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` のようになります。

## キャプチャしたイメージから新しい VM をデプロイする
ここで、イメージとテンプレートを使用して、新しい Linux VM を作成します。次の手順では、Azure CLI と、`azure vm capture` コマンドで作成した JSON ファイル テンプレートを使用して、新しい仮想ネットワークに VM を作成する方法を説明します。

### ネットワーク リソースを作成する

テンプレートを使用するには、まず、新しい VM に対して仮想ネットワークと NIC をセットアップする必要があります。これらのリソース用に新しいリソース グループを作成することをお勧めします。次に示すようなコマンドに、リソースの名前と適切な Azure の場所 (次のコマンドでは "centralus") を指定して実行します。

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

キャプチャ中に保存した JSON を使用して、イメージから仮想マシンをデプロイするには、NIC の ID が必要です。これを取得するには、次のコマンドを実行します。

	azure network nic show <your-new-resource-group-name> <your-nic-name>

出力内の **ID** は、次のような文字列となります。

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### 新しいデプロイを作成する
ここで、次のコマンドを実行して、キャプチャした VM イメージと保存したテンプレート JSON ファイルとから VM を作成します。

	azure group deployment create <your-new-resource-group-name> <your-new-deployment-name> -f <your-template-file-name.json>

新しい VM 名、管理者のユーザー名とパスワード、および前に作成した NIC の ID を指定するように求められます。

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

デプロイが成功した場合は、次のような出力が表示されます。

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------

	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### デプロイを検証する

ここで、作成した仮想マシンに SSH を使用して接続し、デプロイを検証し、新しい VM の使用を開始します。SSH を介して接続するには、次のコマンドを実行して、作成済みの VM の IP アドレスを見つけます。

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

コマンドの出力には、パブリック IP アドレスが一覧されます。既定では、ポート 22 で SSH を使用して Linux VM に接続します。

## テンプレートを使用して追加の仮想マシンを作成する

前のセクションで概説した手順を使用して追加の VM をデプロイするには、キャプチャしたイメージとテンプレートを使用します。

* VM イメージが、VM の VHD をホストするのと同じストレージ アカウントにあることを確認します。
* テンプレート JSON ファイルをコピーし、各 VM の VHD の **uri** として一意の値を入力します。
* 同じ仮想ネットワークまたは別の仮想ネットワークに新しい NIC を作成します。
* 変更したテンプレート JSON ファイルを使用して、仮想ネットワークをセットアップするリソース グループ内にデプロイメントを作成します。

イメージから VM を作成するときにネットワークが自動的にセットアップされるようにする場合は、GitHub の [101-vm-from-user-image テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)を使用してください。このテンプレートでは、カスタム イメージおよび必要な仮想ネットワークと、パブリック IP アドレスと、NIC リソースから仮想マシンを作成します。Azure ポータルでのテンプレートの使用に関する詳細なチュートリアルについては、「[ARM テンプレートを使用してカスタム イメージから仮想マシンを作成する方法](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)」を参照してください。

## azure vm create コマンドを使用する

一般には、リソース マネージャー テンプレートを使用して、イメージから VM を作成します。ただし、**azure vm create** コマンドと **-Q** (**--image-urn**) パラメーターを組み合わせて使用して VM を_強制的に_作成することができます。新しい VM の OS .vhd ファイルの場所を指定するために、**-d** (**--os-disk-vhd**) パラメーターも渡します。これは、イメージ VHD ファイルが格納されているストレージ アカウントの vhds コンテナー内でなければなりません。このコマンドは、新しい VM の VHD を自動的に vhds コンテナーにコピーします。

イメージで **azure vm create** を実行する前に次の操作を行います。

1.	新しいリソース グループを作成するか、デプロイ用に既存のリソース グループを特定します。

2.	新しい VM 用のパブリック IP アドレス リソースと NIC リソースを作成します。CLI を使用して仮想ネットワーク、パブリック IP アドレス、および NIC を作成する手順については、この記事の前の方を参照してください (**azure vm create** で新しい NIC を作成することもできますが、仮想ネットワークとサブネットの追加のパラメーターを渡す必要があります)。


次に、以下のようなコマンドを実行し、新しい OS VHD ファイルと既存のイメージの両方に URI を渡します。

	azure vm create <your-resource-group-name> <your-new-vm-name> eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/<your-new-VM-prefix>.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

追加のコマンド オプションについては、`azure help vm create` を実行してください。

## 次のステップ

CLI を使用して VM を管理するには、「[Azure リソース マネージャー テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](virtual-machines-linux-cli-deploy-templates.md)」に記載のタスクを参照してください。

<!---HONumber=AcomDC_0420_2016-->