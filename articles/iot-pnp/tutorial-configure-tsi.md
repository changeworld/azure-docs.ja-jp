---
title: Time Series Insights を使用して Azure IoT プラグ アンド プレイ デバイス テレメトリを格納して分析する | Microsoft Docs
description: Time Series Insights 環境を設定し、IoT ハブを接続して、IoT プラグ アンド プレイ デバイスからのテレメトリを表示、分析します。
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ad5c6f205fc832eb125e52b4135990fc58742e62
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453235"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>プレビュー チュートリアル: Time Series Insights Gen2 を作成し、それに接続することで IoT プラグ アンド プレイのデバイス テレメトリを格納、視覚化、分析する

このチュートリアルでは、[Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) (TSI) 環境を作成し、IoT プラグ アンド プレイ ソリューションと連携するよう正しく構成する方法について説明します。 TSI を使用すると、モノのインターネット (IoT) 規模で時系列データを収集、処理、格納、照会、視覚化できます。

まず、TSI 環境をプロビジョニングし、ストリーミング イベント ソースとして IoT ハブを接続します。 次に、モデルの同期を行い、温度コントローラーおよびサーモスタット デバイスに使用した [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) のサンプル モデル ファイルに基づいて、[時系列モデル](../time-series-insights/concepts-model-overview.md)を作成します。

> [!NOTE]
> この統合はプレビュー段階です。 DTDL デバイス モデルを時系列モデルにマップする方法は変更される可能性があります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

この時点で、次を用意します。

* Azure IoT Hub。
* IoT プラグ アンド プレイ デバイスの個別のデバイス登録が作成され、IoT ハブにリンクされている DPS インスタンス。
* シミュレートされたデータをストリーム配信する、単一またはマルチコンポーネント デバイスから IoT ハブへの接続。

Azure CLI をローカルにインストールする必要がないようにするために、Azure Cloud Shell を使用してクラウド サービスを設定できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>イベント ソースの準備

先ほど作成した IoT ハブが TSI 環境の[イベント ソース](../time-series-insights/concepts-streaming-ingestion-event-sources.md)になります。

> [!IMPORTANT]
> 既存の IoT Hub ルートをすべて無効にします。 [ルーティング](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints)が構成された TSI イベント ソースとして IoT ハブを使用する場合は、既知の問題があります。 すべてのルーティング エンドポイントを一時的に無効にし、IoT ハブが TSI に接続されたら、再度有効にできます。

まず、使用する TSI の IoT ハブに一意のコンシューマー グループを作成します。 `my-pnp-hub` は、先ほど使用した IoT ハブの名前に置き換えます。

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>時系列 ID の選択

TSI 環境をプロビジョニングするときに、"*時系列 ID*" を選択する必要があります。 適切な時系列 ID を選択することが重要です。このプロパティは変更不可であり、設定した後に変更することはできないからです。 時系列 ID は、データベースのパーティション キーのようなものです。 時系列 ID は、時系列モデルの主キーとして機能します。 詳しくは、「[時系列 ID の選択に関するベスト プラクティス](../time-series-insights/how-to-select-tsid.md)」をご覧ください。

IoT プラグ アンド プレイ ユーザーとして、`iothub-connection-device-id` と `dt-subject` で構成される "_複合キー_" を時系列 ID として指定します。 IoT Hub によって、それぞれ IoT プラグ アンド プレイ デバイス ID とデバイス コンポーネント名を含むこれらのシステム プロパティが追加されます。

IoT プラグ アンド プレイ デバイス モデルで現在コンポーネントを使用していない場合でも、後で使用できるように、複合キーの一部として `dt-subject` を含めておいた方がよいです。 時系列 ID は変更不可能であるため、後で必要になる場合に備えて、このオプションを有効にすることをお勧めします。

> [!NOTE]
> 次の例は、マルチコンポーネント **TemperatureController** デバイスを対象としていますが、その概念はコンポーネントなしの **Thermostat** デバイスでも同じです。

## <a name="provision-your-tsi-environment"></a>TSI 環境のプロビジョニング

このセクションでは、Azure Time Series Insights Gen2 環境をプロビジョニングする方法について説明します。

次のコマンドで、以下を行います。

* ご利用の環境の[コールド ストア](../time-series-insights/concepts-storage.md#cold-store)用の Azure ストレージ アカウントを作成します。これは、履歴データの長期保有と分析を意図したものです。
  * `mytsicoldstore` は、実際のコールド ストレージ アカウントの一意の名前に置き換えます。
* 保持期間が 7 日間のウォーム ストレージと無期限のコールド ストレージを含んだ Azure Time Series Insights Gen2 環境を作成します。
  * `my-tsi-env` は、実際の TSI 環境の一意の名前に置き換えます。
  * `my-pnp-resourcegroup` は、設定時に使用したリソース グループの名前に置き換えます。
  * `iothub-connection-device-id, dt-subject` は時系列 ID プロパティです。

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

IoT Hub イベント ソースを接続します。 `my-pnp-resourcegroup`、`my-pnp-hub`、`my-tsi-env` は、選択した値に置き換えます。 次のコマンドでは、前に作成した TSI のコンシューマー グループを参照します。

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

[Azure portal](https://portal.azure.com) 内で目的のリソース グループに移動し、新しい Time Series Insights 環境を選択します。 インスタンスの概要に示されている *Time Series Insights Explorer URL* にアクセスします。

![ポータルの概要ページ](./media/tutorial-configure-tsi/view-environment.png)

エクスプローラーには、3 つのインスタンスが表示されます。

* &lt;実際の pnp デバイス ID&gt;、thermostat1
* &lt;実際の pnp デバイス ID&gt;、thermostat2
* &lt;実際の pnp デバイス ID&gt;、`null`

> [!NOTE]
> 3 番目のタグは、デバイス メモリのワーキング セットなど、**TemperatureController** 自体からのテレメトリを表します。 これは最上位レベルのプロパティであるため、コンポーネント名の値は null になります。 後の手順で、これをもっとわかりやすい名前に更新します。

![エクスプローラー ビュー 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>モデル変換の構成

次に、DTDL デバイス モデルを Azure Time Series Insights (TSI) の資産モデルに変換します。 TSI のタイム シリーズ モデルは、TSI 内でのデータのコンテキスト化のためのセマンティック モデリング ツールです。 タイム シリーズ モデルには、次の 3 つの主要なコンポーネントがあります。

* [タイム シリーズ モデルのインスタンス](../time-series-insights/concepts-model-overview.md#time-series-model-instances)。 インスタンスは、時系列自体の仮想表現です。 インスタンスは、時系列 ID によって一意に識別されます。
* [タイム シリーズ モデルの階層](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies)。 階層を使用すると、プロパティ名とそのリレーションシップを指定して、インスタンスを整理できます。
* [タイム シリーズ モデルの型](../time-series-insights/concepts-model-overview.md#time-series-model-types)。 型は、計算を行うための[変数](../time-series-insights/concepts-variables.md)や数式を定義するのに役立ちます。 型は特定のインスタンスに関連付けられています。

### <a name="define-your-types"></a>型の定義

Azure Time Series Insights Gen2 へのデータの取り込みは、モデルを事前に定義していなくても開始できます。 テレメトリが到着すると、TSI は、時系列 ID プロパティの値に基づいて時系列インスタンスの自動解決を試みます。 すべてのインスタンスには、"*既定の型*" が割り当てられます。 ご利用のインスタンスを正しく分類するために新しい型を手動で作成する必要があります。 次の詳細は、デバイスの DTDL モデルを時系列モデルの型と同期させる最も簡単な方法を示しています。

* デジタル ツイン モデル識別子は型の ID になります。
* 型の名前には、モデルの名前または表示名を指定できます。
* モデルの説明は、型の説明になります。
* 数値のスキーマを備えたテレメトリごとに、型変数が少なくとも 1 つ作成されます。
  * 変数に使用できるのは数値データ型だけですが、変換可能な別の型として値が送信される場合 (`"0"` など)、`toDouble` などの[変換](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions)関数を使用することができます。
* 変数の名前には、テレメトリの名前または表示名を指定できます。
* Time Series Expression 変数を定義する際は、送信中のテレメトリの名前とそのデータ型を参照します。

| DTDL の JSON | 時系列モデルの型の JSON | 値の例 |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (配列)| `variables` (オブジェクト)  | 以下の例を参照

![DTDL から時系列モデルの型](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> この例に示されている変数は 3 つですが、1 つの型につき最大 100 個定義できます。 それぞれの変数で同じテレメトリ値を参照して、必要に応じてさまざまな計算を実行することができます。 フィルター、集計、スカラー関数の全一覧については、[Time Series Insights Gen2 Time Series 式の構文](/rest/api/time-series-insights/reference-time-series-expression-syntax.md)に関するページを参照してください。

テキスト エディターを開き、次の JSON をローカル ドライブに保存します。

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

Time Series Insights Explorer で、左側のモデル アイコンを選択して **[モデル]** タブに移動します。 **[型]** を選択し、 **[JSON をアップロードします]** を選択します。

![アップロード](./media/tutorial-configure-tsi/upload-type.png)

**[ファイルの選択]** を選択し、先ほど保存した JSON を選択して、 **[アップロード]** を選択します。

新しく定義された **Temperature Controller** 型が表示されます。

### <a name="create-a-hierarchy"></a>階層の作成

タグを整理するための階層を、親である **TemperatureController** の下に作成します。 次の単純な例はレベルが 1 つですが、IoT ソリューションでは一般的に、組織内の物理的およびセマンティックな位置内でタグをコンテキスト化するために、入れ子のレベルがたくさんあります。

**[階層]** を選択し、 **[階層の追加]** を選択します。 名前として「*Device Fleet*」と入力し、"*Device Name*" というレベルを 1 つ作成します。 その後、 **[保存]** を選びます。

![階層を追加する](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>インスタンスを正しい型に割り当てる

次に、インスタンスの型を変更し、それらを階層内に配置します。

**[インスタンス]** タブを選択し、デバイスのワーキング セットを表すインスタンスを見つけて、右端にある **[編集]** アイコンを選択します。

![インスタンスを編集する](./media/tutorial-configure-tsi/edit-instance.png)

**[型]** ドロップダウンを選択し、 **[Temperature Controller]** を選択します。 「*defaultComponent, <your device name>* 」と入力して、デバイスに関連付けられているすべての最上位レベルのタグを表すインスタンスの名前を更新します。

![インスタンスの型を変更する](./media/tutorial-configure-tsi/change-type.png)

[保存] を選択する前に、 **[インスタンスのフィールド]** タブを選択し、 **[Device Fleet]** チェック ボックスをオンにします。 「`<your device name> - Temp Controller`」と入力して、テレメトリをグループ化し、 **[保存]** を選択します。

![階層に割り当てる](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

前の手順を繰り返して、サーモスタットのタグに正しい型と階層を割り当てます。

## <a name="view-your-data"></a>データの表示

グラフ ペインに戻り、 **[Device Fleet]、実際のデバイス** の順に展開します。 **thermostat1** を選択して **Temperature** 変数を選択し、 **[追加]** を選択して値をグラフ化します。 **thermostat2** と **defaultComponent** **workingSet** 値に対して同じことを行います。

![thermostat2 のインスタンスの型を変更する](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>次の手順

* 間隔のサイズ設定や Y 軸コントロールなど、さまざまなグラフ作成オプションの詳細については、「[Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md)」を参照してください。

* 実際の環境の時系列モデルの詳細な概要については、「[Azure Time Series Insights Gen2 の時系列モデル](../time-series-insights/concepts-model-overview.md)」の記事を参照してください。

* クエリ API と Time Series Expression の構文の詳細については、[Azure Time Series Insights Gen2 クエリ API](/rest/api/time-series-insights/reference-query-apis.md) に関するページを参照してください。
