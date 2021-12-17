---
title: クイック スタート - ポータルを使用して接続済みレジストリを作成する
description: Azure portal を使用して、イメージおよび他の成果物をクラウド レジストリと同期できる、接続された Azure Container Registry のリソースを作成します。
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6181f0fbdea8c03741f28dc9023a6761971cf2e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029703"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-portal"></a>クイック スタート: Azure portal を使用したコンテナー レジストリの作成

このクイック スタートでは、Azure portal を使用して Azure で[接続レジストリ](intro-connected-registry.md) リソースを作成します。 Azure Container Registry の接続されたレジストリ機能を使用すると、リモートで、またはオンプレミスにレジストリをデプロイし、イメージや他の成果物をクラウド レジストリと同期できます。 

ここでは、クラウド レジストリ用に 2 つの接続されたレジストリ リソースを作成します。1 つの接続されたレジストリでは、読み取りおよび書き込み (成果物のプルとプッシュ) 機能が許可され、1 つは読み取り専用機能を許可します。 

接続されたレジストリを作成した後は、他のガイドに従って、オンプレミスまたはリモートのインフラストラクチャにデプロイして使用できます。

## <a name="prerequisites"></a>前提条件

* Azure Container Registry - コンテナー レジストリがまだない場合は、接続されたレジストリをサポートする[リージョン](intro-connected-registry.md#available-regions)に [1 つ作成](container-registry-get-started-portal.md)します (Premium レベルが必要)。 

イメージをコンテナー レジストリにインポートするには、Azure CLI を使用します。[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>クラウド レジストリの専用データ エンドポイントを有効にする

クラウド内の Azure コンテナー レジストリの[専用データ エンドポイント](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)を有効にします。 この手順は、接続されたレジストリがクラウド レジストリと通信するために必要です。

1. [Azure portal](https://portal.azure.com) で、自分のコンテナー レジストリに移動します。
1. **[ネットワーキング] > [パブリック アクセス]** を選択します。
**[Enable dedicated data endpoint]\(専用データ エンドポイントを有効にする\)** チェックボックスを選択し ます。
1. **[保存]** を選択します。

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>読み取りおよび書き込み機能用の接続されたレジストリ リソースを作成する

次の手順では、クラウド レジストリにリンクされている接続されたレジストリを [ReadWrite モード](intro-connected-registry.md#modes)で作成します。

1. [Azure portal](https://portal.azure.com) で、自分のコンテナー レジストリに移動します。
1. **[Connected registries (Preview)]\(接続されたレジストリ (プレビュー)\) > [+ 作成]** を選択します。
1. 次の表の値を入力または選択し、 **[保存]** を選択します。


|Item  |説明  |
|---------|---------|
|Parent     | クラウド レジストリにリンクされている接続されたレジストリで **[親なし]** を選択します。        |
|モード     | **[ReadWrite]** を選択します。         |
|Name     | 接続されたレジストリ名は、先頭を文字とし、英数字のみを使う必要があります。 長さは 5 文字から 40 文字にし、この Azure コンテナー レジストリの階層で一意にする必要があります。       |
|ログのプロパティ     | 既定の設定をそのまま使用します。       |
|Sync properties (同期プロパティ)    | 既定の設定をそのまま使用します。 既定では同期スケジュールが定義されていないので、中断することなく、クラウド レジストリと接続されたレジストリの間でリポジトリが同期されます。      |
|リポジトリ     | 前の手順でインポートしたリポジトリの名前を選択または入力します。 指定されたリポジトリは、デプロイされると、クラウド レジストリと接続されたレジストリの間で同期されます。     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readwrite-connected-registry.png" alt-text="ReadWrite モードで接続されたレジストリを作成する":::


> [!IMPORTANT]
> 下位レイヤーがインターネットにアクセスできない入れ子になったシナリオをサポートするには、常に `acr/connected-registry` リポジトリの同期を許可する必要があります。 このリポジトリには、接続されたレジストリのランタイムのイメージが含まれています。

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>読み取り専用機能用の接続されたレジストリ リソースを作成する

次の手順では、前のセクションで作成した接続されたレジストリが親である、接続されたレジストリを [ReadOnly モード](intro-connected-registry.md#modes)で作成します。 この接続されたレジストリでは、デプロイ後に読み取り専用 (成果物のプル) 機能が有効になります。

1. [Azure portal](https://portal.azure.com) で、自分のコンテナー レジストリに移動します。
1. **[Connected registries (Preview)]\(接続されたレジストリ (プレビュー)\) > [+ 作成]** を選択します。
1. 次の表の値を入力または選択し、 **[保存]** を選択します。


|Item  |説明  |
|---------|---------|
|Parent     | 前に作成した、接続されたレジストリを選択します。        |
|モード     | **[ReadOnly]** を選択します。         |
|Name     | 接続されたレジストリ名は、先頭を文字とし、英数字のみを使う必要があります。 長さは 5 文字から 40 文字にし、この Azure コンテナー レジストリの階層で一意にする必要があります。      |
|ログのプロパティ     | 既定の設定をそのまま使用します。       |
|Sync properties (同期プロパティ)    | 既定の設定をそのまま使用します。 既定では同期スケジュールが定義されていないので、中断することなく、クラウド レジストリと接続されたレジストリの間でリポジトリが同期されます。      |
|リポジトリ     | 前の手順でインポートしたリポジトリの名前を選択または入力します。 指定されたリポジトリは、デプロイされると、親レジストリと接続されたレジストリの間で同期されます。     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readonly-connected-registry.png" alt-text="ReadOnly モードで接続されたレジストリを作成する":::

## <a name="view-connected-registry-properties"></a>接続されたレジストリのプロパティを表示する

接続状態 (オフライン、オンライン、または異常) やアクティブ化されている (オンプレミスにデプロイされている) かどうかなどのプロパティを表示する、接続されたレジストリをポータルで選択します。 次の例では、接続されたレジストリはデプロイされていません。 接続状態が "オフライン" の場合は、現在クラウドから切断されていることを示します。

:::image type="content" source="media/quickstart-connected-registry-portal/connected-registry-properties.png" alt-text="接続されたレジストリのプロパティを表示する":::

このビューから、接続文字列を生成し、必要に応じて[同期トークン](overview-connected-registry-access.md#sync-token)のパスワードを生成することもできます。 接続文字列には、接続されたレジストリをデプロイし、親レジストリと内容を同期するのに使用される構成設定が含まれます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure portal を使用して Azure で 2 つの接続されたレジストリ リソースを作成しました。 これらの新しい接続されたレジストリ リソースはクラウド レジストリに関連付けられているので、成果物をクラウド レジストリと同期できます。

接続されたレジストリを IoT Edge にデプロイして使用する方法については、接続されたレジストリのデプロイ ガイドに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: IoT Edge に接続されたレジストリをデプロイする][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
