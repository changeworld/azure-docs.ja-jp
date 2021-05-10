---
title: Azure Time Series Insights と統合する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins から Azure Time Series Insights へのイベント ルートを設定する方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783775"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure Digital Twins と Azure Time Series Insights を統合する

この記事では、Azure Digital Twins と [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) を統合する方法について説明します。

この記事で説明されているソリューションを使用すると、IoT ソリューションに関する履歴データを収集して分析することができます。 Azure Digital Twins は、Time Series Insights に送信する前に複数のデータ ストリームを関連付け、情報を標準化できるため、Time Series Insights にデータを供給するのに最適です。

## <a name="prerequisites"></a>前提条件

Time Series Insights との関係を設定する前に、次のリソースを設定する必要があります。
* **IoT ハブ**。 手順については、"*IoT Hub のテレメトリの送信*" に関するクイックスタートの「[*IoT Hub の作成*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub)」セクションを参照してください。
* **Azure Digital Twins インスタンス**。
手順については、[*方法: Azure Digital Twins インスタンスと認証の設定*](./how-to-set-up-instance-portal.md)に関するページを参照してください。
* **Azure Digital Twins インスタンス内のモデルとツイン**。
Time Series Insights で追跡されているデータを表示するには、ツインの情報を数回更新する必要があります。 手順については、"*IoT ハブの取り込み方法*" に関する記事の「[*モデルとツインの追加*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin)」セクションを参照してください。

> [!TIP]
> この記事では、簡略化のため、Time Series Insights に表示されるデジタル ツインの変化する値を手動で更新しています。 ただし、シミュレートされたライブ データを使ってこの記事を完了したい場合は、シミュレートされたデバイスからの IoT テレメトリ イベントに基づいてデジタル ツインを更新する Azure 関数を設定できます。 手順については、デバイス シミュレーターを実行し、データ フローが動作することを確認する最終手順を含め、"[*IoT Hub データの取り込み方法*](how-to-ingest-iot-hub-data.md)" に関する記事に従ってください。
>
> 後で、デバイス シミュレーターの実行を開始する場所を示す別のヒントを探して、手動のデジタル ツイン更新コマンドを送信する代わりに、Azure 関数によって自動的にツインが更新されるようにします。


## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

次のパスを使用して、Time Series Insights を Azure Digital Twins に接続します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Time Series Insights が強調表示されている、エンドツーエンド シナリオでの Azure サービスの図。" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>イベント ハブの名前空間の作成

イベント ハブを作成する前に、まず、Azure Digital Twins インスタンスからイベントを受信するイベント ハブ名前空間を作成します。 以下の Azure CLI の手順を使用するか、Azure portal を使用することができます: 「[*クイック スタート:Azure portal を使用したイベント ハブの作成*](../event-hubs/event-hubs-create.md)」。 イベント ハブがサポートされているリージョンを確認するには、"[*リージョン別の利用可能な Azure 製品*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)" に関するページを参照してください。

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> `BadRequest: The specified service namespace is invalid.` というエラーが表示された場合は、名前空間に対して選択した名前が、「[名前空間の作成](/rest/api/servicebus/create-namespace)」というリファレンス ドキュメントで説明されている名前付け要件を満たしていることを確認してください。

この記事に必要な 2 つのイベント ハブを保持するために、このイベント ハブ名前空間を使用します。

  1. **Twins ハブ** - ツインの変更イベントを受け取るイベント ハブ
  2. **Time Series ハブ** - Time Series Insights にイベントをストリーム配信するイベント ハブ

次のセクションでは、イベント ハブ名前空間内にこれらのハブを作成して構成する手順について説明します。

## <a name="create-twins-hub"></a>Twins ハブを作成する

この記事で最初に作成するイベント ハブは、**Twins ハブ** です。 このイベント ハブは、Azure Digital Twins からツイン変更イベントを受け取ります。
Twins ハブを設定するには、このセクションの次の手順を実行します。

1. Twins ハブを作成する
2. ハブへのアクセス許可を制御するための承認規則を作成する
3. 承認規則を使用してハブにアクセスするエンドポイントを Azure Digital Twins に作成する
4. エンドポイントと接続された Twins ハブにツインの更新イベントを送信するルートを Azure Digital Twins に作成する
5. Twins ハブの接続文字列を取得する

次の CLI コマンドを使用して **Twins ハブ** を作成します。 Twins ハブの名前を指定します。

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Twins ハブの承認規則を作成する

送信および受信のアクセス許可を持つ[承認規則](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create)を作成します。 規則の名前を指定します。

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Twins ハブのエンドポイントを作成する

イベント ハブを Azure Digital Twins インスタンスにリンクする Azure Digital Twins [エンドポイント](concepts-route-events.md#create-an-endpoint)を作成します。 Twins ハブのエンドポイントの名前を指定します。

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Twins ハブのイベント ルートを作成する

ツインの状態が更新されるたびに、Azure Digital Twins インスタンスで[ツイン更新イベント](how-to-interpret-event-data.md)を生成できます。 このセクションでは、さらなる処理を行うためにこれらの更新イベントを Twins ハブに送信する Azure Digital Twins の **イベント ルート** を作成します。

ツイン更新イベントを上記からエンドポイントに送信するための[ルート](concepts-route-events.md#create-an-event-route)を Azure Digital Twins に作成します。 このルートのフィルターでは、エンドポイントに渡されるツイン更新メッセージのみが許可されます。 Twins ハブのイベント ルートの名前を指定します。

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Twins ハブの接続文字列を取得する

Twins ハブに対して上で作成した承認規則を使用して、[Twins イベント ハブの接続文字列](../event-hubs/event-hubs-get-connection-string.md)を取得します。

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
この記事の後半で Twins ハブ アプリの設定を構成するために、結果から **primaryConnectionString** 値をメモしておきます。

## <a name="create-time-series-hub"></a>Time Series ハブを作成する

この記事で作成する 2 つ目のイベント ハブは、**Time Series ハブ** です。 これは、Azure Digital Twins のイベントを Time Series Insights にストリーミング配信するイベント ハブです。
Time Series ハブを設定するには、こちらの手順を実行します。

1. Time Series ハブを作成する
2. ハブへのアクセス許可を制御するための承認規則を作成する
3. Time Series ハブの接続文字列を取得する

後で、Time Series Insights インスタンスを作成するときに、この Time Series ハブを Time Series Insights インスタンスのイベント ソースとして接続します。

次のコマンドを使用して、**Time Series ハブ** を作成します。 Time Series ハブの名前を指定します。

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Time Series ハブの承認規則を作成する

送信および受信のアクセス許可を持つ[承認規則](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create)を作成します。 Time Series ハブの承認規則の名前を指定します。

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Time Series ハブの接続文字列を取得する

Time Series ハブに対して上で作成した承認規則を使用して、[Time Series ハブの接続文字列](../event-hubs/event-hubs-get-connection-string.md)を取得します。

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
この記事の後半で Time Series ハブ アプリの設定を構成するために、結果から **primaryConnectionString** 値をメモしておきます。

また、後で Time Series Insights インスタンスの作成で使用するために、次の値をメモしておきます。
* イベント ハブの名前空間
* Time Series ハブ名
* Time Series ハブの承認規則

## <a name="create-a-function"></a>関数を作成する

このセクションでは、ツイン更新イベントを、元の形式の JSON Patch ドキュメントから、Twins からの更新および追加された値だけが含まれる JSON オブジェクトに変換する Azure 関数を作成します。

### <a name="step-1-create-function-app"></a>手順 1: 関数アプリを作成する

最初に、Visual Studio で新しい関数アプリ プロジェクトを作成します。 それを行う方法については、"*データを処理するために関数を設定する方法*" に関する記事の「[**Visual Studio で関数アプリを作成する**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio)」セクションを参照してください。

### <a name="step-2-add-a-new-function"></a>手順 2: 新しい関数を追加する

Time Series Insights へのデバイス テレメトリ イベントを更新するために、*ProcessDTUpdatetoTSI.cs* という名前の新しい Azure 関数を作成します。 関数の種類は、**イベント ハブ トリガー** になります。

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="イベント ハブ トリガーの種類の新しい Azure 関数を作成する Visual Studio のスクリーンショット。":::

### <a name="step-3-fill-in-function-code"></a>手順 3: 関数コードを入力する

次のパッケージをご利用のプロジェクトに追加します。
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

*ProcessDTUpdatetoTSI.cs* ファイルのコードを次のコードに置き換えます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

関数コードを保存します。

### <a name="step-4-publish-the-function-app-to-azure"></a>手順 4: 関数アプリを Azure に発行する

*ProcessDTUpdatetoTSI.cs* 関数を使用して、プロジェクトを Azure の関数アプリに発行します。

それを行う方法については、「*データを処理するために関数を設定する方法*」に関する記事の「[**関数アプリを Azure に発行する**](how-to-create-azure-function.md#publish-the-function-app-to-azure)」セクションを参照してください。

後で 2 つのイベント ハブ用にアプリ設定を構成する際に使用するために、関数アプリ名を保存します。

### <a name="step-5-security-access-for-the-function-app"></a>手順 5: 関数アプリのセキュリティ アクセス

次に、Azure Digital Twins インスタンスにアクセスできるように、関数に **アクセス ロールを割り当て** て、**アプリケーション設定を構成** します。 それを行う方法については、"*データを処理するために関数を設定する方法*" に関する記事の「[**関数アプリのセキュリティ アクセスを設定する**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app)」セクションを参照してください。

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>手順 6: 2 つのイベント ハブ用にアプリ設定を構成する

次に、関数アプリの設定に環境変数を追加します。これにより、Twins ハブと Time Series ハブにアクセスできるようになります。

前に保存した Twins ハブの **primaryConnectionString** 値を使用して、Twins ハブの接続文字列が含まれたアプリ設定を関数アプリに作成します。

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

前に保存した Time Series ハブの **primaryConnectionString** 値を使用して、Time Series ハブの接続文字列が含まれたアプリ設定を関数アプリに作成します。

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Azure Time Series Insights インスタンスを作成して接続する

このセクションでは、Time Series ハブからデータを受信するように Time Series Insights インスタンスを設定します。 このプロセスの詳細については、"[*チュートリアル: Azure Time Series Insights Gen2 PAYG 環境の設定*](../time-series-insights/tutorial-set-up-environment.md)" に関するページを参照してください。 Time Series Insights 環境を作成するには、下の手順に従います。

1. [Azure portal](https://portal.azure.com) で "*Time Series Insights の環境*"を検索し、 **[追加]** ボタンを選択します。 Time Series 環境を作成するには、次のオプションを選択します。

    * **サブスクリプション** - サブスクリプションを選択します。
        - **リソース グループ** - リソース グループを選択します。
    * **環境名** - Time Series 環境の名前を指定します。
    * **場所** - 場所を選択します。
    * **レベル** - **Gen2(L1)** の価格レベルを選択します。
    * **プロパティ名** - 「 **$dtId**」と入力します (ID 値の選択の詳細については、「[*タイム シリーズ ID の選択に関するベスト プラクティス*](../time-series-insights/how-to-select-tsid.md)」をお読みください)。
    * **ストレージ アカウント** - ストレージ アカウント名を指定します。
    * **ウォーム ストアを有効にする** - このフィールドは、 *[はい]* の設定のままにします。

    このページの他のプロパティは、既定値のまま使用できます。 **[Next : Event Source >]\(次へ: イベント ソース >\)** ボタンを選択します。

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Time Series Insights 環境を作成する Azure portal のスクリーンショット。サブスクリプション、リソース グループ、場所をそれぞれのドロップダウンから選択し、環境の名前を選択します。" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Time Series Insights 環境を作成する Azure portal のスクリーンショット。Gen2(L1) 価格レベルが選択されており、Time Series ID プロパティの名前は $dtId です。" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. *[イベント ソース]* タブで、次のフィールドを選択します。

   * **Create an Event Source? \(イベント ソースを作成しますか\)** - *[はい]* を選択します。
   * **ソースの種類** - *[イベント ハブ]* を選択します。
   * **名前** - イベント ソースの名前を指定します。
   * **サブスクリプション** - Azure サブスクリプションを選択します。
   * **イベント ハブ名前空間** - この記事の前半で作成した名前空間を選択します。
   * **イベント ハブ名** - この記事の前半で作成した **Time Series ハブ** 名を選択します。
   * **イベント ハブのアクセス ポリシー名** - この記事の前半で作成した "*Time Series ハブの承認規則*" を選択します。
   * **イベント ハブ コンシューマー グループ** - *[新規作成]* を選択し、イベント ハブ コンシューマー グループの名前を指定します。 その後、 *[追加]* を選択します。
   * **プロパティ名** - このフィールドは空白のままにします。
    
    **[確認と作成]** ボタンを選択して、すべての詳細を確認します。 次に、 **[確認と作成]** ボタンをもう一度選択して、Time Series 環境を作成します。

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Time Series Insights 環境を作成する Azure portal のスクリーンショット。上記のイベント ハブ情報を使用してイベント ソースを作成しています。また、新しいコンシューマー グループも作成しています。" lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>IoT データを Azure Digital Twins に送信する

Time Series Insights へのデータの送信を開始するには、変化するデータ値を使用した Azure Digital Twins のデジタル ツイン プロパティの更新を開始する必要があります。

次の CLI コマンドを使用して、「[前提条件](#prerequisites)」セクションでインスタンスに追加した *Thermostat67* ツインの *Temperature* プロパティを更新します。

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**異なる Temperature 値を使用して少なくともさらに 4 回コマンドを繰り返し**、後で Time Series Insights 環境で観察できるいくつかのデータ ポイントを作成します。

> [!TIP]
> 手動でデジタル ツインの値を更新するのではなく、シミュレートされたライブ データを使用してこの記事を完了したい場合は、まず、「[*前提条件*](#prerequisites)」セクションのヒントを完了して、シミュレートされたデバイスからツインを更新する Azure 関数を設定してください。
その後、デバイスをすぐに実行して、シミュレートされたデータの送信と、そのデータ フローを使用したデジタル ツインの更新を開始できます。

## <a name="visualize-your-data-in-time-series-insights"></a>Time Series Insights でデータを視覚化する

現在は、データが Time Series Insights インスタンスに流れているはずであり、分析する準備ができました。 次の手順のようにして、受信したデータを調べます。

1. [Azure portal](https://portal.azure.com) で、前に作成した Time Series 環境名を検索します。 左側のメニュー オプションから *[概要]* を選択して、*Time Series Insights Explorer URL* を表示します。 URL を選択すると、Time Series Insights 環境に反映された気温の変化が表示されます。

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Time Series Insights 環境の [概要] タブで Time Series Insights Explorer の URL を選択する Azure portal のスクリーンショット。" lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. エクスプローラーで、左側に Azure Digital Twins インスタンス内のツインが表示されます。 *Thermostat67* ツインを選択し、*Temperature* プロパティを選択して、 **[追加]** を押します。

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Thermostat67 を選択する Time Series Insights エクスプローラーのスクリーンショット。Temperature プロパティを選択し、[追加] を押します。" lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. これで、下に示すように、サーモスタットからの初期温度読み取りが表示されます。 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="初期温度データを表示する TSI エクスプローラーのスクリーンショット。68 から 85 までのランダムな値です。" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

シミュレーションをさらに長時間実行させておくと、表示は次のようになります。

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="各ツインの温度データが異なる色の 3 本の平行線でグラフ化されている TSI エクスプローラーのスクリーンショット。" lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>次のステップ

デジタル ツインは、Time Series Insights にフラット階層として既定で格納されますが、モデル情報と組織の複数レベルの階層を使用して強化することができます。 このプロセスの詳細については、以下を参照してください。 

* [*チュートリアル:モデルを定義して適用する*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Azure Digital Twins に既に格納されているモデルとグラフ データを使用して、この情報を自動的に提供するカスタム ロジックを作成できます。 ツイン グラフの情報の管理、アップグレード、および取得について詳しくは、以下の参照情報をご覧ください。

* [*方法: デジタル ツインを管理する*](./how-to-manage-twin.md)
* [*方法: ツイン グラフにクエリを実行する*](./how-to-query-graph.md)