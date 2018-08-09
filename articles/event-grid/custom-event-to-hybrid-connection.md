---
title: ハイブリッド接続への Azure Event Gridカスタム イベントの送信| Microsoft Docs
description: Azure Event Grid と Azure CLI を使用して、トピックを発行したり、そのイベントをサブスクライブしたりします。 ハイブリッド接続はエンドポイントに使用されます｡
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 06/29/2018
ms.topic: tutorial
ms.service: event-grid
ms.openlocfilehash: 544f5210adbea6791f9224a1e2be0743ce9995d5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434148"
---
# <a name="route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Azure CLI および Azure Event Grid を利用した Azure Relay Hybrid Connections へのカスタム イベントの転送

Azure Event Grid は、クラウドのイベント処理サービスです。 Azure Relay Hybrid Connections はサポートされているイベント ハンドラーの 1 つです｡ パブリックなエンドポイントがないアプリケーションからのイベントを処理する必要がある場合は､イベント ハンドラーとしてハイブリッド接続を利用します｡ そうしたアプリケーションは､コーポレート エンタープライズ ネットワークに存在することがあります｡ この記事では、Azure CLI からカスタム トピックを作成してトピックをサブスクライブし、イベントをトリガーして結果を表示します。 イベントはハイブリッド接続に送信します｡

## <a name="prerequisites"></a>前提条件

この記事では､ハイブリッド接続およびリスナー アプリケーションがすでに作成されていることを前提としています｡ ハイブリッド接続を開始するには､[Get started with Relay Hybrid Connections - .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) または [Get started with Relay Hybrid Connections - Node](../service-bus-relay/relay-hybrid-connections-node-get-started.md) を参照してください｡

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-a-resource-group"></a>リソース グループの作成

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 

次の例では、*gridResourceGroup* という名前のリソース グループを *westus2* の場所に作成します。

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>カスタム トピックの作成

Event Grid のトピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 次の例では、リソース グループにカスタム トピックを作成します。 `<topic_name>` は、トピックの一意の名前に置き換えてください。 トピック名は、DNS エントリによって表されるため、一意である必要があります。

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-topic"></a>トピックのサブスクライブ

どのイベントを追跡するかは、トピックをサブスクライブすることによって Event Grid に伝えます。次の例では､作成したトピックにサブスクライブして､エンドポイントに対するハイブリッド接続のリソース ID を渡しています｡ ハイブリッド接続の ID は以下の形式です｡

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

次のスクリプトは､リレー名前空間のリソース ID を取得します｡ ハイブリッド接続の ID を作成し､イベント グリッドのトピックにサブスクライブします｡ このスクリプトは、エンドポイントのタイプを `hybridconnection` に設定し､そのエンドポイントにハイブリッド接続 ID を使用します。

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"

az eventgrid event-subscription create \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid
```

## <a name="create-application-to-process-events"></a>イベントを処理するアプリケーションを作成する

ハイブリッド接続からイベントを取得できるアプリケーションが必要です。 [C# 用の Microsoft Azure Event Grid Hybrid Connection Consumer サンプル](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination)は、その操作を実行します。 前提条件の手順は既に完了しています。

1. Visual Studio 2017 Version 15.5 以降を使っていることを確認します。

1. ローカル コンピューターにリポジトリを複製します。

1. Visual Studio に HybridConnectionConsumer プロジェクトを読み込みます。

1. Program.cs で、`<relayConnectionString>` と `<hybridConnectionName>` を、作成した Relay 接続文字列およびハイブリッド接続名に置き換えます。

1. Visual Studio からアプリケーションをコンパイルして実行します。

## <a name="send-an-event-to-your-topic"></a>トピックへのイベントの送信

イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 この記事では、Azure CLI を使ってイベントをトリガーする方法を示します。 代わりに、[Event Grid パブリッシャー アプリケーション](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher)を使うこともできます。

まず、カスタム トピックの URL とキーを取得します。 `<topic_name>` には、先ほどと同じトピック名を使用してください。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

この記事では、単純化するために、トピックに送信するサンプル イベント データを使用します。 通常はイベント データをアプリケーションまたは Azure サービスから送信することになります。 CURL は、HTTP 要求を送信するユーティリティです。 この記事では、CURL を使用して、イベントをトピックに送信します。  次の例では、3 つのイベントをイベント グリッド トピックに送信します。

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

リスナー アプリケーションはイベント メッセージを受信することになります｡

## <a name="clean-up-resources"></a>リソースのクリーンアップ
引き続きこのイベントを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 それ以外の場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>次の手順

トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Event Grid について](overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)
