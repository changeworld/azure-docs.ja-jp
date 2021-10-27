---
title: エンドポイントとルートを管理する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins データのエンドポイントとイベント ルートを設定および管理する方法について説明します
author: baanders
ms.author: baanders
ms.date: 7/30/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fb27a56dcf1661a95b6c7926a3c11628f6341446
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133901"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Azure Digital Twins でのエンドポイントとルートの管理

Azure Digital Twins では、ダウンストリームのサービスや接続されているコンピューティング リソースに[イベント通知](concepts-event-notifications.md)をルーティングすることができます。 これを行うには、まず、イベントを受信できる **エンドポイント** を設定します。 この後、Azure Digital Twins によって生成されたどのイベントをどのエンドポイントに送信するかを指定する "[イベント ルート](concepts-route-events.md)" を作成することができます。

この記事では、[Azure portal](https://portal.azure.com)、[REST API](/rest/api/azure-digitaltwins/)、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)、および [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) を使用して、エンドポイントとルートを作成する手順について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure アカウント** が必要 ([無料で設定できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* ご利用の Azure サブスクリプションに **Azure Digital Twins インスタンス** が必要となります。 まだインスタンスをお持ちでない場合は、「[インスタンスと認証を設定する](how-to-set-up-instance-portal.md)」の手順を使用して作成してください。 セットアップ中、次の値をメモしておいてください。後でこの記事の中で使用します。
    - インスタンス名
    - Resource group

これらの詳細については、インスタンスの設定後に、[Azure portal](https://portal.azure.com) で確認できます。 ポータルにログインし、ポータルの検索バーでインスタンスの名前を検索します。
 
:::image type="content" source="media/how-to-manage-routes/search-field-portal.png" alt-text="Azure portal 検索バーのスクリーンショット。" lightbox="media/how-to-manage-routes/search-field-portal.png":::

結果からインスタンスを選択すると、インスタンスの [概要] ページに詳細が表示されます。

:::image type="content" source="media/how-to-manage-routes/instance-details.png" alt-text="Azure portal での Azure Digital Twins インスタンスの [概要] ページのスクリーンショット。名前とリソース グループが強調表示されています。":::

このガイドに従って Azure CLI を使用する場合は、次の手順に従ってください。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins のエンドポイントの作成

インスタンスに作成できるエンドポイントのタイプは次のとおりです。
* [Event Grid](../event-grid/overview.md) トピック
* [Event Hubs](../event-hubs/event-hubs-about.md) ハブ
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) トピック

>[!NOTE]
> Event Grid エンドポイントの場合は、Event Grid **トピック** のみがサポートされます。 Event Grid **ドメイン** は、エンドポイントとしてサポートされていません。

さまざまなエンドポイントのタイプの詳細については、"[Azure メッセージング サービスの選択](../event-grid/compare-messaging-services.md)" に関する記事を参照してください。

このセクションでは、[Azure portal](https://portal.azure.com) または [Azure CLI](/cli/azure/dt/endpoint?view=azure-cli-latest&preserve-view=true) を使用してエンドポイントを作成する方法について説明します。 [DigitalTwinsEndpoint コントロール プレーン API](/rest/api/digital-twins/controlplane/endpoints) を使用してエンドポイントを管理することもできます。

### <a name="prerequisite-create-endpoint-resources"></a>前提条件:エンドポイント リソースを作成する

エンドポイントを Azure Digital Twins にリンクするには、エンドポイントで使用するイベント グリッド トピック、イベント ハブ、または Service Bus トピックが既に存在している必要があります。

次の表を使用して、エンドポイントを作成する前に設定する必要があるリソースを確認してください。

| エンドポイントの種類 | 必要なリソース (作成手順にリンクされています) |
| --- | --- |
| Event Grid エンドポイント | [イベント グリッド トピック](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)<br/>*イベント スキーマは、Event Grid スキーマまたは Cloud Event スキーマ v1.0 である必要があります |
| Event Hubs エンドポイント | [Event&nbsp;Hubs&nbsp;名前空間](../event-hubs/event-hubs-create.md)<br/><br/>[イベント ハブ](../event-hubs/event-hubs-create.md)<br/><br/>(省略可能) キーベースの認証用の[承認規則](../event-hubs/authorize-access-shared-access-signature.md) | 
| Service Bus エンドポイント | [Service Bus 名前空間](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus トピック](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (省略可能) キーベースの認証用の[承認規則](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)|

### <a name="create-the-endpoint"></a>エンドポイントを作成する 

エンドポイント リソースを作成したら、Azure Digital Twins エンドポイントにそれらを使用できます。 

# <a name="portal"></a>[ポータル](#tab/portal) 

新しいエンドポイントを作成するには、[Azure portal](https://portal.azure.com) でインスタンスのページに移動します (ポータルの検索バーにインスタンスの名前を入力することで、それを見つけることができます)。

1. インスタンスのメニューから、 _[エンドポイント]_ を選択します。 次に、 *[エンドポイント]* ページで、 *[+ エンドポイントの作成]* を選択します。 これにより、 *[エンドポイントの作成]* ページが開きます。次の手順でフィールドに入力します。

    :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-grid.png" alt-text="Azure portal で種類が Event Grid のエンドポイントを作成するスクリーンショット。" lightbox="media/how-to-manage-routes/create-endpoint-event-grid.png":::

1. エンドポイントの **[名前]** を入力し、 **[エンドポイントの種類]** を選択します。

1. エンドポイントの種類にとって必要なその他の詳細 (サブスクリプションや[上](#prerequisite-create-endpoint-resources)のエンドポイント リソースなど) を入力します。
    1. Event Hub と Service Bus エンドポイントでのみ、 **[認証の種類]** を選択する必要があります。 事前に作成された承認規則によるキーベースの認証を使用できます。エンドポイントで Azure Digital Twins インスタンスの[マネージド ID](concepts-security.md#managed-identity-for-accessing-other-resources) を使用する場合は、ID ベースの認証を使用できます。 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Azure portal で種類が Event Hub のエンドポイントを作成するスクリーンショット。" lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. _[保存]_ を選択して、エンドポイントの作成を終了します。

>[!IMPORTANT]
> エンドポイントに対する ID ベースの認証を問題なく使用するには、「[マネージド ID を使用してイベントをルーティングする](how-to-route-with-managed-identity.md)」の手順に従って、インスタンスのマネージド ID を作成する必要があります。

エンドポイントを作成したら、上部の Azure portal バーの通知アイコンを確認することで、エンドポイントが正常に作成されたことを確認できます。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-notifications.png" alt-text="Azure portal のエンドポイントの作成を確認する通知のスクリーンショット。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

エンドポイントの作成に失敗した場合は、エラー メッセージを確認し、数分後に再試行してください。

また、Azure Digital Twins インスタンスの *[エンドポイント]* ページに戻って、作成したエンドポイントを表示することもできます。

これで、エンドポイント用に選択した名前で、ベント グリッド、イベント ハブ、または Service Bus トピックが Azure Digital Twins の内部のエンドポイントとして使用できるようになりました。 通常、この名前を、**イベント ルート** ([この記事の中で後で](#create-an-event-route)作成します) のターゲットとして使用します。

# <a name="cli"></a>[CLI](#tab/cli) 

次の例では、[Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) の [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) コマンドを使用してエンドポイントを作成する方法を示します。 コマンドのプレースホルダーは、独自のリソースの詳細に置き換えます。

Event Grid エンドポイントを作成するには:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Event Hubs エンドポイントを作成するには (キーベース認証):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Service Bus トピック エンドポイントを作成するには (キーベース認証):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

これらのコマンドが正常に実行されると、イベント グリッド、イベント ハブ、または Service Bus トピックが、Azure Digital Twins 内のエンドポイントとして、`--endpoint-name` 引数で指定した名前で使用できるようになります。 通常、この名前を、**イベント ルート** ([この記事の中で後で](#create-an-event-route)作成します) のターゲットとして使用します。

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>ID ベースの認証を使用してエンドポイントを作成する

また、ID ベースの認証を使用するエンドポイントを作成し、そのエンドポイントを[マネージド ID](concepts-security.md#managed-identity-for-accessing-other-resources) で使用することもできます。 このオプションは、種類が Event Hubs と Service Bus のエンドポイントでのみ使用できます (Event Grid ではサポートされていません)。

この種類のエンドポイントを作成する CLI コマンドは次のようになります。 コマンドのプレースホルダーには次の値を設定する必要があります。
* Azure Digital Twins インスタンスの Azure リソース ID
* エンドポイント名
* エンドポイントの種類
* エンドポイント リソースの名前空間
* イベント ハブまたは Service Bus トピックの名前
* Azure Digital Twins インスタンスの場所

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

### <a name="create-an-endpoint-with-dead-lettering"></a>配信不能処理を行うエンドポイントを作成する

エンドポイントでは、一定の時間内にイベントを配信できない場合、あるいはイベントの配信を一定回数試行したが配信できない場合、未配信イベントをストレージ アカウントに送信できます。 このプロセスは **配信不能処理** と呼ばれます。

必要なストレージ リソースは、[Azure portal](https://ms.portal.azure.com/#home) または [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) を使用して設定できます。 ただし、配信不能処理が有効なエンドポイントを作成するには、[Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) または[コントロール プレーン API](concepts-apis-sdks.md#overview-control-plane-apis) を使用する必要があります。

配信不能処理の詳細については、「[イベント ルート](concepts-route-events.md#dead-letter-events)」を参照してください。 配信不能処理を構成してエンドポイントを設定する方法については、このセクションの残りの部分を読み進めます。

#### <a name="set-up-storage-resources"></a>ストレージ リソースを設定する

配信不能の場所を設定するには、ご使用の Azure アカウントに[コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)が設定された[ストレージ アカウント](../storage/common/storage-account-create.md?tabs=azure-portal)が必要です。 

後でエンドポイントを作成するときに、このコンテナーの URI を指定します。 配信不能の場所は、[SAS トークン](../storage/common/storage-sas-overview.md)を含むコンテナー URI としてエンドポイントに提供されます。 このトークンには、ストレージ アカウント内の宛先コンテナーに対する `write` アクセス許可が必要です。 完全な形式の **配信不能 SAS URI** は、`https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` という形式になります。

以下の手順に従って、Azure アカウントでこれらのストレージ リソースを設定し、次のセクションでエンドポイント接続の設定を準備します。

1. 「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md?tabs=azure-portal)」の手順に従って、Azure サブスクリプションに **ストレージ アカウント** を作成します。 後で使用するために、そのストレージ アカウント名を書き留めておきます。
1. 「[コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)」の手順に従って、新しいストレージ アカウント内に **コンテナー** を作成します。 後で使用するために、そのコンテナー名を書き留めておきます。

##### <a name="create-a-sas-token"></a>SAS トークンを作成する

次に、ストレージ アカウント用に **SAS トークン** を作成して、エンドポイントからアクセスするときにこれを使用できるようにします。

# <a name="portal"></a>[ポータル](#tab/portal)

1. まず、[Azure portal](https://ms.portal.azure.com/#home) でご自分のストレージ アカウントに移動します (ポータルの検索バーを使って名前で見つけることができます)。
1. ストレージ アカウントのページで、左側のナビゲーション バーの _[Shared Access Signature]_ リンクを選択し、SAS トークンの設定を開始します。

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-1.png" alt-text="Azure portal の [ストレージ アカウント] ページのスクリーンショット。" lightbox="./media/how-to-manage-routes/generate-sas-token-1.png":::

1. *Shared Access Signature のページ* の *[使用できるサービス]* と *[使用できるリソースの種類]* で、目的の設定を選択します。 カテゴリごとに少なくとも 1 つのボックスを選択する必要があります。 *[与えられているアクセス許可]* で **[書き込み]** を選択します (必要に応じて他のアクセス許可を選択することもできます)。
1. 残りの設定には、任意の値を設定します。
1. 完了後、 _[SAS と接続文字列を生成する]_ ボタンを選択して SAS トークンを生成します。 

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-2.png" alt-text="SAS トークンを生成するためのすべての設定の選択が示されている Azure portal の [ストレージ アカウント] ページのスクリーンショット。" lightbox="./media/how-to-manage-routes/generate-sas-token-2.png"::: 

1. これにより、同じページの下部の選択した設定の下に、いくつかの SAS と接続文字列の値が生成されます。 下にスクロールして値を表示し、 *[クリップボードにコピー]* アイコンを使用して **SAS トークン** 値をコピーします。 後で使用するために保存します。

    :::image type="content" source="./media/how-to-manage-routes/copy-sas-token.png" alt-text="Azure portal の [ストレージ アカウント] ページのスクリーンショット。配信不能シークレットで使用する SAS トークンのコピー方法が強調表示されています。" lightbox="./media/how-to-manage-routes/copy-sas-token.png":::

# <a name="cli"></a>[CLI](#tab/cli)

1. 次のコマンドを使用してストレージ アカウント キーを取得し、いずれかのキーの値をコピーします。

    ```azurecli
    az storage account keys list --account-name <storage-account-name>
    ```

1. 次のコマンドを使用して、有効期限を選択し、ストレージ アカウントの SAS トークンを生成します。

    ```azurecli
    az storage account generate-sas --account-name <storage-account-name> --account-key <storage-account-key> --expiry <expiration-date> --services bfqt --resource-types o --permissions w
    ```

    このコマンドの出力は SAS トークンです。 後で使用するために SAS トークン値をコピーします。

    > [!NOTE]
    > このコマンドには、"**b** lob"、"**f** ile"、"**q** ueue"、および "**t** able" の "*サービス*" と、"**o** bject" の "*リソースの種類*" が含まれ、"**w** rite" の "*権限*" を許可しています。
    >
    > `az storage account generate-sas` コマンドとそのパラメーターの詳細については、[Azure CLI のリファレンス](/cli/azure/storage/account?view=azure-cli-latest&preserve-view=true#az_storage_account_generate_sas)を参照してください。

---

#### <a name="create-the-dead-letter-endpoint"></a>配信不能エンドポイントを作成する

# <a name="portal"></a>[ポータル](#tab/portal)

配信不能処理が有効なエンドポイントを作成するには、Azure portal ではなく [CLI コマンド](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)または[コントロール プレーン API](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) を使用してエンドポイントを作成する必要があります。

これを Azure CLI で行う方法の手順については、このセクションの [CLI] タブに切り替えてください。

# <a name="cli"></a>[CLI](#tab/cli)

配信不能処理が有効なエンドポイントを作成するには、次の配信不能パラメーターを、[Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) の [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) コマンドに追加します。

パラメーターの値は、[前のセクション](#set-up-storage-resources)で収集したストレージ アカウント名、コンテナー名、SAS トークンで構成された **配信不能 SAS URI** です。 このパラメーターにより、キーベースの認証を使用するエンドポイントが作成されます。

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

配信不能処理が有効になっている目的の種類のエンドポイントを作成するには、前の「[エンドポイントを作成する](#create-the-endpoint)」セクションのエンドポイント作成コマンドの末尾に、このパラメーターを追加します。

または、CLI の代わりに [Azure Digital Twins コントロール プレーン API](concepts-apis-sdks.md#overview-control-plane-apis) を使用して、配信不能エンドポイントを作成することもできます。 これを行うには、[DigitalTwinsEndpoint のドキュメント](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)を参照し、要求を構成して配信不能パラメーターを追加する方法を確認してください。

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>ID ベースの認証を使用して配信不能エンドポイントを作成する

また、ID ベースの認証を使用する配信不能エンドポイントを作成し、そのエンドポイントを[マネージド ID](concepts-security.md#managed-identity-for-accessing-other-resources) で使用することもできます。 このオプションは、種類が Event Hubs と Service Bus のエンドポイントでのみ使用できます (Event Grid ではサポートされていません)。

この種類のエンドポイントを作成するには、前と同じ CLI コマンドを使用して [ID ベースの認証を使用するエンドポイントを作成する](#create-an-endpoint-with-identity-based-authentication)ときに、JSON ペイロードで `deadLetterUri` の追加フィールドを指定します。

コマンドのプレースホルダーには次の値を設定する必要があります。
* Azure Digital Twins インスタンスの Azure リソース ID
* エンドポイント名
* エンドポイントの種類
* エンドポイント リソースの名前空間
* イベント ハブまたは Service Bus トピックの名前
* **配信不能 SAS URI** の詳細: ストレージ アカウント名、コンテナー名
* Azure Digital Twins インスタンスの場所

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

#### <a name="message-storage-schema"></a>メッセージ ストレージ スキーマ

配信不能処理を行うエンドポイントが設定されると、配信不能メッセージが次の形式でストレージ アカウントに格納されます。

`<container>/<endpoint-name>/<year>/<month>/<day>/<hour>/<event-ID>.json`

配信不能メッセージは、元のエンドポイントに配信されることを意図していた元のイベントのスキーマと一致します。

次に、[ツイン作成通知](concepts-event-notifications.md#digital-twin-lifecycle-notifications)の配信不能メッセージの例を示します。

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>イベント ルートを作成する

Azure Digital Twins からエンドポイントに実際にデータを送信するには、**イベント ルート** を定義する必要があります。 これらのルートにより、開発者はシステム全体およびダウンストリーム サービスへのイベント フローを結び付けることができます。 1 つのルートで、複数の通知とイベントの種類を選択できるようにします。 イベント ルートの詳細については、[Azure Digital Twins イベントのルーティング](concepts-route-events.md)に関する記事をご覧ください。

**前提条件**:ルートの作成に進む前に、この記事の前出の説明に従ってエンドポイントを作成する必要があります。 エンドポイントの設定が完了したら、イベント ルートの作成に進むことができます。

>[!NOTE]
>エンドポイントを最近デプロイした場合は、新しいイベント ルートでそれらの使用を試みる **前に**、それらのデプロイが完了していることを確認します。 エンドポイントの準備ができていないためにルートのデプロイが失敗する場合は、数分待ってからやり直してください。
>
> このフローをスクリプト化する場合は、ルートの設定に進む前に、エンドポイント サービスのデプロイが完了するまでの 2 ～ 3 分の待機時間を組み込んで、このことを考慮に入れておくことを推奨します。

ルート定義にはこれらの要素を含めることができます。
* 使用するルート名
* 使用するエンドポイントの名前
* エンドポイントに送信されるイベントを定義するフィルター
    - イベントが送信されないようにルートを無効にするには、`false` のフィルター値を使用します
    - 特定のフィルターを適用しないルートを有効にするには、`true` のフィルター値を使用します
    - その他の種類のフィルターの詳細については、後述の「[フィルター イベント](#filter-events)」セクションを参照してください

ルート名がない場合、メッセージは Azure Digital Twins の外部にルーティングされません。 ルート名があり、フィルターが `true` の場合は、すべてのメッセージがエンドポイントにルーティングされます。 ルート名があり、別のフィルターを追加した場合は、そのフィルターに基づいてメッセージがフィルター処理されます。

イベント ルートは、[Azure portal](https://portal.azure.com)、[EventRoutes データ プレーン API](/rest/api/digital-twins/dataplane/eventroutes)、または [az dt route CLI コマンド](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true)を使用して作成できます。 このセクションの残りの部分では、作成プロセスについて説明します。

# <a name="portal"></a>[ポータル](#tab/portal2)

イベント ルートを作成するには、[Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスの詳細ページに移動します (インスタンスを見つけるには、ポータルの検索バーにその名前を入力します)。

インスタンスのメニューから、 _[イベント ルート]_ を選択します。 次に、 *[イベント ルート]* ページで、 *[+ イベント ルートの作成]* を選択します。 

開いた *[イベント ルートの作成]* ページで、少なくとも次を選択します。
* _[名前]_ フィールドでルートの名前
* ルートの作成に使用する _[エンドポイント]_ 

有効にするルートについて、少なくとも `true` の **イベントルート フィルターを追加する** 必要もあります。 (`false` の既定値のままにするとルートが作成されますが、イベントがそれに送信されません)。これを行うには、 _[詳細エディター]_ のスイッチを切り替えて有効にし、 *[フィルター]* ボックスに `true` と書き込みます。

:::image type="content" source="media/how-to-manage-routes/create-event-route-no-filter.png" alt-text="Azure portal でインスタンスのイベント ルートを作成するスクリーンショット。" lightbox="media/how-to-manage-routes/create-event-route-no-filter.png":::

完了したら、 _[保存]_ ボタンを選択して、イベント ルートを作成します。

# <a name="cli"></a>[CLI](#tab/cli2)

ルートは Azure Digital Twins CLI の [az dt route](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true) コマンドを使用して管理できます。 

CLI の使用方法および使用できるコマンドの詳細は、「[Azure Digital Twins CLI コマンド セット](concepts-cli.md)」を参照してください。

# <a name="net-sdk"></a>[.NET SDK](#tab/sdk2)

このセクションでは、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) を使用してイベント ルートを作成する方法について説明します。

`CreateOrReplaceEventRouteAsync` は、イベント ルートを追加するために使用される SDK 呼び出しです。 その使用例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

#### <a name="event-route-sample-sdk-code"></a>イベント ルートのサンプル SDK コード

次のサンプル メソッドでは、C# SDK を使用してイベント ルートを作成、一覧表示、削除する方法を示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

---

## <a name="filter-events"></a>イベントのフィルター処理

前述のように、ルートには **[フィルター]** フィールドがあります。 ルートのフィルター値が `false` の場合、エンドポイントにイベントが送信されません。 

`true` の最小フィルターを有効にすると、エンドポイントでは Azure Digital Twins からさまざまなイベントを受信します。
* Azure Digital Twins サービス API を使用して[デジタル ツイン](concepts-twins-graph.md)で発生するテレメトリ
* Azure Digital Twins インスタンスでツインのプロパティが変更されたときに発生する、ツインのプロパティ変更通知
* ツインまたはリレーションシップが作成または削除されたときに発生するライフサイクル イベント

より具体的なフィルターを定義することで、送信されるイベントの種類を制限できます。

>[!NOTE]
> フィルターは **大文字と小文字を区別** し、ペイロードの大文字と小文字と一致する必要があります。 
>
> テレメトリ フィルターの場合、これは、大文字と小文字の指定が、デバイスによって送信されたテレメトリ内の大文字と小文字の指定と一致する必要はあるが、必ずしもツインのモデル内に定義された大文字と小文字の指定に一致する必要はないことを意味します。

# <a name="portal"></a>[ポータル](#tab/portal3)

イベント ルートの作成中にイベント フィルターを追加するには、 *[イベント ルートを作成する]* ページの [イベント ルート フィルターの追加] セクションを使用します。 

いくつかの基本的な共通フィルター オプションから選択することも、高度なフィルター オプションを使用して独自のカスタム フィルターを作成することもできます。

### <a name="use-the-basic-filters"></a>基本的なフィルターを使用する

基本的なフィルターを使用するには、 _[イベントの種類]_ オプションを展開し、エンドポイントに送信する必要があるイベントに対応するチェックボックスをオンにします。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-1.png" alt-text="Azure portal の基本フィルターでイベント ルートを作成するスクリーンショット。イベントのチェックボックスが強調表示されています。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

これにより、選択したフィルターのテキストがフィルター テキスト ボックスに自動的に設定されます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-2.png" alt-text="Azure portal の基本フィルターでイベント ルートを作成するスクリーンショット。イベントの選択後の自動設定されたフィルター テキストが強調表示されています。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="use-the-advanced-filters"></a>高度なフィルターを使用する

または、[高度なフィルター] オプションを使用して、独自のカスタム フィルターを作成することもできます。

高度なフィルター オプションを使用してイベント ルートを作成するには、 _[詳細エディター]_ のスイッチを切り替えて、有効にします。 それにより、 *[フィルター]* ボックスに独自のイベント フィルターを記述できます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-advanced.png" alt-text="Azure portal の高度なフィルターを使用してイベント ルートを作成するスクリーンショット。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

# <a name="api"></a>[API](#tab/api)

API を使用して、カスタム フィルターを記述できます。 フィルターを追加するために、次の本文を含む PUT 要求を `https://<Your-Azure-Digital-Twins-host-name>/eventRoutes/<event-route-name>?api-version=2020-10-31` に対して使用できます。

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

---

### <a name="supported-route-filters"></a>サポートされているルート フィルター

サポートされているルート フィルターを次に示します。

| フィルター名 | 説明 | フィルター テキスト スキーマ | サポート状況の値 | 
| --- | --- | --- | --- |
| True/False | フィルター処理なしでルートを作成したり、ルートを無効にしてイベントが送信されないようにしたりすることができます | `<true/false>` | `true` = ルートはフィルター処理なしで有効になります。 <br> `false` = ルートは無効になります。 |
| Type | デジタル ツイン インスタンスを通過する[イベントの種類](concepts-route-events.md#types-of-event-messages) | `type = '<event-type>'` | 指定できるイベントの種類の値は次のとおりです。 <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| source | Azure Digital Twins インスタンスの名前 | `source = '<host-name>'`| 指定できるホスト名の値は次のとおりです。 <br> **通知の場合**: `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net` <br> **テレメトリの場合**: `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net/<twin-ID>`|
| サブジェクト | 上記のイベント ソースのコンテキストでのイベントの説明 | `subject = '<subject>'` | 指定できるサブジェクトの値は次のとおりです。 <br>**通知の場合**: サブジェクトは、サブジェクトの `<twin-ID>` <br> または URI 形式です。これは、次のように複数の部分または ID で一意に識別されます。<br>`<twin-ID>/relationships/<relationship-ID>`<br> **テレメトリの場合**: ツイン コンポーネントからテレメトリが生成された場合、サブジェクトはコンポーネント パス (例: `comp1.comp2`) です。 コンポーネントからテレメトリが生成されていない場合、サブジェクト フィールドは空になります。 |
| データ スキーマ | DTDL モデル ID | `dataschema = '<model-dtmi-ID>'` | **テレメトリの場合**: データ スキーマは、テレメトリを生成するツインまたはコンポーネントのモデル ID です。 たとえば、`dtmi:example:com:floor4;2` のように指定します。 <br>**通知の場合 (作成/削除)** :データ スキーマには、`$body.$metadata.$model` の通知本文でアクセスできます。 <br>**通知の場合 (更新)** :データ スキーマには、`$body.modelId` の通知本文でアクセスできます|
| Content type | データ値のコンテンツ タイプ | `datacontenttype = '<content-type>'` | コンテンツ タイプは`application/json` です。 |
| 仕様バージョン | 使用しているイベント スキーマのバージョン | `specversion = '<version>'` | バージョンは `1.0` である必要があります。 これは、CloudEvents スキーマ バージョン 1.0 を示しています。 |
| 通知本文 | 通知の `data` フィールドの任意のプロパティを参照します | `$body.<property>` | 通知の例については、「[イベント通知](concepts-event-notifications.md)」をご覧ください。 `data` フィールドの任意のプロパティは、`$body` を使用して参照できます

>[!NOTE]
> Azure Digital Twins では現在、配列内のフィールドに基づくイベントのフィルタリングはサポートされていません。 これには、[デジタル ツインの変更通知](concepts-event-notifications.md#digital-twin-change-notifications)の `patch` セクション内のプロパティを条件とするフィルタリングも含まれます。

上記のデータへの参照によって返される値としては、次のデータ型がサポートされています。

| データの種類 | 例 |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twin-ID>')`|
|**整数**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

ルート フィルターを定義するときは、次の演算子がサポートされます。

|ファミリ|オペレーター|例|
|-|-|-|
|論理|AND、OR、( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|比較|<、<=、>、>=、=、!=|`$body.temperature <= 5.5`

ルート フィルターを定義するときは、次の関数がサポートされます。

|機能|説明|例|
|--|--|--|
|STARTS_WITH(x,y)|値 `x` が文字列 `y` で始まっている場合、true を返します。|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | 値 `x` が文字列 `y` で終わっている場合、true を返します。|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 値 `x` に文字列 `y` が含まれる場合、true を返します。|`CONTAINS(subject, '<twin-ID>')`|

フィルターを実装または更新したときに、変更がデータ パイプラインに反映されるまでに数分かかることがあります。

## <a name="monitor-event-routes"></a>イベント ルートの監視

カウント、待ち時間、失敗率などのルーティング メトリックは、[Azure portal](https://portal.azure.com/) で確認できます。 

portal のホームページで、Azure Digital Twins インスタンスを検索して詳細を取得します。 左側にある Azure Digital Twins インスタンスのナビゲーション メニューから **[メトリック]** オプションを選択すると、 *[メトリック]* ページが表示されます。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins の [メトリック] ページが示されているスクリーンショット。":::

ここから、インスタンスのメトリックを表示したり、カスタム ビューを作成したりできます。

Azure Digital Twins メトリックの表示について詳しくは、[トラブルシューティング: メトリック](troubleshoot-metrics.md)を参照してください。

## <a name="next-steps"></a>次のステップ

受信できるさまざまな種類のイベント メッセージについてご確認ください。
* [イベント通知](concepts-event-notifications.md)