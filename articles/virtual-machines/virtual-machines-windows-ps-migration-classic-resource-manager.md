<properties
	pageTitle="Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する"
	description="この記事では、PowerShell スクリプトを使用した、プラットフォームでサポートされるサービス管理から Azure Resource Manager への移行サービス機能について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する

ここでは、Azure PowerShell コマンドを使用して IaaS リソースをクラシックからリソース マネージャーに移行する手順について説明します。以下の手順では、空欄に記入する手法でカスタム環境を移行します。コマンドを利用し、環境に合わせた値を変数 ("$" で始まる行) に入力するだけです。

## 手順 1: 移行を準備する

ここでは、クラシックから Resource Manager への IaaS リソースの移行を評価するときに推奨できるベスト プラクティスをいくつか紹介します。

- サポートされていない構成または機能の一覧については、[こちら](virtual-machines-windows-migration-classic-resource-manager.md)を参照してください。サポートされていない構成または機能を使用する Virtual Machines がある場合、機能や構成のサポートが発表されるまで待つことをお勧めします。または、必要に応じて、その機能を削除するか、その構成を外して、移行を可能にすることもできます。
-	現時点で、インフラストラクチャとアプリケーションをデプロイする自動スクリプトがある場合は、そのスクリプトを移行に使用して、同様のテスト セットアップを作成して見てください。または、Azure ポータルを使用してサンプル環境をセットアップすることもできます。
- このサービスはパブリック プレビュー段階なので、移行用のテスト環境は、必ず運用環境から分離してください。また、テスト環境と運用環境で、ストレージ アカウント、VNet などのリソースが混在しないようにしてください。

## 手順 2: 最新の Azure PowerShell をインストールする

インストールには、[PowerShell ギャラリー](https://www.powershellgallery.com/profiles/azure-sdk/)と [WebPI](http://aka.ms/webpi-azps) という 2 つの大きなオプションがあります。WebPI は月次の更新プログラムを受け取ります。PowerShell ギャラリーは、継続的に更新プログラムを受け取ります。

詳細については、「[Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/)」を参照してください。

## 手順 3: サブスクリプションを設定し、移行のパブリック プレビューにサインアップする

まず PowerShell プロンプトを開始します。移行のシナリオの場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。

リソース マネージャー モデルの自分のアカウントにログインします。

	Login-AzureRmAccount

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

現在のセッション用の Azure サブスクリプションを設定します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

次のコマンドを使用して、パブリック プレビューにサインアップします。

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

登録が完了するまで 5 分待ってください。次のコマンドを使用して、承認の状態を確認できます。次に進む前に、RegistrationState が 'Registered' であることを確認します。

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

クラシック モデルの自分のアカウントにログインします。

	Add-AzureAccount

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

現在のセッション用の Azure サブスクリプションを設定します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## 手順 4: IaaS リソースを移行するコマンド

>[AZURE.NOTE] 次に示すすべての操作はべき等であることに注意してください。サポートされていない機能や構成エラー以外の場合は、準備、中止またはコミット操作を再試行し、プラットフォームでアクションを再試行することをお勧めします。

### (Virtual Network ではなく) クラウド サービス内の Virtual Machines を移行する

次のコマンドを使用して Cloud Services の一覧を取得し、移行するクラウド サービスを選択します。Cloud Services の VM が VNet 内にある場合、または web/worker ロールがある場合、エラー メッセージが返されます。

	Get-AzureService | ft Servicename

次のコマンドを使用し、クラウド サービスのデプロイ名を取得する

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

移行に合わせてクラウド サービスの Virtual Machines を準備します。2 つのオプションから選択できます。

Virtual Network を作成したプラットフォームに VM を移行する場合、次のコマンドを使用します。

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Resource Manager デプロイメント モデルで既存の Virtual Network に移行する場合、次のコマンドを使用します。

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

準備操作が成功したら、VM の移行状態を問い合わせ、VM が 'Prepared' 状態であることを確認できます。

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

PowerShell または Azure ポータルを使用して、準備したリソースの構成を確認します。移行の準備ができておらず、古い状態に戻すには、次のコマンドを使用します。

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### Virtual Network の Virtual Machines を移行する

移行する Virtual Network を選択します。Virtual Network に web/worker ロールが含まれる場合、またはサポートされない構成の VM が含まれる場合、検証エラー メッセージが返されます。

次のコマンドを使用して、移行対象の仮想ネットワークを準備します。

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

PowerShell または Azure ポータルを使用して、準備した Virtual Machines の構成を確認します。移行の準備ができておらず、古い状態に戻すには、次のコマンドを使用します。

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## 参照

- [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
- [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [PowerShell スクリプトを使用して Azure Resource Manager にクラシック仮想マシンを複製する](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->