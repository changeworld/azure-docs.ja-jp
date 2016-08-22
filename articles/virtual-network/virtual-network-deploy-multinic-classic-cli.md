<properties
   pageTitle="クラシック デプロイメント モデルで Azure CLI を使用して複数 NIC の VM をデプロイする | Microsoft Azure"
   description="クラシック デプロイメント モデルで Azure CLI を使用して複数の NIC の VM をデプロイする方法を説明します"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#Azure CLI を使用した複数の NIC VM (クラシック) のデプロイ

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager モデルを使用してこれらの手順を実行する](virtual-network-deploy-multinic-arm-cli.md)方法について説明します。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

現時点では、NIC が 1 つの VM と、NIC が複数ある VM を、同じクラウド サービスに含めることはできません。したがって、このシナリオでは、バックエンド サーバーは、他のすべてのコンポーネントと異なるクラウド サービスで実装する必要があります。以下の手順では、メイン リソースに *IaaSStory* という名前のクラウド サービスを使用し、バックエンド サーバーに *IaaSStory-BackEnd* を使用します。

## 前提条件

バックエンド サーバーをデプロイするには、このシナリオで必要なすべてのリソースを含むメイン クラウド サービスをデプロイする必要があります。少なくとも、バックエンド用のサブネットを含む仮想ネットワークを作成する必要があります。仮想ネットワークをデプロイする方法については、「[Azure CLI を使用した仮想ネットワークの作成](virtual-networks-create-vnet-classic-cli.md)」を参照してください。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## バックエンド VM のデプロイ

バックエンド VM は、以下にリストしたリソースの作成に依存します。

- **データ ディスクのストレージ アカウント**。パフォーマンスを高めるために、データベース サーバー上のデータ ディスクはソリッド ステート ドライブ (SSD) テクノロジーを使用します。これには、Premium Storage アカウントが必要です。デプロイする Azure の場所が Premium Storage をサポートすることを確認してください。
- **NIC**。各 VM には 2 つの NIC があり、1 つはデータベース アクセス用で、もう 1 つは管理用です。
- **可用性セット**。メンテナンス中に少なくとも 1 つの VM が稼働し、実行されているようにするためには、すべてのデータベース サーバーを単一の可用性セットに追加します。

### 手順 1 - スクリプトの開始

使用するすべての Bash スクリプトは、[ここ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)からダウンロードできます。以下の手順に従って、ご使用の環境で機能するようにスクリプトを変更します。

1. 上記の[前提条件](#Prerequisites)でデプロイした既存のリソース グループに基づいて、以下の変数の値を変更します。

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. バックエンド デプロイメントに使用する値に基づいて、以下の変数の値を変更します。

		backendCSName="IaaSStory-Backend"
		prmStorageAccountName="iaasstoryprmstorage"
		image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskPrefix="db"
		dataDiskName="datadisk"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

### 手順 2 - VM 用に必要なリソースの作成

1. すべてのバックエンド VM 向けに新しいクラウド サービスを作成します。リソース グループ名のための `$backendCSName` 変数と Azure リージョンのための `$location` を使用していることに注意してください。

		azure service create --serviceName $backendCSName \
		    --location $location

2. VM で使用するデータ ディスクと OS の Premium Storage アカウントを作成します。

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS

### 手順 3 - 複数 NIC を持つ VM の作成

1. `numberOfVMs` 変数に基づいて、複数の VM を作成するループを開始します。

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. 各 VM について、2 つの NIC それぞれの名前と IP アドレスを指定します。

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x

		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. VM を作成します。名前、サブネット、IP アドレスを持つすべての NIC の一覧を含む `--nic-config` パラメーターを使用していることに注目します。

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. 各 VM あたり 2 つのデータ ディスクを作成します。

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### 手順 4 - スクリプトの実行

ニーズに合わせてスクリプトをダウンロードおよび変更したら、スクリプトを実行して複数の NIC を持つバックエンド データベース VM を作成してください。

1. スクリプトを保存し、**Bash** ターミナルから実行します。次のように、最初の出力が表示されます。

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name IaaSStory-Backend
		info:    service create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM

2. 数分後に実行が終了し、次のように、出力の残りの部分が表示されます。

		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM
		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0810_2016-->