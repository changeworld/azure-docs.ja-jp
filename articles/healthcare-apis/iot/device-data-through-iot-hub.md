---
title: Azure IoT Hub 経由でデバイス データを受信する - Azure Healthcare API
description: このチュートリアルでは、IoT コネクタを使用して、デバイスから FHIR サービスへのデバイス IoT Hubルーティングを有効にする方法について説明します。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 9c4dd42d81374f75beb66f0564a2fb2b0fc38c01
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709217"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>チュートリアル:Azure IoT Hub を通じてデバイス データを受信する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

IoT コネクタは、さまざまな IoT 正常性関連または医療デバイスから正常性データを収集し、高速ヘルスケア相互運用性リソース (FHIR®) サービスに取り込む機能を提供します。 IoT コネクタは、高度なワークフローと使いやすさを実現するために、Azure IoT Hub を使用して作成および管理されたデバイスと相互運用可能で応答性に優れたデバイスです。 このチュートリアルでは、Azure IoT Hub から IoT コネクタに接続し、デバイス データをルーティングする手順について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブな Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 少なくとも 1 つの IoT コネクタを使用した FHIR サービス リソース - [IoT](deploy-iot-connector-in-azure.md)コネクタをデプロイするには、次のAzure portal
- 実際のデバイスまたはシミュレートされたデバイスに接続されている Azure IoT Hub リソース - [Azure portal を使用して IoT ハブを作成する](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)

> [!TIP]
> Azure IoT Hub でシミュレートされたデバイス アプリケーションを使用する場合は、サポートされている各種言語およびシステムの中から任意のアプリケーションを選択できます。

## <a name="get-connection-string-for-iot-connector"></a>IoT コネクタの接続文字列を取得する

Azure IoT Hub には、IoT コネクタに安全に接続するための接続文字列が必要です。 「接続文字列を生成する」の説明に従って、IoT コネクタ用の新 [しい接続文字列を作成します](../azure-api-for-fhir/iot-fhir-portal-quickstart.md#generate-a-connection-string)。 セーフこの接続文字列を次の手順で使用する必要があります。

IoT コネクタは、内部で Azure Event Hub インスタンスを使用してデバイス メッセージを受信します。 上記で作成した接続文字列は、実際には、この基盤となるイベント ハブへの接続文字列です。

## <a name="connect-azure-iot-hub-with-iot-connector"></a>Connect Azure IoT Hub と IoT コネクタ

Azure IoT Hub は、[メッセージ ルーティング](../../iot-hub/iot-hub-devguide-messages-d2c.md)と呼ばれる機能をサポートしています。これは、イベント ハブ、ストレージ アカウント、Service Bus などのさまざまな Azure サービスにデバイス データを送信する機能です。 IoT コネクタでは、この機能を使用して、Azure IoT Hub からその Event Hub エンドポイントにデバイス データを接続して送信します。

> [!NOTE] 
> 現時点では、PowerShell または CLI コマンドのみを使用[](../../iot-hub/tutorial-routing.md)してメッセージ ルーティングを作成できます。IoT コネクタのイベント ハブは顧客のサブスクリプションでホストされていないので、Azure portal を通じて表示されません。 ただし、PowerShell または CLI を使用してメッセージ ルート オブジェクトを追加すると、それらのオブジェクトが Azure portal に表示され、そこから管理できるようになります。

メッセージ ルーティングの設定は、2 つの手順から成ります。

### <a name="add-an-endpoint"></a>エンドポイントの追加

この手順では、IoT ハブによるデータのルーティング先のエンドポイントを定義します。 このエンドポイントを作成するには、好みに応じて、PowerShell の [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIoTHubRoute) コマンドか、CLI の [az iot hub routing-endpoint create](/cli/azure/iot/hub/route#az_iot_hub_route_create) コマンドを使用します。

エンドポイントの作成コマンドで使用するパラメーターの一覧を次に示します。

|PowerShell パラメーター|CLI パラメーター|説明|
|---|---|---|
|ResourceGroupName|resource-group|IoT Hub リソースのリソース グループ名。|
|名前|hub-name|IoT Hub リソースの名前。|
|EndpointName|endpoint-name|作成するエンドポイントに割り当てる名前を使用します。|
|EndpointType|endpoint-type|IoT Hub が接続する必要があるエンドポイントの種類。 "EventHub" (PowerShell の場合) および "eventhub" (CLI の場合) というリテラル値を使用します。|
|EndpointResourceGroup|endpoint-resource-group|IoT コネクタの FHIR サービス リソースのリソース グループ名。 この値は、FHIR サービスの [概要] ページから取得できます。|
|EndpointSubscriptionID|endpoint-subscription-id|IoT コネクタの FHIR サービス リソースのサブスクリプション ID。 この値は、FHIR サービスの [概要] ページから取得できます。|
|ConnectionString|connection-string|IoT コネクタへの接続文字列。 前の手順で取得した値を使用します。|

### <a name="add-a-message-route"></a>メッセージ ルートを追加する
この手順では、上で作成したエンドポイントを使用してメッセージ ルートを定義します。 ルートを作成するには、好みに応じて、PowerShell の [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) コマンドか、CLI の [az iot hub route create](/cli/azure/iot/hub/route) コマンドを使用します。

メッセージ ルートの追加コマンドで使用するパラメーターの一覧を次に示します。

|PowerShell パラメーター|CLI パラメーター|説明|
|---|---|---|
|ResourceGroupName|g|IoT Hub リソースのリソース グループ名。|
|名前|hub-name|IoT Hub リソースの名前。|
|EndpointName|endpoint-name|上で作成したエンドポイントの名前。|
|RouteName|route-name|作成するメッセージ ルートに割り当てる名前。|
|source|source-type|エンドポイントに送信するデータの種類。 "DeviceMessages" (PowerShell の場合) および "devicemessages" (CLI の場合) というリテラル値を使用します。|

## <a name="send-device-message-to-azure-iot-hub"></a>デバイス メッセージを Azure IoT Hub に送信する

実際のデバイスまたはシミュレートされたデバイスを使用して、以下に示す心拍メッセージのサンプルを Azure IoT Hub に送信します。 このメッセージは IoT コネクタにルーティングされ、メッセージは FHIR Observation リソースに変換され、FHIR サービスに格納されます。

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
> IoT コネクタで構成されたマッピング テンプレートに準拠するデバイス [メッセージを](how-to-use-fhir-mappings.md) 必ず送信してください。

## <a name="view-device-data-in-fhir-service"></a>FHIR サービスでデバイス データを表示する

Postman を使用して、IoT コネクタによって作成された FHIR Observation リソースを FHIR サービスで表示できます。 詳細については [、「Postman](./../use-postman.md)を使用して FHIR サービスにアクセスする」を参照し、上記のサンプル メッセージで送信された心速度の値を持つ Observation FHIR リソースを表示する要求を `GET` `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 行います。

> [!TIP]
> ユーザーが FHIR サービス データ プレーンに適切なアクセス権を持っている必要があります。 [Azure のロールベースのアクセス制御 (Azure RBAC)](../azure-api-for-fhir/configure-azure-rbac.md) を使用して、必要なデータ プレーン ロールを割り当てます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、デバイス データを IoT コネクタAzure IoTハブを設定します。 IoT コネクタの詳細については、次の手順から選択してください。

IoT コネクタ内のデータ フローの異なるステージについて説明します。

>[!div class="nextstepaction"]
>[IoT コネクタのデータ フロー](iot-data-flow.md)

(FHIR&#174;) は HL7 の商標であり、HL7 の許可を得て使用されます。
