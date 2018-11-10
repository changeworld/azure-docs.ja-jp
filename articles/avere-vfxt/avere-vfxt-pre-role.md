---
title: コントローラーなしで Avere ロールを作成する - Avere vFXT for Azure
description: クラスター コントローラー VM がない状態で必要な RBAC ロールを作成する方法です
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670029"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>コントローラーなしで Avere vFXT クラスター ランタイム アクセス ロールを作成する

このドキュメントでは、クラスター コントローラー VM を作成する前にコマンド ラインからクラスター ノード アクセス ロールを作成する方法を示します。 

クラスター コントローラーから作成する方法については、「[クラスター ノードのアクセス ロールを作成する](avere-vfxt-deploy.md#create-the-cluster-node-access-role)」をご覧ください。 コントローラー イメージには、ロールのプロトタイプ ファイルが含まれています。 自分のサブスクリプション ID でファイルを更新し、それを使用してコントローラー VM 上でローカルにロールを定義できます。

## <a name="create-an-azure-rbac-role"></a>Azure RBAC ロールを作成する

Avere vFXT システムでは、[ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) を使用して、vFXT クラスター ノードに必要なタスクの実行を承認します。  

通常の vFXT クラスター操作の一環として、個々の vFXT ノードでは、Azure リソース プロパティの読み取り、ストレージの管理、他のノードのネットワーク インターフェイス設定の制御などを行う必要があります。 

このロールは、vFXT ノードに対してのみ使用され、クラスター コントローラー VM には使用されません。  

コントローラーの前にロールを作成する場合は、次の手順のようにします。 

1. Azure portal で Azure Cloud Shell を開くか、または [https://shell.azure.com](https://shell.azure.com) を参照します。

1. Azure CLI コマンドを使用して、vFXT サブスクリプションに切り替えます。

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. 次のコマンドを使用して、マーケットプレース イメージからロールの定義をダウンロードし、編集します。 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. ファイルを編集し、サブスクリプション ID を含めて、その上の行を削除します。 ファイルを ``avere-cluster.json``という名前で保存します。

   ![サブスクリプション ID と、削除対象として "この行を削除する" ように指示する行が選択されているコンソール テキスト エディター](media/avere-vfxt-edit-role.png)

5. ロールを作成します。  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

クラスターを作成するときに、ロールの名前を指定します (この場合は `avere-cluster`)。 クラスター作成スクリプトによって、新しく作成されるクラスター ノードにロールが割り当てられます。 

## <a name="sample-cluster-node-runtime-role-definition"></a>クラスター ノード ランタイムのロール定義のサンプル

> [!IMPORTANT] 
> このサンプルの定義は、製品の GA 前バージョンから取得されたものです。 最新の製品ディストリビューションで手に入るバージョンがこれと異なる場合は、そちらのバージョンを使用してください。

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```