---
title: コントローラーのアクセス ロールのカスタマイズ - Avere vFXT for Azure
description: Avere vFXT クラスター コントローラーのカスタム アクセス ロールを作成する方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: c408efa7ca01928e25ac03f5ca63d0aef7d88839
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770044"
---
# <a name="customized-controller-access-role"></a>コントローラーのアクセス ロールのカスタマイズ

Avere vFXT for Azure クラスター コントローラーでは、マネージド ID とロールベースのアクセス制御 (RBAC) を使用して、クラスターを作成および管理できます。 

クラスター コントローラーには、既定で[組み込みの所有者ロール](../role-based-access-control/built-in-roles.md#owner)が割り当てられています。 また、コントローラーのアクセス スコープはそのリソース グループに設定されています。つまり、クラスターのリソース グループ外の要素を変更することはできません。

この記事では、既定の設定を使用する代わりにクラスター コントローラー用の独自のアクセス ロールを作成する方法について説明します。 

## <a name="edit-the-role-prototype"></a>ロールのプロトタイプを編集する

<https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt> で入手できるプロトタイプ ロールから開始します。

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

AssignableScopes ステートメントで、Avere vFXT for Azure デプロイのサブスクリプション ID を追加します。 名前をカスタマイズし、必要に応じて定義を追加または変更します。 

権限を制限する場合は注意してください。 コントローラーに十分なアクセス権がないと、クラスターの作成に失敗することがあります。 

クラスター コントローラーがクラスターを作成するために必要な権限の理解に役立つ情報を得るには、[サポート チケット](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)を開きます。 

カスタム ロール定義を .json ファイルとして保存します。 

## <a name="define-the-role"></a>ロールを定義する 

サブスクリプションにカスタム ロールの定義を追加するには、次の手順に従います。 

1. Azure portal で Azure Cloud Shell を開くか、または [https://shell.azure.com](https://shell.azure.com) を参照します。

1. Azure CLI コマンドを使用して、vFXT サブスクリプションに切り替えます。

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. ロールを作成します。

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   この例の ```/avere-contributor-custom.json``` を実際のファイル名とパスに置き換えてください。 

ロール定義コマンドの出力を保存します。これには、クラスターの作成テンプレートに指定する必要のあるロール識別子が含まれています。 

## <a name="find-the-role-id"></a>ロール ID を調べる

コントローラーにカスタム ロールを割り当てるには、Avere vFXT デプロイ テンプレートにロールのグローバル一意識別子 (GUID) を指定する必要があります。 

ロールの GUID は、次の形式の 32 文字の文字列です。8e3af657-a8ff-443c-a75c-2fe8c4bcb635

ロールの GUID を調べるには、```--name``` パラメーターにロール名を指定した次のコマンドを使用します。

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Avere vFXT for Azure をデプロイするときに、この文字列を **[Avere cluster create role ID]\(Avere クラスター作成ロール ID\)** フィールドに入力します。

## <a name="next-steps"></a>次の手順

「[Deploy the vFXT cluster (vFXT クラスターのデプロイ)](avere-vfxt-deploy.md)」で Avere vFXT for Azure をデプロイする方法を確認します
