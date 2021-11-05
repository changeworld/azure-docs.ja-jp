---
title: Azure Private Link を使用した Azure Cache for Redis
description: Azure プライベート エンドポイントは、Azure Private Link を使用した Azure Cache for Redis にプライベートかつ安全に接続するネットワーク インターフェイスです。 この記事では、Azure portal を使って Azure Cache、Azure 仮想ネットワーク、プライベート エンドポイントを作成する方法について説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 3/31/2021
ms.openlocfilehash: 4a98b229497aa3b11692fff044bb0f0347ada9d7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039527"
---
# <a name="azure-cache-for-redis-with-azure-private-link"></a>Azure Private Link を使用した Azure Cache for Redis

この記事では、Azure portal を使用して、仮想ネットワークと、プライベート エンドポイントを利用する Azure Cache for Redis インスタンスを作成する方法について学習します。 また、既存の Azure Cache for Redis インスタンスにプライベート エンドポイントを追加する方法について学習します。

Azure プライベート エンドポイントは、Azure Private Link を使用した Azure Cache for Redis にプライベートかつ安全に接続するネットワーク インターフェイスです。

`PublicNetworkAccess` フラグを無効にすることで、キャッシュのプライベート エンドポイントへのパブリック アクセスを制限できます。

>[!Important]
> `publicNetworkAccess` フラグは既定で `Disabled` に設定されています。
> 値を `Disabled` または `Enabled` に設定できます。 有効に設定すると、このフラグにより、パブリック エンドポイントとプライベート エンドポイントの両方がキャッシュにアクセスできます。 `Disabled` に設定すると、プライベート エンドポイントのアクセスのみが許可されます。 値を変更する方法の詳細については、「[よくあるご質問](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)」を参照してください。
>
>

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> 現時点では、ポータル コンソールのサポートとファイアウォール ストレージ アカウントへの永続化はサポートされていません。
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>新しい Azure Cache for Redis インスタンスを使用してプライベート エンドポイントを作成する

このセクションでは、プライベート エンドポイントを利用する新しい Azure Cache for Redis インスタンスを作成します。

### <a name="create-a-virtual-network-for-your-new-cache"></a>新しいキャッシュ用の仮想ネットワークを作成する

1. [Azure Portal](https://portal.azure.com) にサインインし、**[リソースの作成]** を選択します。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="[リソースの作成] を選択します。":::

2. **[新規]** ページで、 **[ネットワーク]** を選択してから、 **[仮想ネットワーク]** を選択します。

3. **[追加]** を選択して仮想ネットワークを作成します。

4. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この仮想ネットワークを作成するサブスクリプション。 |
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中に仮想ネットワークやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 |
   | **名前** | 仮想ネットワーク名を入力します。 | 名前の先頭は文字または数字、末尾は文字、数字、アンダースコアでなければならず、含めることができるのは文字、数字、アンダースコア、ピリオド、ハイフンのみです。 |
   | **リージョン** | ドロップ ダウンでリージョンを選択します。 | 仮想ネットワークを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |

5. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

6. **[IP アドレス]** タブで、 **[IPv4 アドレス空間]** を、CIDR 表記で 1 つまたは複数のアドレス プレフィックスとして指定します (例: 192.168.1.0/24)。

7. **[サブネット名]** で、 **[既定値]** を指定してサブネットのプロパティを編集します。

8. **[サブネットの編集]** ペインで、 **[サブネット名]** と **[サブネットのアドレス範囲]** を指定します。 サブネットのアドレスの範囲は CIDR 表記である必要があります (例: 192.168.1.0/24)。 仮想ネットワークのアドレス空間に含まれている必要があります。

9. **[保存]** を選択します。

10. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

11. すべての情報が正しいことを確認し、 **[作成する]** を選択して仮想ネットワークをプロビジョニングします。

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>プライベート エンドポイントを利用する Azure Cache for Redis インスタンスを作成する

キャッシュ インスタンスを作成するには、これらの手順に従います。

1. Azure portal ホームページに戻るか、サイドバー メニューを開き、 **[リソースの作成]** を選択します。

1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="[Azure Cache for Redis] を選択します。":::

1. **[新規 Redis Cache]** ページで、新しいキャッシュの設定を構成します。

   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュの名前は 1 文字から 63 文字の文字列である必要があります。 文字列には、数字、文字、またはハイフンのみを含めることができます。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 |
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 |
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 |
   | **場所** | ドロップ ダウンで場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **価格レベル** | ドロップ ダウンで[価格レベル](https://azure.microsoft.com/pricing/details/cache/)を選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |

1. **[ネットワーク]** タブを選択するか、ページの下部にある **[ネットワーク]** ボタンを選択します。

1. **[ネットワーク]** タブで、接続方法として **[プライベート エンドポイント]** を選択します。

1. **[Add (追加)]** ボタンを選択して、プライベート エンドポイントを作成します。

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="[ネットワーク] で、プライベート エンドポイントを追加する。":::

1. **[プライベート エンドポイントの作成]** ページで、前のセクションで作成した仮想ネットワークとサブネットを使用してプライベート エンドポイントの設定を構成し、 **[OK]** を選択します。

1. **[次へ: 詳細]** タブを選択するか、ページの下部にある **[次へ: 詳細]** ボタンを選択します。

1. Basic または Standard のキャッシュ インスタンスの **[詳細]** タブで、非 TLS ポートを有効にする場合は有効トグルをオンにします。

1. Premium キャッシュ インスタンスの **[詳細]** タブで、非 TLS ポート、クラスタリング、データ永続化の設定を構成します。

1. ページの下部にある **[次へ: タグ]** タブを選択するか、ページの下部にある **[次へ: タグ]** ボタンを選択します。

1. 必要に応じて、 **[タグ]** タブで、リソースを分類する場合は名前と値を入力します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 [確認および作成] タブが表示され、Azure によって構成が検証されます。

1. 緑色の検証に成功のメッセージが表示された後、 **[作成]** を選択します。

キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。

> [!IMPORTANT]
> `publicNetworkAccess` フラグは既定で `Disabled` に設定されています。
> 値を `Disabled` または `Enabled` に設定できます。 `Enabled` に設定すると、このフラグにより、パブリック エンドポイントとプライベート エンドポイントの両方がキャッシュにアクセスできます。 `Disabled` に設定すると、プライベート エンドポイントのアクセスのみが許可されます。 値を変更する方法の詳細については、「[よくあるご質問](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)」を参照してください。
>
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>既存の Azure Cache for Redis インスタンスを使用してプライベート エンドポイントを作成する

このセクションでは、既存の Azure Cache for Redis インスタンスにプライベート エンドポイントを追加します。

### <a name="create-a-virtual-network-for-you-existing-cache"></a>既存のキャッシュ用の仮想ネットワークを作成する

仮想ネットワークを作成するには、これらの手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインし、**[リソースの作成]** を選択します。

1. **[新規]** ページで、 **[ネットワーク]** を選択してから、 **[仮想ネットワーク]** を選択します。

1. **[追加]** を選択して仮想ネットワークを作成します。

1. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この仮想ネットワークを作成するサブスクリプション。 |
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中に仮想ネットワークやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 |
   | **名前** | 仮想ネットワーク名を入力します。 | 名前の先頭は文字または数字、末尾は文字、数字、アンダースコアでなければならず、含めることができるのは文字、数字、アンダースコア、ピリオド、ハイフンのみです。 |
   | **リージョン** | ドロップ ダウンでリージョンを選択します。 | 仮想ネットワークを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |

1. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

1. **[IP アドレス]** タブで、 **[IPv4 アドレス空間]** を、CIDR 表記で 1 つまたは複数のアドレス プレフィックスとして指定します (例: 192.168.1.0/24)。

1. **[サブネット名]** で、 **[既定値]** を指定してサブネットのプロパティを編集します。

1. **[サブネットの編集]** ペインで、 **[サブネット名]** と **[サブネットのアドレス範囲]** を指定します。 サブネットのアドレスの範囲は CIDR 表記である必要があります (例: 192.168.1.0/24)。 仮想ネットワークのアドレス空間に含まれている必要があります。

1. **[保存]** を選択します。

1. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

1. すべての情報が正しいことを確認し、 **[作成する]** を選択して仮想ネットワークをプロビジョニングします。

### <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

プライベート エンドポイントを作成するには、これらの手順に従います。

1. Azure portal で、**Azure Cache for Redis** を検索します。 その後、Enter キーを押すか、検索候補から選択してください。

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Azure Cache for Redis を検索する。":::

1. プライベート エンドポイントを追加するキャッシュ インスタンスを選択します。

1. 画面の左側で、 **[プライベート エンドポイント]** を選択します。

1. **[プライベート エンドポイント]** ボタンをクリックして、プライベート エンドポイントを作成します。

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="プライベート エンドポイントを追加する。":::

1. **[プライベート エンドポイントの作成] ページ** で、プライベート エンドポイントの設定を構成します。

   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | このプライベート エンドポイントを作成するサブスクリプション。 |
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にプライベート エンドポイントやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 |
   | **名前** | プライベート エンドポイント名を入力します。 | 名前は先頭が文字または数字、末尾が文字、数字、アンダースコアでなければならず、中に含めることができるのは文字、数字、アンダースコア、ピリオド、ハイフンのみです。 |
   | **リージョン** | ドロップ ダウンでリージョンを選択します。 | プライベート エンドポイントを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |

1. ページの下部にある **[次へ: リソース]** ボタンを選択します。

1. **[リソース]** タブで、該当するサブスクリプションを選択し、リソースの種類として [`Microsoft.Cache/Redis`] を選んでから、プライベート エンドポイントを接続するキャッシュを選択します。

1. ページの下部にある **[次へ: 構成]** ボタンを選択します。

1. **[構成]** タブで、前のセクションで作成した仮想ネットワークとサブネットを選択します。

1. ページの下部にある **[次へ: タグ]** ボタンを選択します。

1. 必要に応じて、 **[タグ]** タブで、リソースを分類する場合は名前と値を入力します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** タブが表示され、Azure によって構成が検証されます。

1. 緑色の **検証に成功** のメッセージが表示された後、 **[作成]** を選択します。

> [!IMPORTANT]
>
> `publicNetworkAccess` フラグは既定で `Disabled` に設定されています。
> 値を `Disabled` または `Enabled` に設定できます。 有効に設定すると、このフラグにより、パブリック エンドポイントとプライベート エンドポイントの両方がキャッシュにアクセスできます。 `Disabled` に設定すると、プライベート エンドポイントのアクセスのみが許可されます。 値を変更する方法の詳細については、「[よくあるご質問](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)」を参照してください。
>
## <a name="create-a-private-endpoint-using-azure-powershell"></a>Azure PowerShell を使用してプライベート エンドポイントを作成する

既存のインスタンスに対して *MyPrivateEndpoint* という名前のプライベート エンドポイントAzure Cache for Redis、次の PowerShell スクリプトを実行します。 変数の値を環境の詳細に置き換えます：

```azurepowershell-interactive

$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cache for Redis instance and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cache for Redis instance
$redisCacheName = "mycacheInstance"

# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$redisCacheResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Cache/Redis/$($redisCacheName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $redisCacheResourceId -GroupId "redisCache"
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

## <a name="retrieve-a-private-endpoint-using-azure-powershell"></a>Azure PowerShellを使用してプライベート エンドポイントを取得する

プライベートエンドポイントの詳細を取得するには、次の PowerShell コマンドを使用します：

```azurepowershell-interactive
Get-AzPrivateEndpoint -Name $PrivateEndpointName -ResourceGroupName $ResourceGroupName
```

## <a name="remove-a-private-endpoint-using-azure-powershell"></a>Azure PowerShell を使用してプライベートエンドポイントを削除する

プライベートエンドポイントを削除するには、次の PowerShell コマンドを使用します。

```azurepowershell-interactive
Remove-AzPrivateEndpoint -Name $PrivateEndpointName -ResourceGroupName $ResourceGroupName
```

## <a name="create-a-private-endpoint-using-azure-cli"></a>Azure CLI を使用してプライベート エンドポイントを作成する

Redis インスタンス用の既存の Azure キャッシュ用に *myPrivateEndpoint* という名前のプライベートエンドポイントを作成するには、次の Azure CLI スクリプトを実行します。 変数の値を環境の詳細に置き換えます：

```azurecli-interactive
# Resource group where the Azure Cache for Redis and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cache for Redis and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cache for Redis instance
redisCacheName="mycacheInstance"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
    --name $VNetName \
    --resource-group $ResourceGroupName \
    --subnet-name $SubnetName

az network vnet subnet update \
    --name $SubnetName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName \
    --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Cache/Redis/$redisCacheName" \
    --group-ids "redisCache" \
    --connection-name $PrivateConnectionName
```

## <a name="retrieve-a-private-endpoint-using-azure-cli"></a>Azure CLI を使用してプライベートエンドポイントを取得する

プライベートエンドポイントの詳細を取得するには、次の CLI コマンドを使用します。

```azurecli-interactive
az network private-endpoint show --name MyPrivateEndpoint --resource-group MyResourceGroup
```

## <a name="remove-a-private-endpoint-using-azure-cli"></a>Azure CLI を使用してプライベートエンドポイントを削除する

プライベートエンドポイントを削除するには、次の CLI コマンドを使用します。

```azurecli-interactive
az network private-endpoint delete --name MyPrivateEndpoint --resource-group MyResourceGroup
```

## <a name="faq"></a>よく寄せられる質問

- [プライベート エンドポイントに接続できないのはなぜですか。](#why-cant-i-connect-to-a-private-endpoint)
- [プライベート エンドポイントでサポートされていない機能は何ですか?](#what-features-arent-supported-with-private-endpoints)
- [どうすれば、プライベート エンドポイントが正しく構成されていることを確認できますか。](#how-do-i-verify-if-my-private-endpoint-is-configured-correctly)
- [プライベート エンドポイントをパブリック ネットワーク アクセスに対して無効または有効になるように変更するにはどうすればよいですか。](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
- [VNet インジェクションされたキャッシュを Private Link キャッシュに移行するにはどうすればよいですか。](#how-can-i-migrate-my-vnet-injected-cache-to-a-private-link-cache)
- [異なる仮想ネットワーク内に複数のエンドポイントを配置するにはどうすればよいですか。](#how-can-i-have-multiple-endpoints-in-different-virtual-networks)
- [キャッシュのすべてのプライベート エンドポイントを削除するとどうなりますか。](#what-happens-if-i-delete-all-the-private-endpoints-on-my-cache)
- [ネットワーク セキュリティ グループ (NSG) はプライベート エンドポイントでは有効になっていますか。](#are-network-security-groups-nsg-enabled-for-private-endpoints)
- [プライベート エンドポイント インスタンスは自分の VNet に含まれていませんが、VNet とどのように関連付けられていますか?](#my-private-endpoint-instance-isnt-in-my-vnet-so-how-is-it-associated-with-my-vnet)

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>プライベート エンドポイントに接続できないのはなぜですか。

- キャッシュが既 VNet に挿入されたキャッシュである場合は、プライベートエンドポイントをキャッシュインスタンスで使用することはできません。
- クラスター化されたキャッシュのプライベートリンクは1つに制限されています。 その他のすべてのキャッシュでは、制限は100プライベートリンクです。
- ファイアウォール規則が適用されている [ストレージアカウント](cache-how-to-premium-persistence.md) にデータを永続化しようとすると、プライベートリンクを作成できなくなる可能性があります。
- キャッシュインスタンスが [サポートされていない機能](#what-features-arent-supported-with-private-endpoints)を使用している場合、プライベートエンドポイントに接続することはできません。

### <a name="what-features-arent-supported-with-private-endpoints"></a>プライベート エンドポイントでサポートされていない機能は何ですか?

Azure portal コンソールから接続しようとすると、サポートされていないシナリオで接続エラーが表示されます。

### <a name="how-do-i-verify-if-my-private-endpoint-is-configured-correctly"></a>どうすれば、プライベート エンドポイントが正しく構成されていることを確認できますか。

プライベート エンドポイントにリンクされている `nslookup` のようなコマンドを VNet 内から実行して、コマンドによって、キャッシュのプライベート IP アドレスに解決されることを確認できます。 プライベート IP アドレスは、リソースから **プライベート エンドポイント** を選択すると、見つかります。 左側のリソース メニューで、 **[DNS 構成]** を選択します。 右側の作業ウィンドウに、**ネットワーク インターフェイス** の IP アドレスが表示されます。

:::image type="content" source="media/cache-private-link/cache-private-ip-address.png" alt-text="Azure portal でのプライベート エンドポイント DNS の設定。":::

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>プライベート エンドポイントをパブリック ネットワーク アクセスに対して無効または有効になるように変更するにはどうすればよいですか。

既定では、`publicNetworkAccess` フラグは `Disabled` です。
`Enabled` に設定すると、このフラグにより、パブリック エンドポイントとプライベート エンドポイントの両方がキャッシュにアクセスできます。 `Disabled` に設定すると、プライベート エンドポイントのアクセスのみが許可されます。 Azure portal または RESTful API パッチ要求で、この値を `Disabled` または `Enabled` に設定できます。

Azure portal で値を変更するには、こちらの手順を実行します。

1. Azure portal で、**Azure Cache for Redis** を検索します。  その後、Enter キーを押すか、検索候補から選択してください。

1. パブリック ネットワーク アクセスの値を変更するキャッシュ インスタンスを選択します。

1. 画面の左側で、 **[プライベート エンドポイント]** を選択します。

1. **[Enable public network access (パブリック ネットワーク アクセスを有効にする)]** ボタンを選択します。

RESTful API パッチ要求を使用して値を変更するには、下を参照して、キャッシュに必要なフラグを反映するように値を編集します。

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-link-cache"></a>VNet インジェクションされたキャッシュを Private Link キャッシュに移行するにはどうすればよいですか。

VNet インジェクションされたキャッシュを Private Link キャッシュに移行するさまざまな方法については、[移行ガイド](cache-vnet-migration.md)を参照してください。

### <a name="how-can-i-have-multiple-endpoints-in-different-virtual-networks"></a>異なる仮想ネットワーク内に複数のエンドポイントを配置するにはどうすればよいですか。

異なる仮想ネットワーク内に複数のプライベート エンドポイントを配置するには、プライベート エンドポイントを作成する _前に_、プライベート DNS ゾーンを複数の仮想ネットワークに手動で構成する必要があります。 詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」をご覧ください。

### <a name="what-happens-if-i-delete-all-the-private-endpoints-on-my-cache"></a>キャッシュのすべてのプライベート エンドポイントを削除するとどうなりますか。

キャッシュのプライベート エンドポイントを削除すると、明示的にパブリック ネットワーク アクセスを有効にするか、別のプライベート エンドポイントを追加するまで、キャッシュ インスタンスに到達できなくなる可能性があります。 `publicNetworkAccess` フラグは、Azure portal または RESTful API パッチ要求のいずれかで変更できます。 値を変更する方法の詳細については、「[よくあるご質問](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)」を参照してください

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>ネットワーク セキュリティ グループ (NSG) はプライベート エンドポイントでは有効になっていますか。

いいえ、プライベート エンドポイントでは無効になっています。 プライベート エンドポイントを含むサブネットに NSG を関連付けることはできますが、プライベート エンドポイントによって処理されるトラフィックに対して規則は有効ではありません。 サブネットにプライベート エンドポイントをデプロイするには、[ネットワーク ポリシーの適用を無効にする](../private-link/disable-private-endpoint-network-policy.md)必要があります。 NSG は、同じサブネット上にホストされている他のワークロードにも適用されます。 クライアント サブネット上のルートは /32 プレフィックスを使用するため、既定のルーティング動作を変更するには同様の UDR が必要です。

ソース クライアントにおけるアウトバウンド トラフィックに対して NSG 規則を使用して、トラフィックを制御します。 /32 プレフィックスを持つ個々のルートをデプロイして、プライベート エンドポイント ルートをオーバーライドします。 送信接続の NSG フロー ログと監視情報は引き続きサポートされており、使用することができます。

### <a name="my-private-endpoint-instance-isnt-in-my-vnet-so-how-is-it-associated-with-my-vnet"></a>プライベート エンドポイント インスタンスは自分の VNet に含まれていませんが、VNet とどのように関連付けられていますか?

お使いの VNet にリンクされているだけです。 これは VNet 内に存在しないため、依存エンドポイントに対して NSG 規則を変更する必要はありません。

## <a name="next-steps"></a>次の手順

- Azure Private Link の詳細については、[Azure Private Link のドキュメント](../private-link/private-link-overview.md)を参照してください。
- キャッシュ インスタンスのさまざまなネットワークの分離のオプションを比較するには、「[Azure Cache for Redis のネットワークの分離オプション](cache-network-isolation.md)」のドキュメントを参照してください。
