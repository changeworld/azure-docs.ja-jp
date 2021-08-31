---
title: Azure Private Link 用の Azure RBAC アクセス許可
description: プライベート エンドポイントとプライベート リンク サービスをデプロイするために必要な Azure RBAC のアクセス許可についての学習を開始します。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 5/25/2021
ms.custom: template-concept
ms.openlocfilehash: 50b648c5419b995f9c808288895af49b12f29b86
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178318"
---
# <a name="azure-rbac-permissions-for-azure-private-link"></a>Azure Private Link 用の Azure RBAC アクセス許可

クラウド リソースに対するアクセスの管理は、すべての組織にとって重要な機能です。 Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスと操作を管理します。

プライベート エンドポイントまたはプライベート リンク サービスをデプロイするには、ユーザーに次のような組み込みのロールが割り当てられている必要があります。 

* [[所有者]](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#owner)
* [Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#contributor)
* [ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#network-contributor)

以降のセクションで説明するアクセス許可を持つ[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json)を作成することで、よりきめ細かいアクセスを提供できます。

> [!IMPORTANT]
> この記事では、プライベート エンドポイントまたはプライベート リンク サービスを作成するための特定のアクセス許可の一覧を示します。 Azure SQL の Microsoft.SQL 共同作成者ロールなど、プライベート リンクを使用してアクセス権を付与するサービスに関連する特定のアクセス許可を追加してください。 組み込みロールの詳細については、[ロールベースのアクセス制御](../role-based-access-control/built-in-roles.md)に関するページをご覧ください。

Microsoft.Network と、デプロイする特定のリソース プロバイダー (Microsoft Sql など) をサブスクリプション レベルで登録する必要があります。

![image](https://user-images.githubusercontent.com/20302679/129105527-b946eee9-038a-46ef-b446-be371eb23ca9.png)

## <a name="private-endpoint"></a>プライベート エンドポイント

このセクションでは、プライベート エンドポイントをデプロイするために必要なきめ細かいアクセス許可の一覧を示します。

| アクション                                                              | 説明                                                                      |
| ---------                                                           | -------------                                                                 |
| Microsoft.Resources/deployments/*                                   | デプロイの作成と管理                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | リソース グループのリソースを読み取ります                                     |
| Microsoft.Network/virtualNetworks/read                              | 仮想ネットワークの定義を読み取ります                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | 仮想ネットワーク サブネットの定義を読み取ります                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | 仮想ネットワーク サブネットを作成するか、既存の仮想ネットワーク サブネットを更新します。|
| Microsoft.Network/virtualNetworks/subnets/join/action               | 仮想ネットワークに参加します。                                                       |
| Microsoft.Network/privateEndpoints/read                             | プライベート エンドポイントのリソースを読み取ります                                             |
| Microsoft.Network/privateEndpoints/write                            | 新しいプライベート エンドポイントを作成するか、または既存のプライベート エンドポイントを更新します       |
| Microsoft.Network/locations/availablePrivateEndpointTypes/read      | 使用できるプライベート エンドポイント リソースを読み取ります                                     |

上記のアクセス許可の JSON 形式を次に示します。 独自の roleName、description、および assignableScopes を入力します。

```JSON
{
 "properties": {
   "roleName": "Role Name",
   "description": "Description",
   "assignableScopes": [
     "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
   ],
   "permissions": [
     {
       "actions": [
         "Microsoft.Resources/deployments/*",
         "Microsoft.Resources/subscriptions/resourceGroups/read",
         "Microsoft.Network/virtualNetworks/read",
         "Microsoft.Network/virtualNetworks/subnets/read",
         "Microsoft.Network/virtualNetworks/subnets/write",
         "Microsoft.Network/virtualNetworks/subnets/join/action",
         "Microsoft.Network/privateEndpoints/read",
         "Microsoft.Network/privateEndpoints/write",
         "Microsoft.Network/locations/availablePrivateEndpointTypes/read"
       ],
       "notActions": [],
       "dataActions": [],
       "notDataActions": []
     }
   ]
 }
}
```

## <a name="private-link-service"></a>プライベート リンク サービス

このセクションでは、プライベート リンク サービスをデプロイするために必要な詳細なアクセス許可の一覧を示します。

| アクション | 説明   |
| --------- | ------------- |
| Microsoft.Resources/deployments/*                                   | デプロイの作成と管理                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | リソース グループのリソースを読み取ります                                     |
| Microsoft.Network/virtualNetworks/read                              | 仮想ネットワークの定義を読み取ります                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | 仮想ネットワーク サブネットの定義を読み取ります                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | 仮想ネットワーク サブネットを作成するか、既存の仮想ネットワーク サブネットを更新します。|
| Microsoft.Network/privateLinkServices/read | プライベート リンクのサービス リソースを読み取ります|
| Microsoft.Network/privateLinkServices/write | 新しいプライベート リンク サービスを作成するか、または既存のプライベート リンク サービスを更新します|
| Microsoft.Network/privateLinkServices/privateEndpointConnections/read | プライベート エンドポイント接続の定義を読み取ります |
| Microsoft.Network/privateLinkServices/privateEndpointConnections/write | 新しいプライベート エンドポイント接続を作成するか、または既存のプライベート エンドポイント接続を更新します|
| Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 |
| Microsoft.Network/loadBalancers/read | ロード バランサー定義を読み取ります |
| Microsoft.Network/loadBalancers/write | ロード バランサーを作成するか、既存のロード バランサーを更新します。 |

```JSON
{
  "properties": {
    "roleName": "Role Name",
    "description": "Description",
    "assignableScopes": [
      "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/write",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/privateLinkServices/read",
          "Microsoft.Network/privateLinkServices/write",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/read",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/write",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Network/loadBalancers/read",
          "Microsoft.Network/loadBalancers/write"
        ],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

## <a name="next-steps"></a>次のステップ

Azure Private Link のプライベート エンドポイントおよびプライベート リンク サービスの詳細については、以下を参照してください。

- [Azure プライベート エンドポイントとは](private-endpoint-overview.md)
- [Azure Private Link サービスとは](private-link-service-overview.md)
