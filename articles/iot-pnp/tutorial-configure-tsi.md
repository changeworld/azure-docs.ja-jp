---
title: Time Series Insights を使用して Azure IoT プラグ アンド プレイ デバイス テレメトリを格納して分析する | Microsoft Docs
description: Time Series Insights 環境を設定し、IoT ハブを接続して、IoT プラグ アンド プレイ デバイスからのテレメトリを表示、分析します。
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422265"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>チュートリアル:Time Series Insights Gen2 を作成し、それに接続することで IoT プラグ アンド プレイのデバイス テレメトリを格納、視覚化、分析する

このチュートリアルでは、[Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) 環境を作成し、IoT プラグ アンド プレイ ソリューションと連携するよう正しく構成する方法について説明します。 TSI を使用すると、モノのインターネット (IoT) 規模で時系列データを収集、処理、格納、照会、視覚化できます。

まず、TSI 環境をプロビジョニングし、ストリーミング イベント ソースとして IoT ハブを接続します。 次に、モデルの同期を行い、温度コントローラーおよびサーモスタット デバイスに使用した [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) のサンプル モデル ファイルに基づいて、TSI 環境のタイム シリーズ モデルを作成します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Azure CLI をローカルにインストールする必要がないようにするために、Azure Cloud Shell を使用してクラウド サービスを設定できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>時系列 ID の選択

TSI 環境をプロビジョニングする間に、時系列 ID を選択する必要があります。 適切な時系列 ID を選択することがきわめて重要となります。このプロパティは変更不可であり、設定した後に変更することはできません。 タイム シリーズ ID の選択は、データベースでのパーティション キーの選択のようなものです。 通常、TS ID は、資産モデルのリーフ ノードである必要があります。 つまり、テレメトリを出力する最も粒度の細かい資産 (またはセンサー) の ID プロパティを選択するのが一般的です。

IoT プラグ アンド プレイ ユーザーとして、TS ID の選択には、実際のデバイス モデルに[コンポーネント](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component)が存在するかどうかが関係します。 

![TS ID の選択](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* 既にクイックスタートに取り組んでいて、IoT Hub デバイスが[サーモスタット](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)を表している場合は、TS ID として `iot-hub-connection-device-id` を使用します。

* マルチ コンポーネント [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) のいずれかのチュートリアルに取り組んでいる場合は、以下のセクションで複合キー (`iot-hub-connection-device-id, dt-subject`) を使用します。

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 環境をプロビジョニングする

以下のコマンドは、次の処理を実行します。

* ご利用の環境の[コールド ストア](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store)用の Azure ストレージ アカウントを作成します。これは、履歴データの長期保有と分析を意図したものです。
  * `mytsicoldstore` は、実際のアカウントの一意の名前に置き換えます。
* 保持期間が 7 日間のウォーム ストレージと無期限のコールド ストアを含んだ Azure Time Series Insights Gen2 環境を作成します。 
  * `my-tsi-env` は、実際の TSI 環境の一意の名前に置き換えます 
  * `my-pnp-resourcegroup` は、設定時に使用したリソース グループの名前に置き換えます
  * `my-ts-id-property` は、上記の選択条件に基づいて、実際の TS ID プロパティの値に置き換えます

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

今度は、先ほどご利用の環境の[イベント ソース](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)として作成した IoT ハブを構成します。 イベント ソースが接続されると、ハブから送信されたイベントのインデックス作成が TSI によって開始されます。インデックス作成は、キュー内の最も古いイベントから順に実行されます。

まず、TSI 環境の IoT ハブに一意のコンシューマー グループを作成します。 `my-pnp-hub` は、先ほど使用した IoT ハブの名前に置き換えてください。

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

IoT ハブを接続します。 `my-pnp-resourcegroup`、`my-pnp-hub`、`my-tsi-env` は、それぞれ実際の値に置き換えてください。

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
[Azure portal](https://portal.azure.com) で目的のリソース グループに移動し、新しく作成した Time Series Insights 環境を選択します。 インスタンスの概要で示されている *Time Series Insights Explorer URL* にアクセスします。

![ポータルの概要ページ](./media/tutorial-configure-tsi/view-environment.png)

エクスプローラーで、[すべての階層] にサーモスタットが 2 つ表示されます。 次に、実際のデバイス モデルに基づいてタイム シリーズ モデルをキュレーションします。

![エクスプローラー ビュー 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Digital Twins Definition Language と Time Series Insights Gen2 の間でモデルを同期する

次に、DTDL デバイス モデルを Azure Time Series Insights (TSI) の資産モデルに変換します。 TSI のタイム シリーズ モデルは、TSI 内でのデータのコンテキスト化のためのセマンティック モデリング ツールです。 タイム シリーズ モデルには、次の 3 つの主要なコンポーネントがあります。

* [タイム シリーズ モデルのインスタンス](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances)。 インスタンスは、時系列自体の仮想表現です。 インスタンスは、TS ID によって一意に識別されます。
* [タイム シリーズ モデルの階層](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies)。 階層を使用すると、プロパティ名とそのリレーションシップを指定して、インスタンスを整理できます。
* [タイム シリーズ モデルの型](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types)。 型は、計算を行うための[変数](https://docs.microsoft.com/azure/time-series-insights/concepts-variables)や数式を定義するのに役立ちます。 型は特定のインスタンスに関連付けられています。

> [!NOTE]
> 以下に示したのは、マルチ コンポーネントの TemperatureController の例です。

### <a name="define-your-types"></a>型を定義する

Azure Time Series Insights Gen2 へのデータの取り込みは、モデルを事前に定義していなくても開始できます。 テレメトリが到着すると、TSI は、TS ID プロパティの値に基づいて時系列インスタンスの自動解決を試みます。 すべてのインスタンスには、"*既定の型*" が割り当てられます。 ご利用のモデルを表す新しい型を手動で作成する必要があります。 以下の画像は、DTDL のモデルと TSM の型を同期するための簡単な方法を表したものです。

![DTDL から TSM の型へ](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* DTMI (Digital Twin Model Identifier) は型の ID になります。
* 型の名前には、モデルの名前または表示名を指定できます。
* モデルの説明は、型の説明になります。
* 数値のスキーマを備えたテレメトリ コンポーネントごとに、型変数が少なくとも 1 つ作成されます。 
  * 変数に使用できるのは数値データ型だけですが、解析可能な文字列として値が送信される場合 (`"0"` など)、`toDouble` などの[変換](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions)関数を使用することができます。
* 変数の名前には、テレメトリの名前または表示名を指定できます。
* TSX (Time Series Expression) 変数を定義する際は、送信中のテレメトリの名前とそのデータ型を参照します。

> [!NOTE]
> この例に示されている変数は 2 つ (集計と数値) だけですが、定義できる変数は、1 つの型につき最大 100 個です。 それぞれの変数で同じテレメトリ値を参照して、必要に応じてさまざまな計算を実行することができます。 フィルター、集計、スカラー関数の全一覧については、[Time Series Insights Gen2 Time Series 式の構文](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)に関するドキュメントを参照してください。

任意のテキスト エディターを開き、以下の JSON をローカル ドライブに保存します。

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Time Series Insights Explorer で、左側のモデル アイコンをクリックして [モデル] タブに移動します。 **[Types]\(型\)** をクリックし、 **[JSON をアップロードします]** をクリックします。

![アップロード](./media/tutorial-configure-tsi/upload-type.png)

**[ファイルの選択]** を選択し、先ほど保存した JSON を選択して、 **[アップロード]** をクリックします。

新しく定義した Thermostat 型が表示されます。

### <a name="optional---create-a-hierarchy"></a>省略可 - 階層を作成する

必要に応じて、2 つのサーモスタット コンポーネントを整理するための階層を、親である TemeraptureController の下に作成することができます。

*[階層]* をクリックし、 *[階層の追加]* を選択します。 名前として「`Device Fleet`」を入力し、`Device Name` というレベルを 1 つ作成して、 *[保存]* をクリックします。

![階層を追加する](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>インスタンスを正しい型に割り当てる

次に、インスタンスの型を変更し、必要に応じて、それらのインスタンスを階層内に位置付けます。

*[インスタンス]* タブを選択し、右端の *[編集]* アイコンをクリックします。

![インスタンスを編集する](./media/tutorial-configure-tsi/edit-instance.png)

[型] ドロップダウンをクリックし、[`Thermostat`] を選択します。 

![インスタンスの型を変更する](./media/tutorial-configure-tsi/change-type.png)

階層を作成した場合は、 *[インスタンス フィールド]* を選択し、[`Device Fleet`] チェック ボックスをオンにします。 親デバイスの値として「`Temperature Controller`」を入力し、 *[保存]* をクリックします。

![階層に割り当てる](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

2 つ目の Thermostat にも上記の手順を繰り返します。

### <a name="view-your-data"></a>データの表示

グラフ ペインに戻り、[Device Fleet] と [TemperatureController] を展開します。 thermostat1 をクリックして `Temperature` 変数を選択し、 *[追加]* をクリックして値をグラフ化します。 thermostat2 についても同じ作業を行います。

![thermostat2 のインスタンスの型を変更する](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>次の手順

* 間隔のサイズや Y 軸コントロールなど、さまざまなグラフ作成オプションの詳細については、「[Azure Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels)」ドキュメントを参照してください。

* 実際の環境のタイム シリーズ モデルの詳細な概要については、[こちら](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview)の記事を参照してください。

* クエリ API と Time Series Expression の構文の詳細については、[こちらを選択](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)してください。




