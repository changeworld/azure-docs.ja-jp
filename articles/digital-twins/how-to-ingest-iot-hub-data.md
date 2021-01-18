---
title: IoT Hub からテレメトリを取り込む
titleSuffix: Azure Digital Twins
description: IoT Hub からデバイスのテレメトリ メッセージを取り込む方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9ecc14aa9591d6e62dccd9899a80de44411928a1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051090"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Azure Digital Twins に IoT Hub テレメトリを取り込む

Azure Digital Twins は、IoT デバイスやその他のソースからのデータに基づいています。 Azure Digital Twins で使用するデバイス データの一般的なソースは [IoT Hub](../iot-hub/about-iot-hub.md) です。

Azure Digital Twins にデータを取り込むプロセスは、[Azure Functions](../azure-functions/functions-overview.md) を使って作られた関数のような外部のコンピューティング リソースを設定することです。 この関数はデータを受け取り、必要に応じて [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) を使用してプロパティを設定したり、[デジタル ツイン](concepts-twins-graph.md)でテレメトリ イベントを発生させたりします。 

このハウツー ドキュメントでは、IoT Hub からテレメトリを取り込むことができる関数を作成するプロセスについて手順を追って説明します。

## <a name="prerequisites"></a>前提条件

この例を続行する前に、前提条件として次のリソースを設定する必要があります。
* **IoT ハブ** 手順については、[この IoT Hub のクイック スタート](../iot-hub/quickstart-send-telemetry-cli.md)の「*IoT Hub の作成*」のセクションを参照してください。
* デジタル ツイン インスタンスを呼び出すための適切なアクセス許可が備わっている **関数**。 手順については、[*方法: データを処理するための関数を Azure で設定する*](how-to-create-azure-function.md)を参照してください。 
* デバイス テレメトリを受信する **Azure Digital Twins インスタンス**。 手順については、[*方法: Azure Digital Twins インスタンスと認証の設定*](./how-to-set-up-instance-portal.md)に関するページを参照してください。

### <a name="example-telemetry-scenario"></a>テレメトリのシナリオ例

このハウツーでは、Azure の関数を使用して、IoT Hub から Azure Digital Twins にメッセージを送信する方法の概要について説明します。 メッセージの送信に使用できる構成とマッチング戦略は多数ありますが、この記事の例には次のパーツが含まれています。
* 既知のデバイス ID を持つ IoT Hub の温度計デバイス
* ID が一致するデバイスを表すデジタル ツイン

> [!NOTE]
> この例では、デバイス ID と対応するデジタル ツインの ID の間の単純な ID 一致を使用していますが、(マッピング テーブルなどを使用して) デバイスからツインへのより高度なマッピングを用意することもできます。

サーモスタット デバイスから温度テレメトリ イベントが送信されるたびに、関数によってそのテレメトリが処理され、デジタル ツインの *temperature* プロパティが更新されます。 このシナリオの概要を次の図に示します。

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="フロー チャートを示す図。このグラフでは、IoT Hub を通じて、温度テレメトリが IoT Hub デバイスから Azure の関数に送信されます。これにより、Azure Digital Twins 内にあるツインの temperature プロパティが更新されます。" border="false":::

## <a name="add-a-model-and-twin"></a>モデルとツインの追加

次の CLI コマンドを使用してモデルを追加またはアップロードできます。その後、このモデルを使用して IoT Hub の情報で更新されるツインを作成できます。

モデルは次のようになります。
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**このモデルをツイン インスタンスにアップロードする** には、Azure CLI を開き、次のコマンドを実行します。

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

次に、**このモデルを使用して 1 つのツインを作成** します。 次のコマンドを使用してツインを作成して、初期温度値を 0.0 に設定します。

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

ツイン作成コマンドが成功した場合の出力は、次のようになります。
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>関数を作成する

このセクションでは、同じ Visual Studio のスタートアップ手順と、関数スケルトンを使用します。これについては、[*方法:データを処理するための関数の設定*](how-to-create-azure-function.md)に関するページを参照してください。 このスケルトンを使用すると、認証を処理し、サービス クライアントを作成することができます。これにより、データを処理し、応答として Azure Digital Twins API を呼び出すことができるようになります。 

次の手順では、IoT Hub からの IoT テレメトリ イベントを処理するための特定のコードを追加します。  

### <a name="add-telemetry-processing"></a>テレメトリの処理を追加する
    
テレメトリ イベントは、デバイスからのメッセージの形式で送信されます。 テレメトリ処理コードを追加する最初の手順は、このデバイス メッセージの関連部分を Event Grid イベントから抽出することです。 

デバイスによってメッセージの構成が異なる場合があるため、**この手順のコードは接続されているデバイスによって変わります**。 

次のコードは、テレメトリを JSON として送信する単純なデバイスの例を示しています。 このサンプルについては、["*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)" に関するページを参照してください。 次のコードによって、メッセージを送信したデバイスのデバイス ID と温度値が検索されます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Find_device_ID_and_temperature":::

次のコード サンプルでは、ID と温度値を取得し、それらを使用して、そのツインを "パッチ" (更新) します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Update_twin_with_device_temperature":::

### <a name="update-your-function-code"></a>関数コードの更新

前のサンプルのコードを理解したので、Visual Studio で「[*前提条件*](#prerequisites)」セクションから関数を開きます。 (Azure で作成された関数がない場合は、前提条件のリンクにアクセスして、今すぐ作成してください。)

関数のコードをこのサンプル コードに置き換えます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

関数コードを保存し、関数アプリを Azure に発行します。 方法については、[*Azure でデータ処理の関数を設定する方法*](how-to-create-azure-function.md)に関するページの [*関数アプリの発行*](./how-to-create-azure-function.md#publish-the-function-app-to-azure)に関するセクションを参照してください。

発行が成功すると、次に示すように、Visual Studio のコマンド ウィンドウに出力が表示されます。

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
発行プロセスの状態は、[Azure portal](https://portal.azure.com/) で確認することもできます。 "_リソース グループ_" を検索し、 _[アクティビテ ィログ]_ に移動して、一覧で _[Get web app publishing profile]\(Web アプリ発行プロファイルの取得\)_ を探し、状態が [成功] であることを確認します。

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="発行プロセスの状態を示す Azure portal のスクリーンショット。":::

## <a name="connect-your-function-to-iot-hub"></a>関数の IoT Hub への接続

ハブ データのイベントの宛先を設定します。
[Azure portal](https://portal.azure.com/) で、「[*前提条件*](#prerequisites)」セクションで作成した IoT Hub インスタンスに移動します。 **[イベント]** で、関数のサブスクリプションを作成します。

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="イベント サブスクリプションの追加を示す Azure portal のスクリーンショット。":::

**[イベント サブスクリプションの作成]** ページで、各フィールドに次のように入力します。
  1. **[名前]** で、サブスクリプションに任意の名前を付けます。
  2. **[イベント スキーマ]** で、 _[Event Grid スキーマ]_ を選択します。
  3. **[イベントの種類]** で、 _[Device Telemetry]\(デバイス テレメトリ\)_ チェックボックスをオンにし、その他のイベントの種類をオフにします。
  4. **[エンドポイントのタイプ]** で _[Azure 関数]_ を選択します。
  5. **[エンドポイント]** で、 _[エンドポイントの選択]_ リンクを選択してエンドポイントを作成します。
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="イベント サブスクリプションの詳細を作成する Azure portal のスクリーンショット":::

_[Azure 関数の選択]_ ページが開いたら、次の詳細を確認します。
 1. **サブスクリプション**:お使いの Azure サブスクリプション
 2. **[リソース グループ]** :該当するリソース グループ
 3. **関数アプリ**:自分の関数アプリの名前
 4. **Slot**:_運用_
 5. **関数**:ドロップダウンから関数を選択します。

_[選択の確認]_ ボタンを選択して詳細を保存します。            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="関数を選択する Azure portal のスクリーンショット。":::

_[作成]_ ボタンを選択してイベント サブスクリプションを作成します。

## <a name="send-simulated-iot-data"></a>シミュレートされた IoT データの送信

新しいイングレス機能をテストするには、デバイス シミュレーターを使用します。このデバイス シミュレーターについては、["*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)" に関するページを参照してください。 このチュートリアルは、C# で記述されたサンプル プロジェクトによって進められます。 サンプル コードについては、こちらを参照してください。[Azure Digital Twins のエンドツーエンド サンプル](/samples/azure-samples/digital-twins-samples/digital-twins-samples)。 このリポジトリでは、**DeviceSimulator** プロジェクトを使用します。

このエンド ツー エンドのチュートリアルでは、次の手順を実行します。
1. [*シミュレートされたデバイスを IoT Hub に登録する*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*シミュレーションを構成して実行する*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>結果の検証

上記のデバイス シミュレーターを実行中に、デジタル ツインの温度値が変化します。 Azure CLI で、次のコマンドを実行して、温度値を確認します。

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

出力には、次のような温度値が含まれている必要があります。

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

値の変化を確認するには、上記のクエリ コマンドを繰り返し実行します。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins を使用したデータのイングレスとエグレスについて確認します。
* "[*概念: 他のサービスとの統合*](concepts-integration.md)
