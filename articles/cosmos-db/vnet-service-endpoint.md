---
title: Azure Virtual Network サービス エンドポイントを使用して Azure Cosmos DB アカウントへのアクセスをセキュリティで保護する | Microsoft Docs
description: このドキュメントでは、Azure Cosmos DB 仮想ネットワーク サービス エンドポイントのセットアップに必要な手順について説明します。
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: e6b263c1eb9fe3b151f0a51b5da9a92b8ced4549
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109744"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Azure Virtual Network サービス エンドポイントを使用して Azure Cosmos DB アカウントへのアクセスをセキュリティで保護する

Azure CosmosDB アカウントは、Azure Virtual Network の特定のサブネットからのアクセスのみを許可するように構成することができます。 Virtual Network とそのサブネットの Azure CosmosDB の[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を有効にすることにより、トラフィックに Azure Cosmos DB への最適で安全なルートが確保されます。  

Azure Cosmos DB は、グローバル分散型のマルチモデル データベース サービスです。 Azure Cosmos DB アカウント内のデータは、複数のリージョンにレプリケートすることができます。 Azure Cosmos DB が 1 つの仮想ネットワーク サービス エンドポイントで構成されている場合、各仮想ネットワークは、特定のサブネットに属している IP からのアクセスを許可します。 次の図は、仮想ネットワーク サービス エンドポイントが有効になっている Azure Cosmos DB を示しています。

![仮想ネットワーク サービス エンドポイントのアーキテクチャ](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Azure Cosmos DB アカウントは、仮想ネットワーク サービス エンドポイントを使用して構成されると、指定されたサブネットからのみアクセスできるようになり、すべてのパブリック/インターネット アクセスは削除されます。 サービス エンドポイントの詳細については、Azure の[仮想ネットワーク サービス エンドポイントの概要](../virtual-network/virtual-network-service-endpoints-overview.md)に関する記事を参照してください。

> [!NOTE]
> 現在、Virtual Network サービス エンドポイントは、Azure Cosmos DB SQL API または Mongo API アカウント用に構成できます。 その他の API やソブリン クラウド (Azure Germany、Azure Government など) 用にサービス エンドポイントを構成する機能は、間もなく利用できるようになります。 Azure Cosmos DB アカウント用に既存の IP ファイアウォールが構成されている場合は、ファイアウォールの構成を書き留め、IP ファイアウォールを削除してから、サービス エンドポイント ACL を構成してください。 サービス エンドポイントを構成した後、必要な場合は IP ファイアウォールを再度有効にすることができます。

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Azure Portal を使用してサービス エンドポイントを構成する
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>既存の Azure 仮想ネットワークとサブネット用のサービス エンドポイントを構成する

1. **[すべてのリソース]** ブレードで、Azure Cosmos DB 用にサービス エンドポイントを構成する仮想ネットワークを見つけます。 **[サービス エンドポイント]** ブレードに移動し、仮想ネットワークのサブネットが "Azure.CosmosDB" サービス エンドポイントに対して有効になっていることを確認します。  

   ![サービス エンドポイントが有効になっていることを確認する](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. **[すべてのリソース]** ブレードで、セキュリティで保護する Azure Cosmos DB アカウントを見つけます。  

3. 仮想ネットワーク サービス エンドポイントを有効にする前に、将来使用できるように、Azure Cosmos DB アカウントに関連付けられている IP ファイアウォール情報をコピーしておいてください。 サービス エンドポイントを構成した後、IP ファイアウォールを再度有効にすることができます。  

4. 設定メニューの **[ファイアウォールと仮想ネットワーク]** を選択し、**[選択されたネットワーク]** からのアクセスを許可するように選択します。  

3. 既存の仮想ネットワークのサブネットへのアクセスを許可するには、[仮想ネットワーク] で **[Add existing Azure virtual network]\(既存の Azure 仮想ネットワークを追加\)** を選択します。  

4. 追加する Azure 仮想ネットワークの**サブスクリプション**を選択します。 Azure Cosmos DB アカウントへのアクセスを提供する Azure **仮想ネットワーク**と**サブネット**を選択します。 次に、**[有効化]** を選択して、サービス エンドポイントを持つ選択したネットワークを "Microsoft.AzureCosmosDB" に対して有効にします。 完了したら、**[追加]** を選択します。  

   ![仮想ネットワークとサブネットを選択する](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Azure Cosmos DB のサービス エンドポイントが、選択した Azure 仮想ネットワークとサブネットに対してまだ構成されていない場合は、この操作の一部として構成することができます。 アクセスが有効になるまでに、最大で 15 分かかることがあります。 後で再び有効にするために、IP ファイアウォールの ACL の内容を書き留めた後に、IP ファイアウォールを無効にすることが重要です。 

   ![正常に構成された仮想ネットワークとサブネット](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

これで、Azure Cosmos DB アカウントが、この選択したサブネットからのトラフィックのみを許可するようになります。 以前に IP ファイアウォールを有効にしていた場合は、以前の情報を使用して再度有効にしてください。

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>新しい Azure 仮想ネットワークとサブネット用のサービス エンドポイントを構成する

1. **[すべてのリソース]** ブレードで、セキュリティで保護する Azure Cosmos DB アカウントを見つけます。  

> [!NOTE]
> Azure Cosmos DB アカウント用に既存の IP ファイアウォールが構成されている場合は、ファイアウォールの構成を書き留め、IP ファイアウォールを削除してから、サービス エンドポイントを有効にしてください。 ファイアウォールを無効にせずにサービス エンドポイントを有効にすると、その IP 範囲からのトラフィックは仮想 IP ID を失い、IP フィルター エラー メッセージでドロップされます。 そのため、このエラーを防ぐには、ファイアウォール ルールを無効にしてコピーし、サブネットからサービス エンドポイントを有効にし、最後に Cosmos DB からサブネットの ACL を実行する必要があります。 サービス エンドポイントを構成して ACL を追加した後、必要な場合は IP ファイアウォールを再度有効にすることができます。

2. 仮想ネットワーク サービス エンドポイントを有効にする前に、将来使用できるように、Azure Cosmos DB アカウントに関連付けられている IP ファイアウォール情報をコピーしておいてください。 サービス エンドポイントを構成した後、IP ファイアウォールを再度有効にすることができます。  

3. 設定メニューの **[Firewalls and Azure virtual networks]\(ファイアウォールと Azure 仮想ネットワーク\)** を選択し、**[選択されたネットワーク]** からのアクセスを許可するように選択します。  

4. 新しい Azure 仮想ネットワークへのアクセスを許可するには、[仮想ネットワーク] で **[新しい仮想ネットワークを追加]** を選択します。  

5. 新しい仮想ネットワークを作成するために必要な詳細を指定し、[作成] を選択します。 "Microsoft.AzureCosmosDB" 用のサービス エンドポイントが有効になっているサブネットが作成されます。

   ![仮想ネットワークと新しい仮想ネットワークのサブネットを選択する](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Azure Portal からのアクセスを許可する

Azure Virtual Network サービス エンドポイントが Azure Cosmos DB データベース アカウントに対して有効になると、既定では、ポータルやその他の Azure サービスからのアクセスは無効になります。 構成されたサブネットの外部のマシンから Azure Cosmos DB データベース アカウントへのアクセスは、ポータルからのアクセスも含めてブロックされます。

![ポータルからのアクセスを許可する](./media/vnet-service-endpoint/allow-access-from-portal.png)

Azure Cosmos DB アカウントが Azure Search のような他の Azure サービスによって使用されたり、Stream Analytics または Power BI からアクセスされる場合は、**[Azure サービスへのアクセスを許可]** をオンにしてアクセスを許可します。

ポータルから Azure Cosmos DB メトリックにアクセスできるようにするには、**[Azure Portal へのアクセスを許可する]** オプションを有効にする必要があります。 これらのオプションの詳細については、「[Azure Portal からの接続](firewall-support.md#connections-from-the-azure-portal)」と「[他の Azure PaaS サービスからの接続](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services)」のセクションを参照してください。 アクセスを選択した後、**[保存]** を選択して、設定を保存します。

## <a name="remove-a-virtual-network-or-subnet"></a>仮想ネットワークまたはサブネットを削除する 

1. **[すべてのリソース]** ブレードで、サービス エンドポイントを割り当てた Azure Cosmos DB アカウントを見つけます。  

2. 設定メニューの **[ファイアウォールと仮想ネットワーク]** を選択します。  

3. 仮想ネットワークまたはサブネットのルールを削除するには、仮想ネットワークまたはサブネットの横にある [...] を選択し、**[削除]** を選択します。

   ![仮想ネットワークを削除する](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  **[保存]** をクリックして変更を保存します。

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Azure PowerShell を使用してサービス エンドポイントを構成する 

Azure PowerShell を使用して、Azure Cosmos DB アカウントへのサービス エンドポイントを構成するには、次の手順を実行します。  

1. 最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) をインストールして[ログイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。  アカウントのサービス エンドポイントを有効にする前に、IP ファイアウォールの設定を書き留め、IP ファイアウォールを完全に削除してください。


> [!NOTE]
> Azure Cosmos DB アカウント用に既存の IP ファイアウォールが構成されている場合は、ファイアウォールの構成を書き留め、IP ファイアウォールを削除してから、サービス エンドポイントを有効にしてください。 ファイアウォールを無効にせずにサービス エンドポイントを有効にすると、その IP 範囲からのトラフィックは仮想 IP ID を失い、IP フィルター エラー メッセージでドロップされます。 そのため、このエラーを防ぐには、ファイアウォール ルールを無効にしてコピーし、サブネットからサービス エンドポイントを有効にし、最後に Cosmos DB からサブネットの ACL を実行する必要があります。 サービス エンドポイントを構成して ACL を追加した後、必要な場合は IP ファイアウォールを再度有効にすることができます。

2. 仮想ネットワーク サービス エンドポイントを有効にする前に、将来使用できるように、Azure Cosmos DB アカウントに関連付けられている IP ファイアウォール情報をコピーしておいてください。 サービス エンドポイントを構成した後、IP ファイアウォールを再度有効にします。  

3. 仮想ネットワークの既存のサブネット用のサービス エンドポイントを有効にします。  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. 仮想ネットワークとサブネットでサービス エンドポイントが Azure Cosmos DB に対して有効になっていることを確認して、CosmosDB アカウントの ACL の有効化を準備します。

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. 次のコマンドレットを実行して、Azure Cosmos DB アカウントのプロパティを取得します。  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. 後で使用するために変数を初期化します。 既存のアカウント定義のすべての変数をセットアップします。複数の場所がある場合は、配列の一部として追加する必要があります。 この手順では、"accountVNETFilterEnabled" 変数を "True" に設定して、仮想ネットワーク サービス エンドポイントも構成します。 この値は、後で "isVirtualNetworkFilterEnabled" パラメーターに割り当てられます。  

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. 次のコマンドレットを実行して、Azure Cosmos DB アカウントのプロパティを新しい構成で更新します。 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. 次のコマンドを実行して、Azure Cosmos DB アカウントが、前の手順で構成した仮想ネットワーク サービス エンドポイントで更新されていることを確認します。

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>IP ファイアウォールが有効になっている Azure Cosmos DB アカウント用の仮想ネットワーク サービス エンドポイントを追加する

1. まず、Azure Cosmos DB アカウントへの IP ファイアウォール アクセスを無効にします。  

2. 前の各セクションで説明した方法のいずれかを使用して、Azure Cosmos DB アカウントへの仮想ネットワーク エンドポイントを有効にします。  

3. IP ファイアウォール アクセスを再度有効にします。 

## <a name="test-the-access"></a>アクセスをテストする

Cosmos DB 用の Azure サービス エンドポイントが期待どおりに構成されているかどうかを確認するには、次の手順を実行します。

* 仮想ネットワーク内に 2 つのサブネットをフロントエンドとバックエンドとしてセットアップし、バックエンド サブネット用の Cosmos DB サービス エンドポイントを有効にします。  

* バックエンド サブネットの Cosmos DB アカウントのアクセスを有効にします。  

* 2 つの仮想マシンを作成します。1 つはバックエンド サブネットに、もう 1 つはフロントエンド サブネットに作成します。  

* 両方の仮想マシンから Azure Cosmos DB アカウントにアクセスしてみます。 バックエンド サブネットで作成した仮想マシンからは接続できますが、フロントエンド サブネットで作成した仮想マシンからは接続できないはずです。 フロントエンド サブネットから接続しようとすると、要求が 404 のエラーになります。このことは、仮想ネットワーク サービス エンドポイントを使用して Azure Cosmos DB へのアクセスが保護されていることを示しています。 バックエンド サブネット内のマシンは正常に動作します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>仮想ネットワーク アクセス制御リスト (ACL) が有効になっている Azure Cosmos DB アカウントにアクセスするとどうなりますか。  

HTTP 404 エラーが返されます。  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>他のリージョンで作成された仮想ネットワークのサブネットは、別のリージョンの Azure Cosmos DB アカウントにアクセスできますか。 たとえば、Azure Cosmos DB アカウントが米国西部または米国東部にあり、仮想ネットワークが複数のリージョンにある場合、仮想ネットワークは Azure Cosmos DB にアクセスできますか。  

はい、別のリージョンで作成された仮想ネットワークは、新しい機能によってアクセスできます。 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Azure Cosmos DB アカウントは、仮想ネットワークのサービス エンドポイントとファイアウォールの両方を持つことができますか。  

はい、Virtual Network Service エンドポイントとファイアウォールは共存できます。 一般に、コンテナーに関連付けられているメトリックを表示できるように仮想ネットワーク サービス エンドポイントを構成する前に、ポータルへのアクセスが常に有効になっている必要があります。

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Azure Cosmos DB に対してサービス エンドポイント アクセスが有効になっている場合、"特定の Azure リージョンから他の Azure サービスへのアクセスを許可" できますか。  

これは、Azure Data Factory、Azure Search、または特定の Azure リージョンにデプロイされるサービスのような他の Azure ファースト パーティ サービスによって Azure Cosmos DB アカウントがアクセスされる場合にのみ必要です。

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Azure Cosmos DB では、いくつの仮想ネットワーク サービス エンドポイントが許可されていますか。  

Azure Cosmos DB アカウントには、64 個の仮想ネットワーク サービス エンドポイントが許可されています。

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>サービス エンドポイントとネットワーク セキュリティ グループ (NSG) ルールの間にはどのような関係がありますか。  

Azure Cosmos DB で NSG ルールを使用すると、特定の Azure Cosmos DB IP アドレス範囲に対するアクセスを制限できます。 特定の[リージョン](https://azure.microsoft.com/global-infrastructure/regions/)に存在する Azure Cosmos DB インスタンスに対するアクセスを許可する場合は、次の形式でリージョンを指定します。 

    AzureCosmosDB.<region name>

NSG タグの詳細については、[仮想ネットワークのサービス タグ](../virtual-network/security-overview.md#service-tags)に関する記事を参照してください。 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>IP ファイアウォールと Virtual Network サービス エンドポイント機能とはどのような関係ですか。  

この 2 つの機能は、Azure Cosmos DB アセットを確実に分離し、それらをセキュリティで保護するために、互いに補完しています。 IP ファイアウォールを使用すると、静的 IP が確実に Azure Cosmos DB アカウントにアクセスできます。  

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Azure Virtual Network ゲートウェイ (VPN) または Express Route ゲートウェイを介して接続しているオンプレミス デバイスの IP アドレスは、Azure Cosmos DB アカウントにアクセスできますか。  

Azure Cosmos DB アカウントにアクセスするには、オンプレミス デバイスの IP アドレスまたは IP アドレス範囲を静的 IP の一覧に追加する必要があります。  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Azure Cosmos DB 用のサービス エンドポイントがセットアップされている仮想ネットワークを削除するとどうなりますか。  

仮想ネットワークが削除されると、その Azure Cosmos DB に関連付けられた ACL 情報が削除され、仮想ネットワークと Azure Cosmos DB アカウント間の対話が削除されます。 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>仮想ネットワーク サービス エンドポイントが有効になっている Azure Cosmos DB アカウントを削除するとどうなりますか。

特定の Azure Cosmos DB アカウントに関連付けられているメタデータがサブネットから削除されます。 また、使用されていたサブネットと仮想ネットワークは、エンド ユーザーが削除する必要があります。

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Azure Cosmos DB 用のサービス エンドポイントを作成するために、ピアリングされた仮想ネットワークを使用することはできますか。  

いいえ、Azure Cosmos DB サービス エンドポイントを作成できるのは、直接の仮想ネットワークとサブネットのみです。

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Azure Cosmos DB サービス エンドポイントが有効になっているサブネット内の仮想マシンなどのリソースの送信元 IP アドレスはどうなりますか。

仮想ネットワーク サービス エンドポイントが有効である場合、仮想ネットワークのサブネット内にあるリソースの送信元 IP アドレスは、Azure Cosmos DB へのトラフィックで、パブリック IPV4 アドレスから Azure Virtual Network のプライベート アドレスに切り替えられます。

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Azure Cosmos DB は、カスタマーが提供する Azure 仮想ネットワーク内に存在しますか。  

Azure Cosmos DB は、パブリック IP アドレスを持つマルチテナント サービスです。 サービス エンドポイント機能を使用して Azure Virtual Network のサブネットへのアクセスを制限すると、特定の Azure 仮想ネットワークとそのサブネットを介して Azure Cosmos DB アカウントに対するアクセスが制限されます。  Azure Cosmos DB アカウントは、その Azure Virtual Network 内にはありません。 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Log Analytics/OMS が有効になっている場合は、何がログに記録されますか。  

Azure Cosmos DB は、ACL によってブロックされた要求に対して、IP アドレス (最後のオクテット以外) をステータス 403 でログにプッシュします。  

## <a name="next-steps"></a>次の手順
Azure Cosmos DB のファイアウォールを構成するには、[ファイアウォールのサポート](firewall-support.md)に関する記事を参照してください。

