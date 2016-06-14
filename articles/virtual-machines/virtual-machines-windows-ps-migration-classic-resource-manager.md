<properties
	pageTitle="Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する | Microsoft Azure"
	description="この記事では、PowerShell スクリプトを使用した、プラットフォームでサポートされているクラシックから Azure Resource Manager へのリソースの移行について説明します"
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

以下の手順では、Azure PowerShell コマンドを使用して、サービスとしてのインフラストラクチャ (IaaS) のリソースをクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行する方法を説明します。以下の手順では、空欄に記入する手法でカスタム環境を移行します。コマンドを利用し、環境に合わせた値を変数 ("$" で始まる行) に入力するだけです。

## 手順 1. 移行を準備する

ここでは、クラシックから Resource Manager への IaaS リソースの移行を評価するときに推奨できるベスト プラクティスをいくつか紹介します。

- [サポートされていない構成または機能の一覧](virtual-machines-windows-migration-classic-resource-manager.md)をご確認ください。サポートされていない構成または機能を使用する仮想マシンがある場合、構成または機能のサポートが発表されるまで待つことをお勧めします。または、必要に応じて、その機能を削除するか、その構成を外して、移行を可能にすることもできます。
-	インフラストラクチャとアプリケーションをデプロイする自動化スクリプトを今お持ちの場合は、これらのスクリプトを移行に使用して、同様のテスト設定を作成してみてください。または、Azure ポータルを使用してサンプル環境をセットアップすることもできます。
- このサービスはパブリック プレビュー段階なので、移行用のテスト環境は、必ず運用環境から分離してください。また、テスト環境と運用環境で、ストレージ アカウント、仮想ネットワークなどのリソースが混在しないようにしてください。

## 手順 2: Azure PowerShell の最新バージョンをインストールする

インストールには、[PowerShell ギャラリー](https://www.powershellgallery.com/profiles/azure-sdk/)と [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps) という 2 つの大きなオプションがあります。WebPI は月次の更新プログラムを受け取ります。PowerShell ギャラリーは、継続的に更新プログラムを受け取ります。

詳細については、「[Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/)」を参照してください。

## 手順 3: サブスクリプションを設定し、移行のパブリック プレビューにサインアップする

まず PowerShell プロンプトを開始します。移行のシナリオの場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。

Resource Manager モデルの自分のアカウントにサインインします。

	Login-AzureRmAccount

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

現在のセッション用の Azure サブスクリプションを設定します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

次のコマンドを使用して、パブリック プレビューにサインアップします。

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

登録が完了するまで 5 分間お待ちください。承認の状態を確認するには、次のコマンドを使用します。RegistrationState が `Registered` であることを確認してから続行してください。

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

ここで、クラシック モデルの自分のアカウントにサインインします。

	Add-AzureAccount

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

現在のセッション用の Azure サブスクリプションを設定します。引用符内のすべての文字 (< and > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## 手順 4: IaaS リソースを移行するコマンドを実行する

>[AZURE.NOTE] ここで説明するすべての操作がべき等です。サポートされていない機能や構成エラー以外の問題が発生した場合は、準備、中止、またはコミット操作を再試行することをお勧めします。これによりプラットフォームでアクションが再試行されます。

### クラウド サービス内 (仮想ネットワーク外) で仮想マシンを移行する

次のコマンドを使用してクラウド サービスの一覧を取得し、移行するクラウド サービスを選択します。クラウド サービスの VM が仮想ネットワーク内にある場合、または Web/worker ロールを持つ場合は、エラー メッセージが返されます。

	Get-AzureService | ft Servicename

次のコマンドを使用して、クラウド サービスのデプロイ名を取得します。

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

移行に合わせてクラウド サービスの仮想マシンを準備します。2 つのオプションから選択できます。

プラットフォームで作成した仮想ネットワークに VM を移行する場合は、次のコマンドを使用します。

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Resource Manager デプロイメント モデルの既存の仮想ネットワークに移行する場合は、次のコマンドを使用します。

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

準備操作が成功したら、VM の移行状態を問い合わせて、VM が `Prepared` 状態であることを確認します。

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

PowerShell または Azure ポータルを使用して、準備したリソースの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### 仮想ネットワークの仮想マシンを移行する

移行する仮想ネットワークを選択します。仮想ネットワークに Web/worker ロールが含まれる場合、またはサポートされない構成の VM が含まれる場合は、検証エラー メッセージが返されます。

次のコマンドを使用して、移行対象の仮想ネットワークを準備します。

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

PowerShell または Azure ポータルを使用して、準備した仮想マシンの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## 次のステップ

- [プラットフォームでサポートされているクラシックから Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
- [プラットフォームでサポートされているクラシックから Resource Manager への移行に関する技術的な詳細](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [コミュニティ PowerShell スクリプトを使用して Azure Resource Manager にクラシック仮想マシンを複製する](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0601_2016-->