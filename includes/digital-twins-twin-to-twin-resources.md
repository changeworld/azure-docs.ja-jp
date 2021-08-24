---
author: baanders
description: Event Grid エンドポイントとルートの設定方法を記述するインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/21/2021
ms.author: baanders
ms.openlocfilehash: 0311f53845a4ee18bf595d9efe2593d486818fb8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748563"
---
### <a name="create-the-event-grid-topic"></a>Event Grid トピックを作成する

[Event Grid](../articles/event-grid/overview.md) は、Azure サービスからのイベントを Azure 内の別の場所にルーティングして配信できる Azure サービスです。 [Event Grid トピック](../articles/event-grid/concepts.md)を作成すると、特定のイベントをソースから収集することができ、また、サブスクライバーは、トピックをリッスンすることで、配信されたイベントを受信することができます。

Event Grid トピックを作成するには、Azure Cloud Shell から次のコマンドを実行します。

```azurecli-interactive
az eventgrid topic create --resource-group <your-resource-group> --name <name-for-your-event-grid-topic> --location <region>
```

このコマンドからは、作成した Event Grid トピックについての情報が出力されます。 後で使用するため、Event Grid トピックに指定した **名前** を保存します。

### <a name="create-the-endpoint"></a>エンドポイントを作成する

次に、Azure Digital Twins に Event Grid エンドポイントを作成します。これにより、インスタンスが Event Grid トピックに接続されます。 下のコマンドを使用して、Event Grid トピックの名前と、必要に応じてその他のプレースホルダー フィールドを入力します。

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

このコマンドからは、作成したエンドポイントについての情報が出力されます。

出力から `provisioningState` フィールドを見つけ、その値が "Succeeded" になっていることを確認します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/output-endpoints.png" alt-text="provisioningState が Succeeded のエンドポイントが示された、Azure portal の Cloud Shell におけるエンドポイント クエリ結果のスクリーンショット。":::

また、エンドポイントがまだ作成されているという意味で、"Provisioning" と表示される場合もあります。 その場合は、数秒待ってから、次のコマンドを実行してエンドポイントの状態を確認します。 `provisioningState`が "Succeeded" と表示されるまで繰り返します。

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

後で使用するため、エンドポイントの **名前** を保存します。

### <a name="create-the-route"></a>ルートを作成する

次に、先ほど作成した Event Grid エンドポイントにイベントを送信する Azure Digital Twins ルートを作成します。 

これは、次の CLI コマンドを使用して行うことができます (エンドポイントの名前と、必要に応じてその他のプレースホルダー フィールドを入力します)。 このコマンドを実行すると、ツイン グラフで発生するすべてのイベントが転送されます。 [フィルター](../articles/digital-twins/how-to-manage-routes.md?tabs=portal%2Cportal2%2Cportal3#filter-events)を使用すると、必要に応じてイベントを特定のもののみに制限できます。

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

このコマンドからは、作成したルートについての情報が出力されます。

>[!NOTE]
>エンドポイント (前の手順のもの) は、それらを使用するイベント ルートをセットアップする前に、プロビジョニングを完了している必要があります。 エンドポイントの準備ができていないためにルートの作成が失敗する場合は、数分待ってからやり直してください。