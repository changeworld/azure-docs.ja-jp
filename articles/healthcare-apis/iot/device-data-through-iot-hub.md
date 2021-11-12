---
title: Azure IoT Hub を使用したデバイスデータの受信-Azure の医療 api
description: このチュートリアルでは、IoT コネクタを使用して IoT Hub から FHIR サービスへのデバイスデータのルーティングを有効にする方法について説明します。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: f12c5efe25f48ae16e4f59159936627a27b47212
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283469"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>チュートリアル:Azure IoT Hub を通じてデバイス データを受信する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

IoT コネクタでは、さまざまな IoT health 関連または医療機器から、高速医療相互運用性リソース (FHIR®) サービスに正常性データを収集して取り込む機能が提供されます。 IoT コネクタは、Azure IoT hub を介して作成および管理されるデバイスとの相互運用性と応答性が向上し、ワークフローの強化と使いやすさを実現します。 このチュートリアルでは、Azure IoT Hub から IoT コネクタに接続し、デバイス データをルーティングする手順について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブな Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 少なくとも1つの IoT コネクタがある FHIR サービスリソース- [Azure portal を使用して iot コネクタをデプロイする](deploy-iot-connector-in-azure.md)
- 実際のデバイスまたはシミュレートされたデバイスに接続されている Azure IoT Hub リソース - [Azure portal を使用して IoT ハブを作成する](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)

> [!TIP]
> Azure IoT Hub でシミュレートされたデバイス アプリケーションを使用する場合は、サポートされている各種言語およびシステムの中から任意のアプリケーションを選択できます。

## <a name="get-connection-string-for-iot-connector"></a>IoT コネクタの接続文字列を取得する

Azure IoTハブには、IoT コネクタと安全に接続するための接続文字列が必要です。 「 [接続文字列を生成する](../azure-api-for-fhir/iot-fhir-portal-quickstart.md#generate-a-connection-string)」で説明されているように、IoT コネクタの新しい接続文字列を作成します。 この接続文字列をセーフ、次の手順で使用します。

IoT コネクタは、内部で Azure Event Hub インスタンスを使用してデバイス メッセージを受信します。 上記で作成した接続文字列は、実際には、この基盤となるイベント ハブへの接続文字列です。

## <a name="connect-azure-iot-hub-with-iot-connector"></a>IoT コネクタを使用した Azure IoT ハブの Connect

Azure IoT Hub は、[メッセージ ルーティング](../../iot-hub/iot-hub-devguide-messages-d2c.md)と呼ばれる機能をサポートしています。これは、イベント ハブ、ストレージ アカウント、Service Bus などのさまざまな Azure サービスにデバイス データを送信する機能です。 IoT コネクタは、この機能を使用して、Azure IoT hub のデバイスデータを接続し、そのイベントハブエンドポイントに送信します。

> [!NOTE] 
> 現時点では、PowerShell または CLI コマンドのみを使用して [メッセージルーティングを作成](../../iot-hub/tutorial-routing.md) できます。これは、IoT コネクタのイベントハブが顧客サブスクリプションでホストされていないため、Azure portal を通じて表示されないためです。 ただし、PowerShell または CLI を使用してメッセージ ルート オブジェクトを追加すると、それらのオブジェクトが Azure portal に表示され、そこから管理できるようになります。

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
|EndpointResourceGroup|endpoint-resource-group|IoT コネクタの FHIR サービスリソースのリソースグループ名。 この値は、FHIR サービスの [概要] ページから取得できます。|
|EndpointSubscriptionID|endpoint-subscription-id|IoT コネクタの FHIR サービスリソースのサブスクリプション ID。 この値は、FHIR サービスの [概要] ページから取得できます。|
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

## <a name="send-device-message-to-azure-iot-hub"></a>Azure IoT Hub にデバイスメッセージを送信する

実際のデバイスまたはシミュレートされたデバイスを使用して、以下に示す心拍メッセージのサンプルを Azure IoT Hub に送信します。 このメッセージは IoT コネクタにルーティングされ、メッセージは FHIR 監視リソースに変換され、FHIR サービスに格納されます。

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
> IoT コネクタで構成された [マッピングテンプレート](how-to-use-fhir-mapping-iot.md) に準拠したデバイスメッセージを送信するようにしてください。

## <a name="view-device-data-in-fhir-service"></a>FHIR サービスでデバイスデータを表示する

Postman を使用して、FHIR サービス上の IoT コネクタによって作成された FHIR 監視リソースを表示できます。 詳細については、「 [Postman を使用した fhir サービスへのアクセス](./../use-postman.md)」と、 `GET` 上記の `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` サンプルメッセージで送信されたハートレートの値を使用して監視 fhir リソースを表示するように要求します。

> [!TIP]
> ユーザーが FHIR サービスデータプレーンへの適切なアクセス権を持っていることを確認します。 [Azure のロールベースのアクセス制御 (Azure RBAC)](../azure-api-for-fhir/configure-azure-rbac.md) を使用して、必要なデータ プレーン ロールを割り当てます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT コネクタにデバイスデータをルーティングするように Azure IoT ハブを設定します。 次の手順の中から選択して、IoT コネクタの詳細を確認してください。

IoT コネクタ内のデータフローのさまざまな段階を理解します。

>[!div class="nextstepaction"]
>[IoT コネクタのデータ フロー](iot-data-flow.md)

(FHIR&#174;) HL7 の登録商標であり、HL7 のアクセス許可と共に使用されます。
