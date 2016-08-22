<properties
 pageTitle="IT プロフェッショナル向けの Azure IoT Hub の情報 | Microsoft Azure"
 description="IT プロフェッショナルがポート要件やセキュリティ バックグラウンドなど、Azure IoT Hub を使用するときに役立つ情報です。"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# IoT Hub へのアクセスの構成と管理

この記事には、ネットワーク インフラストラクチャ上で IoT デバイスが IoT Hub と通信する環境を IT プロフェッショナルが構成する際に役立つ情報が記載されています。

## ネットワーク接続

デバイスは、さまざまなプロトコルを使用して Azure の IoT Hub と通信できます。通常、プロトコルの選択は、ソリューションの特定の要件によって左右されます。次の表に、デバイスが特定のプロトコルを使用できるようにするために開く必要がある送信ポートを示します。

| プロトコル | ポート |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |
| AMQP over WebSocket | 443 |
| MQTT | 8883 |
| LWM2M (デバイス管理) | 5684 |

Azure リージョンに IoT Hub を作成すると、そのハブはハブの存続期間中同じ IP アドレスを保持します。ただし、サービス品質を維持するため、Microsoft によって IoT Hub が別のスケール ユニットに移動されると、新しい IP アドレスが割り当てられます。

## IoT Hub とセキュリティ

IoT Hub に登録されているデバイスのみが、その IoT Hub と通信できます。登録されたデバイスには *DeviceConnect* アクセス許可を付与する必要があります。デバイスは、作成する各要求にデバイス固有の ID をカプセル化するトークンを含めることにより、デバイスが識別されるようにします。ハブはトークンの有効性と、デバイスがブラックリストに入れられていないか (*DeviceConnect* アクセス許可が取り消されていないか) を確認します。IoT Hub でサポートされるトークンの詳細については、「[IoT Hub セキュリティ トークンと X.509 証明書を使用する][lnk-tokens]」を参照してください。

IoT Hub のその他の管理エンドポイントへのアクセスも、*iothubowner*、*service*、*registryRead*、および *registryReadWrite* という一連のアクセス許可で制御されます。IoT Hub に接続しているすべてのクライアント管理のアプリケーションには、適切なアクセス許可を持つトークンが含まれている必要があります。

## 次のステップ

この記事には、開発環境およびテスト環境を構成する IT プロフェッショナルと開発者向けの具体的な情報が含まれています。[「Azure IoT Hub 開発者ガイド」の「セキュリティ」セクション][lnk-devguide]には、IoT Hub でのトークンおよびアクセス許可システムに関する詳細な情報が記載されています。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [開発者ガイド][lnk-devguide]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

[lnk-devguide]: iot-hub-devguide.md#security

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-tokens]: iot-hub-sas-tokens.md

<!---HONumber=AcomDC_0810_2016-->