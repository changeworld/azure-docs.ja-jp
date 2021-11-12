---
title: IoT Hub からテレメトリを取り込む
titleSuffix: Azure Digital Twins
description: デバイス テレメトリ メッセージを Azure IoT Hub から Azure Digital Twins のインスタンスのデジタル ツインに取り込む方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/28/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1b10a87312fd7b27c22bbfa56a928a0357d4698e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500941"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Azure Digital Twins に IoT Hub テレメトリを取り込む

このガイドでは、IoT Hub からテレメトリを取り込み、それを Azure Digital Twins のインスタンスに送信できる関数を記述するプロセスについて説明します。

Azure Digital Twins は、IoT デバイスやその他のソースからのデータに基づいています。 Azure Digital Twins で使用するデバイス データの一般的なソースは [IoT Hub](../iot-hub/about-iot-hub.md) です。

Azure Digital Twins にデータを取り込むプロセスは、[Azure Functions](../azure-functions/functions-overview.md) を使って作られた関数のような外部のコンピューティング リソースを設定することです。 この関数はデータを受け取り、必要に応じて [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) を使用してプロパティを設定したり、[デジタル ツイン](concepts-twins-graph.md)でテレメトリ イベントを発生させたりします。 

このハウツー ドキュメントでは、IoT Hub からテレメトリを取り込むことができる関数を作成するプロセスについて手順を追って説明します。

## <a name="prerequisites"></a>前提条件

この例を続行する前に、前提条件として次のリソースを設定する必要があります。
* **IoT ハブ** 手順については、この [ IoT Hub のクイック スタート](../iot-hub/quickstart-send-telemetry-cli.md)の「*IoT Hub の作成*」のセクションを参照してください。
* デバイス テレメトリを受信する **Azure Digital Twins インスタンス**。 手順については、[Azure Digital Twins インスタンスと認証の設定](./how-to-set-up-instance-portal.md)に関するページを参照してください。

この記事では、**Visual Studio** も使用します。 最新バージョンは、[Visual Studio のダウンロード](https://visualstudio.microsoft.com/downloads/) ページからダウンロードできます。

## <a name="example-telemetry-scenario"></a>テレメトリのシナリオ例

このハウツーでは、Azure の関数を使用して、IoT Hub から Azure Digital Twins にメッセージを送信する方法の概要について説明します。 メッセージの送信に使用できる構成とマッチング戦略は多数ありますが、この記事の例には次のパーツが含まれています。
* 既知のデバイス ID を持つ IoT Hub のサーモスタット デバイス
* ID が一致するデバイスを表すデジタル ツイン

> [!NOTE]
> この例では、デバイス ID と対応するデジタル ツインの ID の間の単純な ID 一致を使用していますが、(マッピング テーブルなどを使用して) デバイスからツインへのより高度なマッピングを用意することもできます。

サーモスタット デバイスから温度テレメトリ イベントが送信されるたびに、関数によってそのテレメトリが処理され、デジタル ツインの *temperature* プロパティが更新されます。 このシナリオの概要を次の図に示します。

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="温度テレメトリを Azure の関数に送信する IoT Hub デバイスの図。これにより、Azure Digital Twins 内にあるツインの temperature プロパティが更新されます。" border="false":::

## <a name="add-a-model-and-twin"></a>モデルとツインの追加

このセクションでは、Azure Digital Twins 内で[デジタル ツイン](concepts-twins-graph.md)を設定します。これは、サーモスタット デバイスを表し、IoT Hub からの情報で更新されます。

サーモスタット型ツインを作成するには、まず、サーモスタット [モデル](concepts-models.md) をご利用のインスタンスにアップロードする必要があります。これは、サーモスタットのプロパティを記述したもので、後でツインを作成するために使用されます。

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

次に、**このモデルを使用して 1 つのツインを作成** します。 次のコマンドを使用して thermostat67 という名前のサーモスタット ツインを作成して、初期温度値を 0.0 に設定します。

```azurecli-interactive
az dt twin create  --dt-name <instance-name> --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}'
```

ツインが正常に作成されると、コマンドからの CLI 出力は次のようになります。
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "lastUpdateTime": "2021-09-09T20:32:46.6692326Z"
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>関数を作成する

このセクションでは、Azure Digital Twins にアクセスし、受信した IoT テレメトリ イベントに基づいてツインを更新するという Azure 関数を作成します。 関数を作成して発行するには、次の手順に従います。

1. 最初に、Visual Studio で新しい関数アプリ プロジェクトを作成します。 これを行う方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project)」を参照してください。

2. 次のパッケージをご利用のプロジェクトに追加します。
    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

3. Visual Studio で生成したサンプル関数 *Function1.cs* の名前を *IoTHubtoTwins.cs* に変更します。 ファイル内のコードを次のコードに置き換えます。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

    関数コードを保存します。

4. *IoTHubtoTwins.cs* 関数を使用して、プロジェクトを Azure の関数アプリに発行します。 これを行う方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#publish-to-azure)」を参照してください。

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

Azure Digital Twins にアクセスするために、関数アプリには、Azure Digital Twins インスタンスにアクセスできるアクセス許可を持つシステム マネージド ID が必要です。 次はその設定をします。

### <a name="configure-the-function-app"></a>Function App を構成する

次に、Azure Digital Twins インスタンスにアクセスできるように、関数に **アクセス ロールを割り当て** て、**アプリケーション設定を構成** します。

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="connect-your-function-to-iot-hub"></a>関数の IoT Hub への接続

このセクションでは、IoT Hub デバイス データのイベントの宛先として関数を設定します。 この方法で関数を設定すると、IoT Hub のサーモスタット デバイスからのデータが処理のために Azure 関数に送信されます。

[Azure portal](https://portal.azure.com/) で、「[前提条件](#prerequisites)」セクションで作成した IoT Hub インスタンスに移動します。 **[イベント]** で、関数のサブスクリプションを作成します。

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="イベント サブスクリプションの追加を示す Azure portal のスクリーンショット。":::

**[イベント サブスクリプションの作成]** ページで、各フィールドに次のように入力します。
  1. **[名前]** で、イベント サブスクリプションに付ける任意の名前を選択します。
  2. **[イベント スキーマ]** では、 _[Event Grid スキーマ]_ を選択します。
  3. **[システム トピック名]** では、任意の名前を選択します。
  1. **[イベントの種類]** では、 _[デバイス テレメトリ]_ チェックボックスをオンにし、その他のイベントの種類をオフにします。
  1. **[エンドポイントのタイプ]** では、 _[Azure 関数]_ を選択します。
  1. **[エンド ポイント]** では、 _[エンドポイントの選択]_ リンクを使用して、エンドポイントに使用する Azure 関数を選択します。
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="イベント サブスクリプションの詳細を作成する Azure portal のスクリーンショット。":::

_[Azure 関数の選択]_ ページが開いたら、次の詳細を確認または入力します。
 1. **サブスクリプション**:Azure サブスクリプション。
 2. **[リソース グループ]** :ご利用のリソース グループ。
 3. **関数アプリ**:ご利用の関数アプリの名前。
 4. **スロット**: "_運用_"。
 5. **関数**: ドロップダウンから前述の関数 *IoTHubtoTwins* を選択します。

_[選択の確認]_ ボタンを使用して詳細を保存します。            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="関数を選択する Azure portal のスクリーンショット。":::

_[作成]_ ボタンを選択してイベント サブスクリプションを作成します。

## <a name="send-simulated-iot-data"></a>シミュレートされた IoT データの送信

新しいイングレス機能をテストするには、デバイス シミュレーターを使用します。これについては、[エンドツーエンドのソリューションの接続](./tutorial-end-to-end.md)に関するページを参照してください。 このチュートリアルは、こちらの [C# で記述された Azure Digital Twins のエンドツーエンドのサンプル プロジェクト](/samples/azure-samples/digital-twins-samples/digital-twins-samples)を使用して進められます。 このリポジトリでは、**DeviceSimulator** プロジェクトを使用します。

このエンド ツー エンドのチュートリアルでは、次の手順を実行します。
1. [シミュレートされたデバイスを IoT Hub に登録する](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [シミュレーションを構成して実行する](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>結果の検証

上記のデバイス シミュレーターを実行中に、デジタル ツインの温度値が変化します。 Azure CLI で、次のコマンドを実行して、温度値を確認します。

```azurecli-interactive
az dt twin query --query-command "select * from digitaltwins" --dt-name <Digital-Twins-instance-name>
```

出力には、次のような温度値が含まれている必要があります。

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"dbf2fea8-d3f7-42d0-8037-83730dc2afc5\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "lastUpdateTime": "2021-06-03T17:05:52.0062638Z"
        }
      },
      "Temperature": 70.20518558807913
    }
  ]
}
```

値の変化を確認するには、上記のクエリ コマンドを繰り返し実行します。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins を使用したデータのイングレスとエグレスについて確認します。
* [データのイングレスとエグレス](concepts-data-ingress-egress.md)
