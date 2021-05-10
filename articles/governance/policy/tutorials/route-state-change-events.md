---
title: 'チュートリアル: Azure CLI を使用してポリシー状態変更イベントを Event Grid にルーティングする'
description: このチュートリアルでは、ポリシー状態変更イベントをリッスンして Webhook を呼び出すように Event Grid を構成します。
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105734882"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>チュートリアル: Azure CLI を使用してポリシー状態変更イベントを Event Grid にルーティングする

この記事では、Azure Policy イベント サブスクリプションの設定を通じて Web エンドポイントにポリシー状態変更イベントを送信する方法について説明します。 Azure Policy ユーザーは、リソースでポリシーの状態が変化したときに生成されるイベントをサブスクライブできます。 これらのイベントは、Web hook、[Azure Functions](../../../azure-functions/index.yml)、[Azure Storage キュー](../../../storage/queues/index.yml)、または [Azure Event Grid](../../../event-grid/index.yml) でサポートされている他の任意のイベント ハンドラーをトリガーできます。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 ただし、このチュートリアルでは、単純化するために、メッセージを収集して表示する Web アプリにイベントを送信します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- このクイックスタートでは、Azure CLI バージョン 2.0.76 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

- 過去に Azure Policy または Event Grid を使用したことがある場合でも、それぞれのリソース プロバイダーを再登録してください。

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>リソース グループを作成する

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 

次の例では、`<resource_group_name>` という名前のリソース グループを _westus_ の場所に作成します。 `<resource_group_name>` を、リソース グループの一意の名前に置き換えます。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Event Grid のシステム トピックを作成する

リソース グループを作成したら、[システム トピック](../../../event-grid/system-topics.md)を作成します。 Event Grid でのシステム トピックは、Azure Policy や Azure Event Hubs などの Azure サービスによって発行された 1 つ以上のイベントを表します。 Azure Policy の状態の変更に関して、このシステム トピックでは `Microsoft.PolicyInsights.PolicyStates` という種類のトピックを使用します。 **scope** パラメーターの `<SubscriptionID>` は実際のサブスクリプションの ID に、また **resource-group** パラメーターの `<resource_group_name>` は、先ほど作成したリソース グループに置き換えてください。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

トピックをサブスクライブする前に、イベント メッセージ用のエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

`<your-site-name>` は、Web アプリの一意の名前に置き換えてください。 Web アプリ名は、DNS エントリの一部であるため、一意である必要があります。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

現在表示されているメッセージがないサイトが表示されます。

## <a name="subscribe-to-the-system-topic"></a>システム トピックをサブスクライブする

どのイベントを追跡し、どこにイベントを送信するかは、トピックをサブスクライブすることによって Event Grid に伝えます。 次の例では、作成したシステム トピックをサブスクライブし、イベント通知を受信するためのエンドポイントとして Web アプリの URL を渡しています。 `<event_subscription_name>` をイベント サブスクリプションの名前に置き換えます。 `<resource_group_name>` と `<your-site-name>` には、先ほど作成した値を使用します。

Web アプリのエンドポイントには、サフィックス `/api/updates/` が含まれている必要があります。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 目のアイコンを選択してイベント データを展開します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 Web アプリには、サブスクリプションを検証するコードが含まれています。

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="あらかじめ構築された Web アプリにおける Event Grid サブスクリプション検証イベントのスクリーンショット。":::

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイックスタートでは、ポリシーの割り当てを作成し、"**リソース グループでタグを必須にする**" 定義を割り当てます。 このポリシーの定義は、ポリシーの割り当て中に構成されたタグが存在しないリソース グループを特定します。

Event Grid トピックを保持するために作成したリソース グループを対象にポリシーの割り当てを作成するには、次のコマンドを実行します。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

上記のコマンドでは次の情報を使用します。

- **Name** - 割り当ての実際の名前。 この例では、_requiredtags-events_ を使用しています。
- **DisplayName** - ポリシーの割り当てに使用する表示名。 このケースでは、"_Require tag on RG_" を使用しています。
- **Scope** - スコープによって、ポリシーの割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループまで、適用対象は多岐にわたります。 &lt;scope&gt; は、実際のリソース グループの名前に置き換えてください。 リソース グループのスコープの形式は `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` です。
- **Policy** - 割り当ての作成に使用するポリシー定義 ID。 ここでは、"_リソース グループでタグを必須にする_" というポリシー定義の ID です。 ポリシー定義 ID を取得するには、次のコマンドを実行します。`az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

ポリシーの割り当て後、**Microsoft.PolicyInsights.PolicyStateCreated** イベント通知が Web アプリに表示されるのを待ちます。 作成したリソース グループに関して最初に表示される `data.complianceState` の値は _NonCompliant_ です。

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="あらかじめ構築された Web アプリにおけるリソース グループに対する Event Grid サブスクリプションの Policy State Created イベントのスクリーンショット。":::

> [!NOTE]
> リソース グループがサブスクリプションまたは管理グループの階層から他のポリシーの割り当てを継承している場合、それぞれのイベントも表示されます。 `data.policyDefinitionId` プロパティを評価してこのチュートリアルでの割り当てに関するイベントであることを確認してください。

## <a name="trigger-a-change-on-the-resource-group"></a>リソース グループの変更をトリガーする

リソース グループを準拠状態にするためには、**EventTest** という名前のタグが必要です。 リソース グループにタグを追加するには次のコマンドを使用します。`<SubscriptionID>` は実際のサブスクリプション ID に、また `<ResourceGroup>` はリソース グループの名前に置き換えてください。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

必要なタグをリソース グループに追加した後、Web アプリに **Microsoft.PolicyInsights.PolicyStateChanged** イベント通知が表示されるのを待ちます。 イベントを展開すると、今度は `data.complianceState` の値が _Compliant_ になっていることがわかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

引き続きこの Web アプリと Azure Policy イベント サブスクリプションを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 引き続き使用する予定がない場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

`<resource_group_name>` は、先ほど作成したリソース グループに置き換えます。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>次のステップ

Azure Policy に使用するトピックとイベント サブスクリプションの作成方法がわかったら、ポリシー状態変更イベントについて、また Event Grid でできることについてさらに情報を収集しましょう。

- [Azure Policy 状態変更イベントに対応する](../concepts/event-overview.md)
- [Event Grid の Azure Policy スキーマの詳細](../../../event-grid/event-schema-policy.md)
- [Event Grid について](../../../event-grid/overview.md)