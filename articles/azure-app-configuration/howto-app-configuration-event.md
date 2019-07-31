---
title: Web エンドポイントにイベントを送信するように Azure App Configuration を設定するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、Web エンドポイントにキーと値の変更イベントを送信するように Azure App Configuration のイベント サブスクリプションを設定する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: jimmyca
manager: yegu
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d41ce06279536e3479b96d8d7afedf81624dbc9b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326598"
---
# <a name="quickstart-route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure App Configuration イベントを Web エンドポイントにルーティングする

このクイックスタートでは、Web エンドポイントにキーと値の変更イベントを送信するように Azure App Configuration のイベント サブスクリプションを設定する方法について説明します。 Azure App Configuration ユーザーは、キーと値が変更されたときに発行されるイベントにサブスクライブできます。 これらのイベントは、Webhook、Azure Functions、Azure Storage キュー、または Azure Event Grid でサポートされている他の任意のイベント ハンドラーをトリガーできます。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 ただし、この記事では、単純化するために、メッセージを収集して表示する Web アプリにイベントを送信します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)。 Azure Cloud Shell を使用することもできます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択した場合、この記事では、最新バージョンの Azure CLI (2.0.24 以降) が実行されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

Cloud Shell を使用していない場合は、先に `az login` でサインインする必要があります。

## <a name="create-a-resource-group"></a>リソース グループの作成

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create](/cli/azure/group) コマンドでリソース グループを作成します。 

次の例では、`<resource_group_name>` という名前のリソース グループを *westus* の場所に作成します。  `<resource_group_name>` を、リソース グループの一意の名前に置き換えます。

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration"></a>App Configuration の作成

`<appconfig_name>` をアプリ構成の一意の名前に置き換え、`<resource_group_name>` を先ほど作成したリソース グループに置き換えます。 名前は、DNS 名として使用されるため、一意である必要があります。

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name>
```

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

トピックをサブスクライブする前に、イベント メッセージ用のエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

`<your-site-name>` は、Web アプリの一意の名前に置き換えてください。 Web アプリ名は、DNS エントリの一部であるため、一意である必要があります。

```azurecli-interactive
$sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

現在表示されているメッセージがないサイトが表示されます。

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration"></a>App Configuration へのサブスクライブ

どのイベントを追跡し、どこにイベントを送信するかは、トピックをサブスクライブすることによって Event Grid に伝えます。 次の例では、作成したアプリ構成にサブスクライブし、Web アプリの URL をイベント通知のエンドポイントとして渡しています。 `<event_subscription_name>` をイベント サブスクリプションの名前に置き換えます。 `<resource_group_name>` と `<appconfig_name>` には、先ほど作成した値を使用します。

Web アプリのエンドポイントには、サフィックス `/api/updates/` が含まれている必要があります。

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 目のアイコンを選択してイベント データを展開します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 Web アプリには、サブスクリプションを検証するコードが含まれています。

![サブスクリプション イベントの表示](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>App Configuration イベントのトリガー

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 前の `<appconfig_name>` を使用して、キーと値を作成します。

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 Web アプリを表示して、送信したイベント確認します。

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
引き続きこのアプリ構成とイベント サブスクリプションを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 引き続き使用する予定がない場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

`<resource_group_name>` は、先ほど作成したリソース グループに置き換えます。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>次の手順

これで、トピックを作成し、イベントをサブスクライブする方法がわかったので、キーと値のイベントについて、また Event Grid でできることについて、さらに情報を収集しましょう。

- [キーと値のイベントへの対応](concept-app-configuration-event.md)
- [Event Grid について](../event-grid/overview.md)
- [Azure Event Grid ハンドラー](../event-grid/event-handlers.md)
