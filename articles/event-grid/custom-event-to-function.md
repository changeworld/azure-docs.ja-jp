---
title: クイック スタート:カスタム イベントを Azure Functions に送信する - Event Grid
description: クイック スタート:Azure Event Grid と Azure CLI (または Azure portal) を使用して、トピックを発行したり、そのイベントをサブスクライブしたりします。 エンドポイントに Azure Functions を使用します。
ms.date: 09/28/2021
ms.topic: quickstart
ms.openlocfilehash: c6f0377b1b6489927de6a980bf4fc1ecb080e051
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277485"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>クイック スタート:Event Grid を使用して Azure Functions にカスタム イベントをルーティングする

Azure Event Grid は、クラウドのイベント処理サービスです。 Azure Functions は、サポートされているイベント ハンドラーの 1 つです。 この記事では、Azure portal を使用し、カスタム トピックを作成してそのトピックをサブスクライブし、イベントをトリガーして結果を表示します。 イベントを Azure Functions に送信します。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Azure 関数の作成
カスタム トピックをサブスクライブする前に、イベントを処理する関数を作成します。 

1. 「[関数アプリを作成する](../azure-functions/functions-get-started.md)」の手順に従って関数アプリを作成します。
1. **[関数アプリ]** ページで、左側のメニューの **[関数]** を選択します。 
1. ツール バーの **[+ コピー]** を選択して、関数を作成します。
1. **[関数の作成]** ページで、次の手順に従います。
    1. この手順は省略可能です。 **開発環境** では、関数コードの操作に使用する開発環境を選択します。 
    1. **[テンプレートの選択]** セクションで、 **[Azure Event Grid Trigger]\(Azure Event Grid トリガー\)** を選択します。 
    1. 関数の名前を入力します。 この例では、**HandleEventsFunc** です。 

        :::image type="content" source="./media/custom-event-to-function/function-event-grid-trigger.png" lightbox="./media/custom-event-to-function/function-event-grid-trigger.png" alt-text="Event Grid トリガーを選択する。":::
4. **[Code + Test]\(コードとテスト\)** ページを使用して、関数の既存のコードを確認し、更新します。 

    :::image type="content" source="./media/custom-event-to-function/function-code-test-menu.png" alt-text="Azure 関数の [Code + Test]\(コードとテスト\) の選択を示す画像。":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>カスタム トピックの作成

Event Grid のトピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 左側のナビゲーション メニューで **[すべてのサービス]** を選択します。"**Event Grid**" を検索し、 **[Event Grid トピック]** を選択します。 

    :::image type="content" source="./media/custom-event-to-function/select-event-grid-topics.png" alt-text="[Event Grid トピック] の選択を示す画像。":::
3. **[Event Grid トピック]** ページで、ツール バーの **[+ 追加]** を選択します。 

    :::image type="content" source="./media/custom-event-to-function/add-event-grid-topic-button.png" alt-text="Event Grid トピックを作成するための [作成] ボタンを示す画像。":::
4. **[トピックの作成]** ページで、次の手順に従います。
    1. **Azure サブスクリプション** を選択します。
    2. 前の手順と同じ **リソース グループ** を選択します。
    3. カスタム トピックの一意の **名前** を指定します。 トピック名は、DNS エントリによって表されるため、一意である必要があります。 画像に示されている名前は使用しないでください。 代わりに、必ず 3 - 50 文字以内で、a - z、A - Z、0 - 9、および "-" のみを含む独自の名前を作成します。
    4. イベント グリッド トピックの **場所** を選択します。
    5. **[Review + create]\(レビュー + 作成\)** を選択します。 
    
        :::image type="content" source="./media/custom-event-to-function/create-custom-topic.png" alt-text="[トピックの作成] ページを示す画像。":::      
    1. **[確認および作成]** ページで、設定を確認し、 **[作成]** を選択します。 
5. カスタム トピックが作成されたら、 **[リソースに移動]** リンクを選択して、作成したトピックに対して次の [Event Grid トピック] ページを表示します。 

    :::image type="content" source="./media/custom-event-to-function/event-grid-topic-home-page.png" alt-text="Event Grid カスタム トピックのホーム ページを示す画像。":::

## <a name="subscribe-to-custom-topic"></a>カスタム トピックのサブスクライブ

どのイベントを追跡し、どこにイベントを送信するかは、イベント グリッド トピックをサブスクライブすることによって Event Grid に伝えます。

1. 次に、カスタム トピックの **[Event Grid トピック]** ページで、ツール バーの **+ [イベント サブスクリプション]** を選択します。

    :::image type="content" source="./media/custom-event-to-function/new-event-subscription.png" alt-text="ツール バーでイベント サブスクリプションの追加を選択することを示す画像。":::
2. **[イベント サブスクリプションの作成]** ページで、次の手順に従います。
    1. イベント サブスクリプションの **名前** を入力します。
    3. **[エンドポイントのタイプ]** に **[Azure Function]** を選択します。 
    4. **[エンドポイントの選択]** を選択します。 

        :::image type="content" source="./media/custom-event-to-function/provide-subscription-values.png" alt-text="イベント サブスクリプション値を示す画像。":::
    5. 関数エンドポイントに対し、関数アプリが存在する Azure サブスクリプションとリソース グループを選択して、先ほど作成した関数アプリと関数を選択します。 **[選択の確認]** を選択します。

        :::image type="content" source="./media/custom-event-to-function/provide-endpoint.png" alt-text="[Azure 関数の選択] ページで、前に作成した関数を選択している画像。":::
    6. この手順は省略可能ですが、運用環境のシナリオについては推奨されます。 **[イベント サブスクリプションの作成]** ページで、 **[高度な機能]** タブに切り替え、 **[バッチごとの最大イベント数]** および **[優先バッチ サイズ (KB 単位)]** の値を設定します。 
    
        バッチ処理を使用すると、高スループットが得られます。 **[バッチごとの最大イベント数]** には、サブスクリプションでバッチに含めるイベントの最大数を設定します。 優先バッチ サイズでは、バッチ サイズとして優先される上限をキロバイト単位で設定しますが、1 つのイベントがこのしきい値を超えると、その上限を超えてしまう可能性があります。
    
        :::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="イベント サブスクリプションのバッチ設定を示す画像。":::
    6. **[イベント サブスクリプションの作成]** ページで、 **[作成]** を選択します。

## <a name="send-an-event-to-your-topic"></a>トピックへのイベントの送信

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 Azure CLI または PowerShell を使用して、テスト イベントをカスタム トピックに送信します。 通常はイベント データをアプリケーションまたは Azure サービスから送信することになります。

最初の例では、Azure CLI を使用しています。 この例では、カスタム トピックの URL とキー、およびサンプル イベント データを取得します。 `<topic name>` には、実際のカスタム トピック名を使用してください。 これによりサンプルのイベント データが作成されます。 JSON の `data` 要素がイベントのペイロードです。 このフィールドには、適切な形式の JSON であればどのようなものでも格納することができます。 また、高度なルーティングやフィルタリングを行う場合には、subject フィールドを使用することもできます。 CURL は、HTTP 要求を送信するユーティリティです。


### <a name="azure-cli"></a>Azure CLI
1. Azure portal で、 **[Cloud Shell]** を選択します。 Cloud Shell ウィンドウの左上隅の **[Bash]** を選択します。 

    :::image type="content" source="./media/custom-event-quickstart-portal/cloud-shell-bash.png" alt-text="Cloud Shell - Bash ウィンドウを示す画像":::
1. 次のコマンドを実行して、トピックの **エンドポイント** を取得します。コマンドをコピーして貼り付けた後、コマンドを実行する前に **トピック名** と **リソース グループ名** を更新してください。 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. 次のコマンドを実行して、カスタム トピックの **キー** を取得します。コマンドをコピーして貼り付けた後、コマンドを実行する前に **トピック名** と **リソース グループ** 名を更新してください。 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. イベント定義を含む次のステートメントをコピーし、**Enter** キーを押します。 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. 次の **Curl** コマンドを実行して、イベントを送信します。

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
2 番目の例では、PowerShell を使用して、同様の手順を実行します。

1. Azure portal で **[Cloud Shell]** を選択します (または `https://shell.azure.com/` にアクセスします)。 Cloud Shell ウィンドウの左上隅の **[PowerShell]** を選択します。 「Azure CLI」セクションにあるサンプルの **Cloud Shell** ウィンドウの画像を参照してください。
2. 次の変数を設定します。 コマンドをコピーして貼り付けた後、**トピック名** と **リソース グループ名** を更新してから、次のコマンドを実行します。

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. 次のコマンドを実行して、トピックの **エンドポイント** と **キー** を取得します。

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. イベントを準備します。 ステートメントをコピーし、Cloud Shell ウィンドウ内で実行します。 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. **Invoke-WebRequest** コマンドレットを使用して、イベントを送信します。 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-that-function-received-the-event"></a>関数がイベントを受信したことを確認する
以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 Event Grid によってトリガーされた関数に移動し、ログを開いてください。 ログでは、そのイベントのデータ ペイロードのコピーを確認できます。 ログ ウィンドウを最初に開いておかなかった場合や、[再接続] をクリックした場合は、もう一度テスト イベントを送信してみてください。

:::image type="content" source="./media/custom-event-to-function/successful-function.png" alt-text="Azure 関数の [モニター] ビューにログが表示されている画像。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする
引き続きこのイベントを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 それ以外の場合は、この記事で作成したリソースを削除してください。

1. 左側のメニューの **[リソース グループ]** を選択します。 左側のメニューにこれが表示されていない場合は、左側のメニューの **[すべてのサービス]** を選択し、 **[リソース グループ]** を選択します。 
2. リソース グループを選択して **[リソース グループ]** ページを開きます。 
3. ツール バーの **[リソース グループの削除]** を選択します。 
4. リソース グループの名前を入力して削除を確認し、 **[削除]** を選択します。 

    ![リソース グループ](./media/custom-event-to-function/delete-resource-groups.png)

    画像に表示されている他のリソース グループは、Cloud Shell ウィンドウによって作成および使用されたものです。 後で Cloud Shell ウィンドウを使用しない場合はこれを削除してください。 

## <a name="next-steps"></a>次のステップ

トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Event Grid について](overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)

さまざまなプログラミング言語を使用して Event Grid でイベントを発行および使用する方法については、次のサンプルを参照してください。 

- [.NET 用の Azure Event Grid サンプル](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [Java 用の Azure Event Grid サンプル](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [Python 用の Azure Event Grid サンプル](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [JavaScript 用の Azure Event Grid サンプル](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [TypeScript 用の Azure Event Grid サンプル](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
