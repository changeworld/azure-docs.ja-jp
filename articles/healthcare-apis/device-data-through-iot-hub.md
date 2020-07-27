---
title: チュートリアル:Azure IoT Hub を通じてデバイス データを受信する
description: このチュートリアルでは、デバイス データを IoT Hub から IoT コネクタ経由で Azure API for FHIR にルーティングできるようにする方法について説明します。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 06/27/2020
ms.author: punagpal
ms.openlocfilehash: 95e3e2be175fa810b1b966a7dda5a0e53e23d780
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540490"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>チュートリアル:Azure IoT Hub を通じてデバイス データを受信する

IoT コネクタを使用すると、Internet of Medical Things (IoMT) デバイスから Azure API for FHIR にデータを取り込むことができます。 [Azure portal を使用した IoT コネクタ (プレビュー) のデプロイ](iot-fhir-portal-quickstart.md)に関するクイックスタートには、Azure IoT Central で管理されているデバイスから IoT コネクタに[テレメトリを送信](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot)する例が示されています。 IoT コネクタは、Azure IoT Hub を介してプロビジョニングおよび管理されているデバイスでも使用できます。 このチュートリアルでは、Azure IoT Hub から IoT コネクタに接続し、デバイス データをルーティングする手順について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブな Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 少なくとも 1 つの IoT コネクタを備えた Azure API for FHIR リソース - [Azure portal を使用して IoT コネクタ (プレビュー) をデプロイする](iot-fhir-portal-quickstart.md)
- 実際のデバイスまたはシミュレートされたデバイスに接続されている Azure IoT Hub リソース - [Azure portal を使用して IoT ハブを作成する](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Azure IoT Hub でシミュレートされたデバイス アプリケーションを使用する場合は、サポートされている各種言語およびシステムの中から任意のアプリケーションを選択できます。

## <a name="get-connection-string-for-iot-connector-preview"></a>IoT コネクタ (プレビュー) の接続文字列を取得する

Azure IoT Hub には、IoT コネクタと安全に接続するための接続文字列が必要です。 「[接続文字列を生成する](iot-fhir-portal-quickstart.md#generate-a-connection-string)」の説明に従って、IoT コネクタ用の新しい接続文字列を作成します。 この接続文字列は、次のセクションで使用できるように保存しておいてください。

IoT コネクタは、内部で Azure Event Hub インスタンスを使用してデバイス メッセージを受信します。 上記で作成した接続文字列は、実際には、この基盤となるイベント ハブへの接続文字列です。

## <a name="connect-azure-iot-hub-with-the-iot-connector-preview"></a>Azure IoT Hub を IoT コネクタ (プレビュー) と接続する

Azure IoT Hub は、[メッセージ ルーティング](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)と呼ばれる機能をサポートしています。これは、イベント ハブ、ストレージ アカウント、Service Bus などのさまざまな Azure サービスにデバイス データを送信する機能です。 IoT コネクタは、この機能を利用して、Azure IoT Hub からそのイベント ハブ エンドポイントに接続し、デバイス データを送信します。

> [!NOTE] 
> 現在、[メッセージ ルーティングの作成](https://docs.microsoft.com/azure/iot-hub/tutorial-routing)に使用できるのは PowerShell または CLI コマンドのみです。これは、IoT コネクタのイベント ハブが顧客サブスクリプションではホストされておらず、Azure portal に表示されないからです。 ただし、PowerShell または CLI を使用してメッセージ ルート オブジェクトを追加すると、それらのオブジェクトが Azure portal に表示され、そこから管理できるようになります。

メッセージ ルーティングの設定は、2 つの手順から成ります。

### <a name="add-an-endpoint"></a>エンドポイントの追加
この手順では、IoT ハブによるデータのルーティング先のエンドポイントを定義します。 このエンドポイントを作成するには、好みに応じて、PowerShell の [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) コマンドか、CLI の [az iot hub routing-endpoint create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) コマンドを使用します。

エンドポイントの作成コマンドで使用するパラメーターの一覧を次に示します。

|PowerShell パラメーター|CLI パラメーター|説明|
|---|---|---|
|ResourceGroupName|resource-group|IoT Hub リソースのリソース グループ名。|
|名前|hub-name|IoT Hub リソースの名前。|
|EndpointName|endpoint-name|作成するエンドポイントに割り当てる名前を使用します。|
|EndpointType|endpoint-type|IoT Hub が接続する必要があるエンドポイントの種類。 "EventHub" (PowerShell の場合) および "eventhub" (CLI の場合) というリテラル値を使用します。|
|EndpointResourceGroup|endpoint-resource-group|IoT コネクタの Azure API for FHIR リソースのリソース グループ名。 この値は、Azure API for FHIR の [概要] ページから取得できます。|
|EndpointSubscriptionId|endpoint-subscription-id|IoT コネクタの Azure API for FHIR リソースのサブスクリプション ID。 この値は、Azure API for FHIR の [概要] ページから取得できます。|
|ConnectionString|connection-string|IoT コネクタへの接続文字列。 前の手順で取得した値を使用します。|

### <a name="add-a-message-route"></a>メッセージ ルートを追加する
この手順では、上で作成したエンドポイントを使用してメッセージ ルートを定義します。 ルートを作成するには、好みに応じて、PowerShell の [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) コマンドか、CLI の [az iot hub route create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) コマンドを使用します。

エンドポイントの作成コマンドで使用するパラメーターの一覧を次に示します。

|PowerShell パラメーター|CLI パラメーター|説明|
|---|---|---|
|ResourceGroupName|g|IoT Hub リソースのリソース グループ名。|
|名前|hub-name|IoT Hub リソースの名前。|
|EndpointName|endpoint-name|上で作成したエンドポイントの名前。|
|RouteName|route-name|作成するメッセージ ルートに割り当てる名前。|
|source|source-type|エンドポイントに送信するデータの種類。 "DeviceMessages" (PowerShell の場合) および "devicemessages" (CLI の場合) というリテラル値を使用します。|

## <a name="send-device-message-to-iot-hub"></a>デバイス メッセージを IoT ハブに送信する

実際のデバイスまたはシミュレートされたデバイスを使用して、以下に示す心拍メッセージのサンプルを Azure IoT Hub に送信します。 このメッセージは IoT コネクタにルーティングされ、そこで FHIR Observation リソースに変換されてから Azure API for FHIR に格納されます。

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> 必ず、IoT コネクタで構成した[マッピング テンプレート](iot-mapping-templates.md)に準拠したデバイス メッセージを送信するようにしてください。

## <a name="view-device-data-in-azure-api-for-fhir"></a>Azure API for FHIR でデバイス データを表示する

IoT コネクタによって作成された FHIR Observation リソースを、Postman を使用して Azure API for FHIR で表示できます。 [Azure API for FHIR にアクセスするように Postman](access-fhir-postman-tutorial.md) を設定し、上のサンプル メッセージで送信された心拍の値を含む Observation FHIR リソースを表示するように `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` に対して `GET` 要求を行います。

> [!TIP]
> usx`er に、Azure API for FHIR データ プレーンへの適切なアクセス権があることを確認します。 [Azure のロールベースのアクセス制御](configure-azure-rbac.md)を使用して、必要なデータ プレーン ロールを割り当ててください。


## <a name="next-steps"></a>次のステップ

このクイックスタート ガイドでは、IoT コネクタにデバイス データをルーティングするように Azure IoT Hub を設定しました。 以下に示す、次のステップの中からいずれかを選択して、IoT コネクタの詳細を確認してください。

IoT コネクタ内のさまざまなデータ フロー ステージについて説明します。

>[!div class="nextstepaction"]
>[IoT コネクタのデータ フロー](iot-data-flow.md)

デバイスと FHIR マッピング テンプレートを使用して IoT コネクタを構成する方法について説明します。

>[!div class="nextstepaction"]
>[IoT コネクタのマッピング テンプレート](iot-mapping-templates.md)

FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。

