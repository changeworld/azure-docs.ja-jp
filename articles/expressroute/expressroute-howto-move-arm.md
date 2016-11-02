<properties
   pageTitle="クラシックから Resource Manager への ExpressRoute 回線の移行 | Microsoft Azure"
   description="このページでは、クラシックの回線を Resource Manager デプロイ モデルに移行する方法について説明します。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>



# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行

## <a name="configuration-prerequisites"></a>構成の前提条件

- Azure PowerShell モジュールの最新バージョン (バージョン 1.0 以降) が必要になります。
- 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、および[ワークフロー](expressroute-workflows.md)を確認してください。
- 先に進む前に、 [クラシックから Resource Manager への ExpressRoute 回線の移行](expressroute-move.md)に関する記事に記載されている情報を確認してください。 サポートされる操作の制限と制約事項を完全に理解していることを確認します。
- Azure ExpressRoute 回線をクラシック デプロイ モデルから Azure Resource Manager デプロイ モデルに移行する場合は、クラシック デプロイ モデルで回線を完全に構成し、運用できる状態にしておく必要があります。
- Resource Manager デプロイ モデルで作成したリソース グループがあることを確認します。

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Resource Manager デプロイ モデルに ExpressRoute 回線を移行する

ExpressRoute 回線をクラシック デプロイ モデルと Resource Manager デプロイ モデルの両方で使用できるようにするには、ExpressRoute 回線を Resource Manager デプロイ モデルに移行する必要があります。 その際、次の PowerShell コマンドを実行します。

### <a name="step-1:-gather-circuit-details-from-the-classic-deployment-model"></a>手順 1: クラシック デプロイ モデルからの回線の詳細を収集する

まず、ExpressRoute 回線に関する情報を収集する必要があります。

Azure クラシック環境にサインインし、サービス キーを収集します。 次の PowerShell スニペットを使用して、情報を収集できます。

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

Resource Manager デプロイ モデルに移行する回線の **サービス キー** をコピーします。

### <a name="step-2:-sign-in-to-the-resource-manager-environment,-and-create-a-new-resource-group"></a>手順 2: Resource Manager 環境にサインインし、新しいリソース グループを作成する

新しいリソース グループを作成するには、次のスニペットを使用します。

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

既存のリソース グループがある場合は、それを使用できます。

### <a name="step-3:-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>手順 3: Resource Manager デプロイ モデルに ExpressRoute 回線を移行する

これで、クラシック デプロイ モデルから Resource Manager デプロイ モデルに ExpressRoute 回線を移行する準備が整いました。 先に進む前に、「 [クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](expressroute-move.md) 」に記載されている情報を確認してください。

その際、次のスニペットを実行します。

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] 移行が完了した後は、前のコマンドレットで表示される新しい名前を使用してリソースにアクセスします。 回線の名前は実質的に変更されます。

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>両方のデプロイ モデルに対して ExpressRoute 回線を有効にする

デプロイ モデルへのアクセスを制御する前に、ExpressRoute 回線を Resource Manager デプロイ モデルに移行する必要があります。

両方のデプロイ モデルへのアクセスを有効にするには、次のコマンドレットを実行します。

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

この操作が正常に完了すると、クラシック デプロイ モデルで回線を表示できるようになります。

ExpressRoute 回線の詳細を取得するには、次のコマンドを実行します。

    get-azurededicatedcircuit

表示されたサービス キーを確認できる必要があります。 これで、クラシック VNet の標準クラシック デプロイメント モデル コマンドと ARM VNET の標準 ARM コマンドを利用し、ExpressRoute のリンクを管理できるようになりました。 次の記事では、ExpressRoute 回線へのリンクを管理する方法について説明します。

- [Resource Manager デプロイ モデルの ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)
- [クラシック デプロイ モデルの ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>クラシック デプロイ モデルに対して ExpressRoute 回線を無効にする

クラシック デプロイ モデルへのアクセスを無効にするには、次のコマンドレットを実行します。

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

この操作が正常に完了すると、クラシック デプロイ モデルで回線を表示できなくなります。

## <a name="next-steps"></a>次のステップ

回線を作成したら、次の操作を必ず実行します。

- [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-arm.md)
- [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)



<!--HONumber=Oct16_HO2-->


