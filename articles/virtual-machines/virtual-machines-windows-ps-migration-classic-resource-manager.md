<properties
	pageTitle="PowerShell を使用した Resource Manager への移行 |Microsoft Azure"
	description="この記事では、Azure PowerShell コマンドを使用して、プラットフォームでサポートされているクラシックから Azure Resource Manager へ IaaS リソースを移行する方法について説明します"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="danlep"/>

# Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する

以下の手順では、Azure PowerShell コマンドを使用して、サービスとしてのインフラストラクチャ (IaaS) のリソースをクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行する方法を説明します。以下の手順では、空欄に記入する手法でカスタム環境を移行します。コマンドを利用し、環境に合わせた値を変数 ("$" で始まる行) に入力します。

必要に応じて、[Azure コマンド ライン インターフェイス (Azure CLI)](virtual-machines-linux-cli-migration-classic-resource-manager.md) を使用してリソースを移行することもできます。

サポートされる移行シナリオの背景については、「[プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)」を参照してください。詳細なガイダンスと移行のチュートリアルについては、「[プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)」を参照してください。

## 手順 1: 移行を計画する

ここでは、クラシックから Resource Manager への IaaS リソースの移行を評価するときに推奨できるベスト プラクティスをいくつか紹介します。

- [機能と構成のうちでサポートされるものとサポートされないもの](virtual-machines-windows-migration-classic-resource-manager.md)に関するページをご覧ください。サポートされていない構成または機能を使用する仮想マシンがある場合、構成または機能のサポートが発表されるまで待つことをお勧めします。または、必要に応じて、その機能を削除するか、その構成を外して、移行を有効にします。
-	インフラストラクチャとアプリケーションをデプロイする自動化スクリプトを今お持ちの場合は、これらのスクリプトを移行に使用して、同様のテスト設定を作成してみてください。または、Azure ポータルを使用してサンプル環境をセットアップすることもできます。

>[AZURE.IMPORTANT]現在、仮想ネットワーク ゲートウェイ (サイト間、Azure ExpressRoute 、アプリケーション ゲートウェイ、ポイント対サイト) は、クラシックから Resource Manager への移行には対応していません。ゲートウェイが設定された従来の仮想ネットワークを移行するには、コミット操作を実行してネットワークを移動する前に、まずそのゲートウェイを削除する必要があります(準備手順はゲートウェイを削除しなくても実行できます)。 その後、移行が完了してから Azure Resource Manager のゲートウェイに再接続します。

## 手順 2: Azure PowerShell の最新バージョンをインストールする

Azure PowerShell のインストールには、[PowerShell ギャラリー](https://www.powershellgallery.com/profiles/azure-sdk/)と [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps) という 2 つの主なオプションがあります。WebPI は月次の更新プログラムを受け取ります。PowerShell ギャラリーは、継続的に更新プログラムを受け取ります。この記事は、Azure PowerShell バージョン 2.1.0 に基づいています。

インストール指示については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

## 手順 3: サブスクリプションを設定し、移行にサインアップする

まず PowerShell プロンプトを開始します。移行のシナリオの場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。

Resource Manager モデルの自分のアカウントにサインインします。

	Login-AzureRmAccount

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

現在のセッション用の Azure サブスクリプションを設定します。引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

	Select-AzureRmSubscription –SubscriptionName "<subscription name>"

>[AZURE.NOTE] 登録は 1 回限りの手順ですが、移行を試みる前に実行する必要があります。登録を行わないと、次のエラー メッセージが表示されます。

>	*BadRequest : Subscription is not registered for migration.* 

次のコマンドを使用して、移行リソース プロバイダーに登録します。
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

登録が完了するまで 5 分間お待ちください。承認の状態は、次のコマンドで確認できます。

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

RegistrationState が `Registered` であることを確認してから続行してください。

ここで、クラシック モデルの自分のアカウントにサインインします。

	Add-AzureAccount

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

現在のセッション用の Azure サブスクリプションを設定します。引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

	Select-AzureSubscription –SubscriptionName "<subscription name>"

## 手順 4: 現在のデプロイメントまたは VNET の Azure リージョンで Azure Resource Manager 仮想マシンのコア数が十分にあることを確認する

Azure Resource Manager での現在のコア数は、次の PowerShell コマンドを使用して確認できます。コア クォータの詳細については、「[制限と Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)」をご覧ください。

```
Get-AzureRmVMUsage -Location "<Your VNET or Deployment's Azure region"
```

## 手順 5: IaaS リソースを移行するコマンドを実行する

>[AZURE.NOTE] ここで説明するすべての操作がべき等です。サポートされていない機能や構成エラー以外の問題が発生した場合は、準備、中止、またはコミット操作を再試行することをお勧めします。これによりプラットフォームでアクションが再試行されます。

### クラウド サービス内 (仮想ネットワーク外) で仮想マシンを移行する

次のコマンドを使用してクラウド サービスの一覧を取得し、移行するクラウド サービスを選択します。クラウド サービスの VM が仮想ネットワーク内にある場合、または Web ロールか worker ロールを持つ場合は、コマンドではエラー メッセージが返されます。

	Get-AzureService | ft Servicename

次のコマンドを使用して、クラウド サービスのデプロイ名を取得します。

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

移行に合わせてクラウド サービスの仮想マシンを準備します。2 つのオプションから選択できます。

* **オプション 1: プラットフォームで作成した仮想ネットワークに VM を移行する**

	最初に、次のコマンドを使用して、クラウド サービスを移行できるかどうかを検証します。

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
		$validate.ValidationMessages

	このコマンドによって、移行をブロックするすべての警告とエラーが表示されます。検証が成功した場合は、次の準備手順に進むことができます。

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

* **オプション 2: Resource Manager デプロイメント モデルの既存の仮想ネットワークに移行する**

		$existingVnetRGName = "<Existing VNET's Resource Group Name>"
		$vnetName = "<Virtual Network Name>"
		$subnetName = "<Subnet name>"

	最初に、次のコマンドを使用して、クラウド サービスを移行できるかどうかを検証します。

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
		$validate.ValidationMessages

	このコマンドによって、移行をブロックするすべての警告とエラーが表示されます。検証が成功した場合は、次の準備手順に進むことができます。

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

上記のいずれかのオプションで Prepare 操作が完了したら、VM の移行状態を照会します。VM の状態が `Prepared` であることを確認してください。

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

PowerShell または Azure ポータルを使用して、準備したリソースの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

準備した構成が正しい場合は、次に進み、以下のコマンドを使用してリソースをコミットできます。

	Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName

### 仮想ネットワークの仮想マシンを移行する

仮想ネットワーク内の仮想マシンを移行するには、その仮想ネットワークを移行します。仮想マシンは、ネットワークとともに自動的に移行されます。移行する仮想ネットワークを選択します。

	$vnetName = "<Virtual Network Name>"

>[AZURE.NOTE] 仮想ネットワークに Web ロールか worker ロールが含まれる場合、またはサポートされない構成の VM が含まれる場合は、検証エラー メッセージが返されます。

最初に、次のコマンドを使用して、仮想ネットワークを移行できるかどうかを検証します。

	Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

このコマンドによって、移行をブロックするすべての警告とエラーが表示されます。検証が成功した場合は、次の準備手順に進むことができます。
	
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Azure PowerShell または Azure Portal のどちらかを使用して、準備した仮想マシンの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

準備した構成が正しい場合は、次に進み、以下のコマンドを使用してリソースをコミットできます。

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### ストレージ アカウントを移行する

仮想マシンの移行が完了したら、ストレージ アカウントを移行することをお勧めします。

次のコマンドを使用して、各ストレージ アカウントの移行の準備をします。

	$storageAccountName = "<storage account name>"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Azure PowerShell または Azure Portal のどちらかを使用して、準備したストレージ アカウントの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

準備した構成が正しい場合は、次に進み、以下のコマンドを使用してリソースをコミットできます。

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## 次のステップ

- 移行の詳細については、「[プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)」を参照してください。
- Azure PowerShell を使用してさらにネットワーク リソースを Resource Manager に移行する場合は、**Move-AzureNetworkSecurityGroup**、**Move-AzureReservedIP**、**Move-AzureRouteTable** を使用して同様の手順を行います。
- クラシックから Resource Manager への Azure リソースの移行で使用できるオープンソース スクリプトについては、[Azure Resource Manager への移行用コミュニティ ツール](virtual-machines-windows-migration-scripts.md)に関するページを参照してください。

<!---HONumber=AcomDC_0921_2016-->