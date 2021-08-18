---
title: Azure IoT Central デバイス ブリッジをデプロイする |Microsoft Docs
description: IoT Central デバイス ブリッジをデプロイして他の IoT クラウドを IoT Central アプリに接続します。 他の IoT クラウドには、Sigfox、Particle Device Cloud、The Things Network などがあります。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 3fd74e0fddedba5f69176c83aea5a5522bc54c2a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113758015"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>IoT Central デバイス ブリッジを使用して他の IoT クラウドを IoT Central に接続する

IoT Central デバイス ブリッジは、他 IoT のクラウドを IoT Central アプリケーションに接続するオープン ソース ソリューションです。 他の IoT クラウドの例として、[Sigfox](https://www.sigfox.com/)、[Particle Device Cloud](https://www.particle.io/)、[The Things Network](https://www.thethingsnetwork.org/) などがあります。 デバイス ブリッジは、他の IoT クラウドに接続されているデバイスから IoT Central アプリケーションにデータを転送することで機能します。 デバイス ブリッジでは、IoT Central にデータを転送するだけで、IoT Central からデバイスにコマンドやプロパティの更新を送信しません。

デバイス ブリッジを使用すると、IoT Central の機能を、Sigfox の低電力広域通信網に接続された資産追跡デバイス、Particle Device Cloud の大気質監視デバイス、The Things Network の土壌水分監視デバイスなどのデバイスと組み合わせることができます。 データのルールや分析などの IoT Central アプリケーションの機能を使用したり、Power Automate や Azure Logic Apps でワークフローを作成したり、データをエクスポートしたりすることができます。

デバイス ブリッジ ソリューションでは、デバイス メッセージを変換して IoT Central に転送するために連携して動作する複数の Azure リソースを Azure サブスクリプションにプロビジョニングします。

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を完了するには、次が必要です。

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="overview"></a>概要

IoT Central デバイス ブリッジは、GitHub のオープン ソース ソリューションです。 カスタム Azure Resource Manager テンプレートを使用して、Azure Functions の関数アプリを含む複数のリソースを Azure サブスクリプションにデプロイします。

関数アプリはデバイス ブリッジの中核部分です。 シンプルな Webhook を介して他の IoT プラットフォームから HTTP POST 要求を受信します。 [Azure IoT Central デバイス ブリッジ](https://github.com/Azure/iotc-device-bridge) リポジトリには、Sigfox、Particle、および The Things Network クラウドに接続する方法を示す例が含まれています。 お使いのプラットフォームで関数アプリに HTTP POST 要求を送信できる場合は、カスタムの IoT クラウドに接続するようにこのソリューションを拡張できます。

関数アプリでは、データを IoT Central で受け入れられる形式に変換し、Device Provisioning Service とデバイス クライアントの API を使用してそれを転送します。

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Azure Functions のスクリーンショット。":::

IoT Central アプリケーションで転送されたメッセージのデバイス ID が認識されると、デバイスからのテレメトリが IoT Central に表示されます。 IoT Central アプリケーションでデバイス ID が認識されない場合、関数アプリでは、そのデバイス ID を使用して新しいデバイスを登録しようとします。 新しいデバイスは、IoT Central アプリケーションの **[デバイス]** ページに **[関連付けられていないデバイス]** として表示されます。 **[デバイス]** ページでは、新しいデバイスをデバイス テンプレートに関連付けてから、テレメトリを表示することができます。

## <a name="deploy-the-device-bridge"></a>デバイス ブリッジをデプロイする

デバイス ブリッジをサブスクリプションにデプロイするには:

1. IoT Central アプリケーションで、 **[管理] > [デバイス接続]** に移動します。

    1. **ID スコープ** を書き留めておきます。 この値は、デバイス ブリッジをデプロイするときに使用します。

    1. 同じページで、 **[SAS-IoT-Devices]** 登録グループを開きます。 **[SAS-IoT-Devices]** グループのページで、**プライマリ キー** をコピーします。 この値は、デバイス ブリッジをデプロイするときに使用します。

1. 下の **[Azure へのデプロイ]** ボタンを使用して、関数アプリをサブスクリプションにデプロイするカスタム Resource Manager テンプレートを開きます。 前の手順の **ID スコープ** と **プライマリ キー** を使用します。

    [![Azure へのデプロイ](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

デプロイが完了したら、関数で必要とされる NPM パッケージをインストールする必要があります。

1. Azure portal で、サブスクリプションにデプロイされた関数アプリを開きます。 次に、 **[開発ツール]**  >  **[コンソール]** の順に移動します。 コンソールで次のコマンドを実行して、パッケージをインストールします。

    ```bash
    cd IoTCIntegration
    npm install
    ```

    これらのコマンドの実行には数分かかることがあります。 警告メッセージはすべて無視してかまいません。

1. パッケージのインストールが完了したら、関数アプリの **[概要]** ページで **[再起動]** を選択します。

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="[再起動] のスクリーンショット。":::

1. これで、関数を使用する準備ができました。 外部システムでは、HTTP POST 要求を使用してデバイス データをデバイス ブリッジ経由で IoT Central アプリケーションに送信できます。 関数の URL を取得するには、 **[関数] > [IoTCIntegration] > [コードとテスト] > [関数の URL の取得]** に移動します。

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="[関数の URL の取得] のスクリーンショット。":::

デバイス ブリッジに送信されるメッセージ本文は、次の形式である必要があります。

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

`measurements` オブジェクトの各キーは、IoT Central アプリケーションのデバイス テンプレートに含まれるテレメトリの種類の名前と一致する必要があります。 このソリューションでは、メッセージ本文でのインターフェイス ID の指定はサポートされていません。 そのため、2 つの異なるインターフェイスに同じ名前のテレメトリの種類がある場合は、IoT Central アプリケーションの両方のテレメトリ ストリームに測定値が表示されます。

本文に `timestamp` フィールドを追加して、メッセージの日付と時刻 (UTC) を指定できます。 このフィールドは、ISO 8601 形式である必要があります。 たとえば、「 `2020-06-08T20:16:54.602Z` 」のように入力します。 タイムスタンプを含めない場合は、現在の日付と時刻が使用されます。

本文に `modelId` フィールドを追加できます。 プロビジョニング中にデバイスをデバイス テンプレートに関連付けるには、このフィールドを使用します。 この機能は、[V3 アプリケーション](howto-faq.yml#how-do-i-get-information-about-my-application-)でのみサポートされています。

`deviceId` は、小文字の英数字でなければならず、ハイフンを含めることができます。

`modelId` フィールドを含めない場合、または IoT Central でモデル ID が認識されない場合は、認識されない `deviceId` を持つメッセージによって、IoT Central に新しい _関連付けられていないデバイス_ が作成されます。 オペレーターは、デバイスを適切なデバイス テンプレートに手動で移行できます。 詳細については、[「Azure IoT Central アプリケーションでデバイスを管理する」のデバイスのテンプレートへの移行](howto-manage-devices-individually.md)に関するセクションを参照してください。

[V2 アプリケーション](howto-faq.yml#how-do-i-get-information-about-my-application-)では、 **[デバイス エクスプローラー] > [関連付けられていないデバイス]** ページに新しいデバイスが表示されます。 デバイスからの受信テレメトリの受信を開始するには、 **[関連付け]** を選択し、デバイス テンプレートを選択します。

> [!NOTE]
> デバイスがテンプレートに関連付けられるまで、関数のすべての HTTP 呼び出しで 403 エラー状態が返されます。

Application Insights で関数アプリのログ記録を切り替えるには、Azure portal の関数アプリで **[監視] > [ログ]** に移動します。 **[Application Insights を有効にする]** を選択します。

## <a name="provisioned-resources"></a>プロビジョニングされたリソース

Resource Manager テンプレートでは、Azure サブスクリプションに次のリソースがプロビジョニングされます。

* 関数アプリ
* App Service プラン
* ストレージ アカウント
* Key Vault

キー コンテナーには、IoT Central アプリケーションの SAS グループ キーが格納されます。

関数アプリは、[従量課金プラン](https://azure.microsoft.com/pricing/details/functions/)で実行されます。 このオプションでは、専用のコンピューティング リソースが提供されませんが、デバイス ブリッジで 1 分あたり数百個のデバイス メッセージを処理できます。これは、小規模なデバイス フリートやメッセージを送信する頻度が低いデバイスに適しています。 アプリケーションが多数のデバイス メッセージのストリーミングに依存している場合は、従量課金プランを専用の [App Service プラン](https://azure.microsoft.com/pricing/details/app-service/windows/)に置き換えます。 このプランでは、専用のコンピューティング リソースが提供されるため、サーバーの応答時間が短縮されます。 Standard App Service プランを使用した場合、このリポジトリで観測された Azure からの関数の最大パフォーマンスは 1 分あたり 1500 個のデバイス メッセージでした。 詳細については、「[Azure Functions のホスティング オプション](../../azure-functions/functions-scale.md)」を参照してください。

従量課金プランではなく専用の App Service プランを使用するには、カスタム テンプレートをデプロイする前に編集します。 **[テンプレートの編集]** を選択します。

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="[テンプレートの編集] のスクリーンショット。":::

次のセグメントを置き換えます。

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

代入

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

次に、テンプレートを編集して、`"properties": {"SiteConfig": {...}}` の下の `functionapp` リソースの構成に `"alwaysOn": true` を含めます。[AlwaysOn 構成](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan)では、関数アプリが常に実行されるようになります。

## <a name="examples"></a>例

以下の例で、さまざまな IoT クラウドのためにデバイス ブリッジを構成する方法を説明します。

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>例 1: デバイス ブリッジを介して Particle デバイスを接続する

デバイス ブリッジを介して IoT Central に Particle デバイスを接続するには、Particle コンソールにアクセスし、新しい Webhook 統合を作成します。 **[要求の形式]** を **[JSON]** に設定します。  **[詳細設定]** で、次のカスタム本文形式を使用します。

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": "{{{PARTICLE_EVENT_VALUE}}}"
  }
}
```

関数アプリから **関数の URL** を貼り付けます。IoT Central では、Particle デバイスが [関連付けられていないデバイス] として表示されます。 詳細については、[Particle を利用したプロジェクトを Azure IoT Central に統合する方法](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/)に関するブログ記事をご覧ください。

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>例 2: デバイス ブリッジを介して Sigfox デバイスを接続する

プラットフォームによっては、Webhook を介して送信されるデバイス メッセージの形式を指定できない場合があります。 このようなシステムでは、デバイス ブリッジでの処理の前に、メッセージのペイロードを期待される本文形式に変換する必要があります。 この変換は、デバイス ブリッジを実行する同じ関数で行うことができます。

このセクションでは、Sigfox Webhook 統合のペイロードをデバイス ブリッジで期待される本文形式に変換する方法について説明します。 Sigfox クラウドでは、デバイス データを 16 進数の文字列形式で送信します。 便宜上、デバイス ブリッジにはこの形式用の変換関数が含まれています。この関数では、Sigfox デバイスのペイロードで使用可能なフィールドの種類のサブセット (8、16、32、または 64 ビットの `int` および `uint`、32 または 64 ビットの `float`、リトルエンディアンとビッグエンディアン) を受け入れます。 Sigfox Webhook 統合からのメッセージを処理するには、関数アプリの _IoTCIntegration/index.js_ ファイルを次のように変更します。

メッセージのペイロードを変換するため、21 行目の `handleMessage` の呼び出しの前に次のコードを追加して、`payloadDefinition` を Sigfox のペイロード定義に置き換えます。

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Sigfox デバイスでは、`204` 応答コードを期待しています。 21 行目の `handleMessage` の呼び出しの後に、次のコードを追加します。

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>例 3: デバイス ブリッジを介して The Things Network のデバイスを接続する

The Things Network デバイスを IoT Central に接続するには:

* 新しい HTTP 統合を The Things Network のアプリケーションに追加します ( **[アプリケーション] > [統合] > [統合の追加] > [HTTP 統合]** )。
* デバイス メッセージのペイロードを関数に送信する前に JSON に自動的に変換するデコーダー関数がアプリケーションに含まれていることを確認します ( **[アプリケーション] > [ペイロード関数] > [デコーダー]** )。

次のサンプルは、バイナリ データから一般的な数値型をデコードするために使用できる JavaScript デコーダー関数を示しています。

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

統合を定義した後、関数アプリの "*IoTCIntegration/index.js*" ファイルの 21 行目にある `handleMessage` の呼び出しの前に、次のコードを追加します。 このコードでは、HTTP 統合の本文を期待される形式に変換します。

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>制限事項

デバイス ブリッジでは、IoT Central にメッセージを転送するだけで、デバイスにメッセージを返信しません。 このため、このデバイス ブリッジを介して IoT Central に接続するデバイスに対するプロパティやコマンドは機能しません。 デバイス ツイン操作はサポートされていないため、デバイス ブリッジを介してデバイスのプロパティを更新することはできません。 これらの機能を使用するには、デバイスでいずれかの [Azure IoT device SDK](../../iot-hub/iot-hub-devguide-sdks.md) を使用して IoT Central に直接接続する必要があります。

## <a name="next-steps"></a>次のステップ
ここでは、IoT Central デバイス ブリッジをデプロイする方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [デバイスの管理](howto-manage-devices-individually.md)
