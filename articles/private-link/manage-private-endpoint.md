---
title: Azure でプライベート エンドポイント接続を管理する
titleSuffix: Azure Private Link
description: Azure でプライベート エンドポイント接続を管理する方法について学習します
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 10/04/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7923e35165f756878ed52e807a1e0106e1660e59
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535967"
---
# <a name="manage-a-private-endpoint-connection"></a>プライベート エンドポイント接続を管理する

Azure Private Link は、Private Link サービスのコンシューマーがサービスを使用するためにサービス プロバイダーへの接続を要求できる承認モデル上で動作します。 

次に、サービス プロバイダーは、コンシューマーに接続を許可するかどうかを決定できます。 サービス プロバイダーは、Azure Private Link を使用して、自分のリソース上のプライベート エンドポイント接続を管理できます。 

この記事では、プライベート エンドポイント接続を管理する方法について説明します。

![プライベート エンドポイントを管理する](media/manage-private-endpoint/manage-private-endpoint.png)

Private Link サービスのコンシューマーは、次の 2 つの接続承認方法から選択できます。

- **自動**: サービスのコンシューマーにサービス プロバイダーのリソースに対する Azure ロールベースのアクセス制御アクセス許可がある場合、コンシューマーは自動の承認方法を選択できます。 要求がサービス プロバイダーのリソースに到達したときにサービス プロバイダーからのアクションは必要なく、接続が自動的に承認されます。 

- **手動**: サービスのコンシューマーにサービス プロバイダーのリソースに対する Azure ロールベースのアクセス制御アクセス許可がない場合、コンシューマーは手動の承認方法を選択できます。 接続要求はサービスのリソース上で "**保留中**" と表示されます。 サービス プロバイダーは、接続を確立する前に、手動で要求を承認する必要があります。 手動の場合、サービスのコンシューマーは、要求と共にメッセージを指定して、サービス プロバイダーにより多くのコンテキストを提供することもできます。 サービス プロバイダーは、すべてのプライベート エンドポイント接続に対して次のいずれかのオプションを選択できます: **Approved**、**Reject**、**Remove**。

次の表に、サービス プロバイダーのさまざまなアクションと、その結果であるプライベート エンドポイントの接続状態を示します。 サービス プロバイダーは、コンシューマーの操作なしで後から接続状態を変更することができます。 このアクションにより、コンシューマー側のエンドポイントの状態が更新されます。 


| サービス プロバイダーのアクション  | サービスのコンシューマーのプライベート エンドポイントの状態 | 説明 |
|---------|---------|---------|
| なし    |    保留中     |    接続が手動で作成されており、Private Link リソースの所有者による承認を待っています。       |
| 承認    |  Approved       |  接続が自動または手動で承認され、使用する準備が整っています。     |
| Reject     | 拒否        | プライベート リンク リソースの所有者によって接続が拒否されました。        |
| [削除]    |  [Disconnected]\(切断済み\)       | プライベート リンク リソースの所有者によって接続が削除されました。プライベート エンドポイントは情報が多くなり、クリーンアップのために削除する必要があります。        |

## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Azure PaaS リソース上のプライベート エンドポイント接続を管理する

Azure portal は、Azure PaaS リソース上のプライベート エンドポイント接続を管理するために推奨される方法です。 

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**プライベート リンク**」と入力します。 検索結果の **[プライベート リンク]** を選択します。

3. **[プライベート リンク センター]** の **[プライベート エンドポイント]** または **[Private Link サービス]** を選択します。

4. エンドポイントごとに、それに関連付けられているプライベート エンドポイント接続の数を確認できます。 必要に応じて、リソースをフィルター処理できます。

5. プライベート エンドポイントを選択します。  一覧表示された接続の下で、管理する接続を選択します。 

6. 上部のオプションから選択することにより、接続の状態を変更できます。

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>顧客またはパートナーが所有する Private Link サービス上のプライベート エンドポイント接続を管理する

Azure PowerShell および Azure CLI は、Microsoft Partner Services または顧客が所有するサービス上のプライベート エンドポイント接続を管理するための推奨手段です。 
 
### <a name="powershell"></a>PowerShell 
  
次の PowerShell コマンドを使用して、プライベート エンドポイント接続を管理できます。  

#### <a name="get-private-link-connection-states"></a>Private Link の接続状態を取得する 

プライベート エンドポイントの接続とその状態を取得するには、[Get-AzPrivateEndpointConnection](/powershell/module/az.network/get-azprivateendpointconnection) を使用します。  

```azurepowershell
Get-AzPrivateEndpointConnection -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
```
 
#### <a name="approve-a-private-endpoint-connection"></a>プライベート エンドポイント接続を承認する 
 
プライベート エンドポイント接続を承認するには、[Approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) コマンドレットを使用します。 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>プライベート エンドポイント接続を拒否する 
 
プライベート エンドポイント接続を拒否するには、[Deny-AzPrivateEndpointConnection](/powershell/module/az.network/deny-azprivateendpointconnection) コマンドレットを使用します。 

```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```

#### <a name="remove-private-endpoint-connection"></a>プライベート エンドポイント接続を削除する 
 
プライベート エンドポイント接続を削除するには、[Remove-AzPrivateEndpointConnection](/powershell/module/az.network/remove-azprivateendpointconnection) コマンドレットを使用します。 

```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
### <a name="azure-cli"></a>Azure CLI 
 
#### <a name="get-private-link-connection-states"></a>Private Link の接続状態を取得する 

プライベート エンドポイント接続とその状態を取得するには、[az network private-endpoint-connection show](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_show) を使用します。  

```azurecli
  az network private-endpoint-connection show \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```
 
#### <a name="approve-a-private-endpoint-connection"></a>プライベート エンドポイント接続を承認する 
 
プライベート エンドポイント接続を承認するには、[az network private-endpoint-connection approve](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_approve) コマンドレットを使用します。 
 
```azurecli
  az network private-endpoint-connection approve \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```
 
#### <a name="deny-private-endpoint-connection"></a>プライベート エンドポイント接続を拒否する 
 
プライベート エンドポイント接続を拒否するには、[az network private-endpoint-connection reject](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_reject) コマンドレットを使用します。 

```azurecli
  az network private-endpoint-connection reject \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```

#### <a name="remove-private-endpoint-connection"></a>プライベート エンドポイント接続を削除する 
 
プライベート エンドポイント接続を削除するには、[az network private-endpoint-connection delete](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_delete) コマンドレットを使用します。 

```azurecli
  az network private-endpoint-connection delete \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>次のステップ
- [プライベート エンドポイントについて学習します](private-endpoint-overview.md)
 
