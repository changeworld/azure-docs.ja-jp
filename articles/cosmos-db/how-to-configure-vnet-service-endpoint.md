---
title: Azure Cosmos アカウント用の仮想ネットワークとサブネット ベースのアクセスを構成する方法
description: このドキュメントでは、Azure Cosmos DB 仮想ネットワーク サービス エンドポイントのセットアップに必要な手順について説明します。
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: a7c2d1e41fa4ac26854e2e6ab57184cd6ed0bd0c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633684"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>仮想ネットワークから Azure Cosmos DB リソースにアクセスする方法

Azure CosmosDB アカウントは、Azure Virtual Network にある特定のサブネットからのアクセスのみを許可するように構成することができます。 仮想ネットワーク (VNET) 内のサブネットからの接続で Azure Cosmos アカウントへのアクセスを制限するために必要な手順は 2 つあります。
 
1. サブネットが、Azure Cosmos DB にサブネットと VNET の ID を送信できるようにします。 特定のサブネット上で Azure Cosmos DB 用のサービス エンドポイントを有効にすることで、これを実現できます。

1. Azure Cosmos アカウントにアクセスできるソースとしてサブネットを指定する規則を Azure Cosmos アカウントに追加します。

> [!NOTE]
> Azure Cosmos アカウント用のサービス エンドポイントがサブネット上で有効になると、Azure Cosmos DB に到達するトラフィックのソースが、パブリック IP から、VNET とサブネットに切り替わります。 このトラフィックの切り替えは、このサブネットからアクセスされるすべての Azure Cosmos アカウントに適用されます。 このサブネットを許可する IP ベースのファイアウォールが Azure Cosmos アカウントに設定されている場合、サービスで有効なサブネットからの要求は IP ファイアウォール規則と一致しなくなり、要求は拒否されるようになります。 詳細については、この記事の「[IP ファイアウォール規則から VNET アクセス コントロール リストへの移行](#migrate-from-firewall-to-vnet)」セクションの手順を参照してください。 

以下のセクションで、Azure Cosmos DB アカウント用の VNET サービス エンドポイントを構成する方法について説明します。

## <a id="configure-using-portal"></a>Azure portal を使用してサービス エンドポイントを構成する

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>既存の Azure 仮想ネットワークとサブネット用のサービス エンドポイントを構成する

1. **[すべてのリソース]** ブレードで、セキュリティで保護する Azure Cosmos アカウントを見つけます。

1. 設定メニューの **[ファイアウォールと仮想ネットワーク]** を選択し、**[選択されたネットワーク]** からのアクセスを許可するように選択します。

1. 既存の仮想ネットワークにあるサブネットへのアクセスを許可するには、**[仮想ネットワーク]** で **[Add existing Azure virtual network]\(既存の Azure 仮想ネットワークを追加\)** を選択します。

1. 追加する Azure 仮想ネットワークの**サブスクリプション**を選択します。 Azure Cosmos アカウントへのアクセスを提供する Azure **仮想ネットワーク**と**サブネット**を選択します。 次に、**[有効化]** を選択して、サービス エンドポイントを持つ選択したネットワークを "Microsoft.AzureCosmosDB" に対して有効にします。 完了したら、**[追加]** を選択します。 

   ![仮想ネットワークとサブネットを選択する](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. 仮想ネットワークからの Azure Cosmos アカウントへのアクセスを有効にすると、選択したサブネットからのトラフィックのみが許可されます。 追加した仮想ネットワークとサブネットは、次のスクリーン ショットのように表示されます。

   ![正常に構成された仮想ネットワークとサブネット](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 仮想ネットワークのサービス エンドポイントを有効にするには、次のサブスクリプションのアクセス許可が必要です。
  * VNET のサブスクリプション: ネットワーク共同作成者
  * Azure Cosmos アカウントのサブスクリプション: DocumentDB アカウント共同作成者

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>新しい Azure 仮想ネットワークとサブネット用のサービス エンドポイントを構成する

1. **[すべてのリソース]** ブレードで、セキュリティで保護する Azure Cosmos DB アカウントを見つけます。  

2. 仮想ネットワーク サービス エンドポイントを有効にする前に、将来使用できるように、Azure Cosmos DB アカウントに関連付けられている IP ファイアウォール情報をコピーしておいてください。 サービス エンドポイントを構成した後、IP ファイアウォールを再度有効にすることができます。  

3. 設定メニューの **[Firewalls and Azure virtual networks]\(ファイアウォールと Azure 仮想ネットワーク\)** を選択し、**[選択されたネットワーク]** からのアクセスを許可するように選択します。  

4. 新しい Azure 仮想ネットワークへのアクセスを許可するには、[仮想ネットワーク] で **[新しい仮想ネットワークを追加]** を選択します。  

5. 新しい仮想ネットワークを作成するために必要な詳細を指定し、[作成] を選択します。 "Microsoft.AzureCosmosDB" 用のサービス エンドポイントが有効になっているサブネットが作成されます。

   ![仮想ネットワークと新しい仮想ネットワークのサブネットを選択する](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Azure Cosmos アカウントが Azure Search のような他の Azure サービスによって使用されたり、Stream Analytics または Power BI からアクセスされたりする場合は、[Azure サービスへのアクセスを許可] をオンにしてアクセスを許可します。

ポータルから Azure Cosmos DB メトリックにアクセスできるようにするには、**[Azure Portal へのアクセスを許可する]** オプションを有効にする必要があります。 これらのオプションの詳細については、[IP ファイアウォールの構成](how-to-configure-firewall.md)に関する記事の Azure portal からの要求と Azure PaaS サービスからの要求に関するセクションを参照してください。 アクセスを選択した後、**[保存]** を選択して、設定を保存します。

## <a id="remove-vnet-or-subnet"></a>仮想ネットワークまたはサブネットを削除する 

1. **[すべてのリソース]** ブレードで、サービス エンドポイントを割り当てた Azure Cosmos DB アカウントを見つけます。  

2. 設定メニューの **[ファイアウォールと仮想ネットワーク]** を選択します。  

3. 仮想ネットワークまたはサブネットのルールを削除するには、仮想ネットワークまたはサブネットの横にある [...] を選択し、**[削除]** を選択します。

   ![仮想ネットワークを削除する](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  **[保存]** をクリックして変更を保存します。

## <a id="configure-using-powershell"></a>Azure PowerShell を使用してサービス エンドポイントを構成する 

Azure PowerShell を使用して、Azure Cosmos DB アカウントへのサービス エンドポイントを構成するには、次の手順を実行します。  

1. 最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) をインストールして[ログイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。  

1. 仮想ネットワークにある、既存のサブネット用のサービス エンドポイントを有効にします。  

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

1. 仮想ネットワークとサブネットで Azure Cosmos アカウント用のサービス エンドポイントが有効になっていることを確認して、そのアカウントの ACL の有効化を準備します。

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. 次のコマンドレットを実行して、Azure Cosmos DB アカウントのプロパティを取得します。  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 後で使用するために変数を初期化します。 既存のアカウント定義のすべての変数をセットアップします。複数の場所がある場合は、配列の一部として追加する必要があります。 この手順では、"accountVNETFilterEnabled" 変数を "True" に設定して、仮想ネットワーク サービス エンドポイントも構成します。 この値は、後で "isVirtualNetworkFilterEnabled" パラメーターに割り当てられます。 

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

1. 次のコマンドレットを実行して、Azure Cosmos DB アカウントのプロパティを新しい構成で更新します。 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

1. 次のコマンドを実行して、Azure Cosmos DB アカウントが、前の手順で構成した仮想ネットワーク サービス エンドポイントで更新されていることを確認します。

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Azure CLI を使用してサービス エンドポイントを構成する 

1. 仮想ネットワーク内のサブネットに対してサービス エンドポイントを有効にします。

1. 既存の Azure Cosmos アカウントを、サブネットの ACL で更新します。

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. サブネットの ACL を持つ新しい Azure Cosmos アカウントを作成します。

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>IP ファイアウォール規則から VNET ACL への移行 

以下の手順は、サブネットを許可する既存の IP ファイアウォール規則が設定されている Azure Cosmos アカウントがあるときに、IP ファイアウォール規則ではなく VNET とサブネット ベースの ACL を使用する場合にのみ実行する必要があります。

サブネットに対して Azure Cosmos アカウント用のサービス エンドポイントが有効になると、要求は、パブリック IP ではなく VNET とサブネットの情報を含むソース付きで送信されます。 このため、このような要求では、IP フィルターが一致しなくなります。 このソースの切り替えは、サービス エンドポイントが有効になっているサブネットからアクセスされるすべての Azure Cosmos アカウントで発生します。 ダウンタイムを回避するために、次の手順に従ってください。

1. 次のコマンドレットを実行して、Azure Cosmos アカウントのプロパティを取得します。

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 後で使用するために変数を初期化します。 既存のアカウントの定義からすべての変数を設定します。 サブネットからアクセスされるすべての Azure Cosmos アカウントに、`ignoreMissingVNetServiceEndpoint` フラグ付きで VNET ACL を追加します。  

   複数の場所がある場合は、配列の一部としてそれらを追加する必要があります。 この手順では、"accountVNETFilterEnabled" 変数を "True" に設定して、仮想ネットワーク サービス エンドポイントも構成します。 この値は、後で "isVirtualNetworkFilterEnabled" パラメーターに割り当てられます。

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
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. 次のコマンドレットを実行して、Azure Cosmos アカウントのプロパティを新しい構成で更新します。

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. サブネットからアクセスするすべての Azure Cosmos アカウントに対して、手順 1 ～ 3 を繰り返します。

1.  15 分間待機した後、サブネットを更新してサービス エンドポイントを有効にします。

1.  仮想ネットワークにある、既存のサブネット用のサービス エンドポイントを有効にします。

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. サブネット用の IP ファイアウォール規則を削除します。

## <a name="next-steps"></a>次の手順

* Azure Cosmos DB 用のファイアウォールを構成するには、[ファイアウォールのサポート](firewall-support.md)に関する記事を参照してください。

