---
title: 透過的なゲートウェイ デバイスを作成する - Azure IoT Edge | Microsoft Docs
description: ダウンストリーム デバイスからの情報を処理できる透過的なゲートウェイとして、Azure IoT Edge デバイスを使用します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6069e0782f69d0dfb73d9be2998cbb11d59d7d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529171"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する

この記事では、他のデバイスが IoT Hub と通信するための透過的なゲートウェイとして機能するように IoT Edge デバイスを構成するための詳細な手順を提供します。 この記事では、*IoT Edge ゲートウェイ* という用語は、透過的ゲートウェイとして構成された IoT Edge デバイスを指します。 詳細は、[「IoT Edge デバイスをゲートウェイとして使用する方法」](./iot-edge-as-gateway.md)を参照してください。

>[!NOTE]
>現時点では:
>
> * また、Edge 対応デバイスは、IoT Edge ゲートウェイに接続できません。
> * ダウンストリーム デバイスではファイル アップロードを使用できません。

透過的なゲートウェイ接続を正常にセットアップするための 3 つの一般的な手順があります。 この記事では、最初の手順について説明します。

1. **ゲートウェイ デバイスは、ダウンストリーム デバイスに安全に接続し、ダウンストリーム デバイスからの通信を受信し、正しい宛先にメッセージをルーティングできる必要があります。**
2. ダウンストリーム デバイスは、IoT Hub で認証を行うためにデバイス ID が必要であり、そのゲートウェイ デバイス経由で通信することを認識している必要があります。 詳細については、「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)」を参照してください。
3. ダウンストリームデバイスは、ゲートウェイデバイスに、安全に接続される必要があります。 詳細については、「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)」のページを参照してください。

デバイスがゲートウェイとして機能するためには、そのダウンストリーム デバイスに安全に接続できる必要があります。 Azure IoT Edge では、公開キー基盤 (PKI) を使用して、これらのデバイス間にセキュリティで保護された接続を設定することができます。 この場合、透過的なゲートウェイとして機能する IoT Edge デバイスにダウンストリーム デバイスが接続できるようにします。 妥当なセキュリティを維持するために、ダウン ストリーム デバイスはゲートウェイ デバイスの ID を確認する必要があります。 この ID 検査により、お使いのデバイスが悪意のある可能性のあるゲートウェイに接続することを防止できます。

透過的ゲートウェイの シナリオでのダウンストリーム デバイスには、[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) クラウド サービスを使って作成された ID を持つ、任意のアプリケーションまたはプラットフォームを使用できます。 多くの場合、これらのアプリケーションでは [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md) が使用されます。 実際には、ダウンストリーム デバイスには IoT Edge ゲートウェイ デバイスそのもので実行されているアプリケーションも指定できます。 ただし、IoT Edge デバイスを IoT Edge ゲートウェイのダウンストリームにすることはできません。

デバイス ゲートウェイ トポロジに必要な信頼を有効にする証明書インフラストラクチャを作成できます。 この記事では、IoT Hub で [X.509 CA セキュリティ](../iot-hub/iot-hub-x509ca-overview.md)を 有効にするために使用するのと同じ証明書のセットアップを前提としています。これには、特定の IoT ハブ (IoT ハブのルート CA) に関連付けられた X.509 CA 証明書、およびこの CA と IoT Edge デバイスの CA によって署名された一連の証明書が使用されます。

![ゲートウェイ証明書の設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>この記事全体で使用されている "ルート CA" という用語は、PKI 証明書チェーンの最上位機関の公開証明書を指し、必ずしもシンジケート化された認証局の証明書ルートではありません。 多くの場合、実際は中間 CA の公開証明書です。

ゲートウェイは、接続の開始時に、ダウンストリーム デバイスに IoT Edge デバイス CA 証明書を提示します。 ダウンストリーム デバイスは、IoT Edge デバイスの CA 証明書がルート CA 証明書によって署名されていることを確認します。 このプロセスにより、ダウンストリーム デバイスは、ゲートウェイが信頼できるソースからのものであることを確認できます。

次の手順では、証明書を作成し、ゲートウェイ上の適切な場所にインストールするプロセスについて説明します。 任意のマシンを使用して証明書を生成してから、その証明書をお使いの IoT Edge デバイスにコピーできます。

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイスは、[運用証明書](how-to-manage-device-certificates.md) で構成されていること。

## <a name="deploy-edgehub-to-the-gateway"></a>edgeHub をゲートウェイにデプロイする

デバイスに IoT Edge を初めてインストールするときには、1 つのシステム モジュール (IoT Edge エージェント) のみが自動的に起動します。 最初のデプロイをしてデバイスを追加したら、2番目のシステムモジュール (IoT Edge ハブ) も起動します。

IoT Edge ハブは、ダウンストリームのデバイスからの受信メッセージを受信し、次の宛先にルーティングする役割を担います。 デバイス上で **edgeHub** モジュールが実行されていない場合は、デバイスの初期デプロイを作成します。 どのモジュールも追加していないため、デプロイが空のように見えますが、これにより、両方のシステム モジュールが実行されているのを確認されます。

デバイスで実行されているモジュールを確認するには、Azure portal でデバイスの詳細を確認するか、Visual Studio または Visual Studio Code でデバイスの状態を表示するか、デバイス自体で `iotedge list` コマンドを実行します。

**edgeAgent** モジュールが、**edgeHub** モジュールなしで実行されている場合は、次の手順を使用します：

1. Azure Portal で、お使いの IoT ハブに移動します。

2. **IoT Edge** に移動し、ゲートウェイとして使用する IoT Edge デバイスを選択します。

3. **[Set Modules] \(モジュールの設定)** を選択します。

4. **[次へ]** を選択します。

5. **[ルートの指定]** ページで、すべてのモジュールから IoT Hub にすべてのメッセージを送信する既定のルートを用意しておく必要があります。 そうしていない場合は、次のコードを追加し、 **[次へ]** を選択します。

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. **[Review template]\(テンプレートのレビュー\)** ページで、 **[送信]** を選びます。

## <a name="open-ports-on-gateway-device"></a>ゲートウェイ デバイスで開いているポート

IoT Hub とのすべての通信は送信接続を介して行われるため、Standard IoT Edge デバイスが機能するために受信接続は必要ありません。 ゲートウェイ デバイスは、ダウンストリーム デバイスからメッセージを受信する必要があるため、異なります。 ダウンストリーム デバイスとゲートウェイ デバイスの間にファイアウォールがある場合は、ファイアウォール経由でも通信が可能である必要があります。

ゲートウェイ シナリオが機能するには、IoT Edge ハブでサポートされているプロトコルの少なくとも 1 つが、ダウンストリーム デバイスからの受信トラフィック用に開かれている必要があります。 サポートされているプロトコルは、MQTT、AMQP、HTTPS、MQTT over WebSockets、および AMQP over WebSockets です。

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>ダウンストリーム デバイスからのメッセージのルーティング

IoT Edge ランタイムでは、モジュールによって送信されたメッセージと同じように、ダウンストリーム デバイスから送信されたメッセージをルーティングできます。 この機能により、クラウドにデータを送信する前に、ゲートウェイで実行されているモジュールの分析を実行することができます。

現時点では、ダウンストリーム デバイスによって送信されたメッセージをルーティングするには、モジュールによって送信されたメッセージからそれらを区別します。 モジュールによって送信されたすべてのメッセージには **connectionModuleId** という名前のシステム プロパティが含まれますが、ダウンストリーム デバイスによって送信されたメッセージには含まれません。 ルートの WHERE 句を使用して、そのシステム プロパティが含まれるメッセージを除外することができます。

以下のルートは、任意のダウンストリーム デバイスから `ai_insights` という名前のモジュールにメッセージを送信し、次に `ai_insights` から IoT Hub へと送信する例です。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

メッセージのルーティングについて詳しくは、[モジュールのデプロイとルートの確立](./module-composition.md#declare-routes)に関する記事をご覧ください。

## <a name="enable-extended-offline-operation"></a>拡張オフライン操作の有効化

IoT Edge ランタイムの [v1.0.4 リリース](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)以降、ゲートウェイ デバイスとそれに接続しているダウンストリーム デバイスを、拡張オフライン操作用に構成できるようになりました。

この機能により、ローカル モジュールまたはダウンストリーム デバイスは、必要に応じて IoT Edge デバイスで再認証を行うことができ、IoT Hub から切断されていても、メッセージとメソッドを使用して相互に通信することができます。 詳細については、「[IoT Edge デバイス、モジュール、子デバイスの拡張オフライン機能について](offline-capabilities.md)」を参照してください。

拡張オフライン機能を有効にするには、IoT Edge ゲートウェイ デバイスとそれに接続するダウンストリーム デバイスとの間で親子関係を確立します。 これらの手順については、「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)」で詳しく説明されています。

## <a name="next-steps"></a>次のステップ

これで IoT Edge デバイスが透過的なゲートウェイとして機能するようになったので、ゲートウェイを信頼してメッセージを送信するようにダウンストリーム デバイスを構成する必要があります。 「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)」に進み、透過的なゲートウェイのシナリオを設定する手順について確認してください。
