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
 ms.date="09/28/2015"
 ms.author="dobett"/>

# IoT Hub へのアクセスの構成と管理

この記事には、ネットワーク インフラストラクチャ上で IoT デバイスが IoT Hub と通信する環境を IT プロフェッショナルが構成する際に役立つ情報が記載されています。

## ネットワーク接続

デバイスは、AMQP または HTTPS のいずれかのプロトコルを使用して Azure の IoT Hub と通信します。通常、プロトコルの選択は、ソリューションの特定の要件によって左右されます。次の表に、デバイスが特定のプロトコルを使用できるようにするために開く必要がある送信ポートを示します。

| プロトコル | ポート |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |

Azure リージョンに IoT Hub を作成すると、そのハブはハブの存続期間中同じ IP アドレスを保持します。ただし、障害復旧シナリオでは、Microsoft によって IoT Hub が別のスケール ユニットに移動されると、新しい IP アドレスが割り当てられます。

## IoT Hub とセキュリティ

IoT Hub に登録されているデバイスのみが、その IoT Hub と通信できます。登録されたデバイスには *DeviceConnect* アクセス許可を付与する必要があります。デバイスは、作成する各要求にデバイス固有の ID をカプセル化するトークンを含めることにより、デバイスが識別されるようにします。ハブはトークンの有効性と、デバイスがブラックリストに入れられていないか (*DeviceConnect* アクセス許可が取り消されていないか) を確認します。

IoT Hub のその他の管理エンドポイントへのアクセスも、*iothubowner*、*service*、*registryRead*、および *registryReadWrite* という一連のアクセス許可で制御されます。IoT Hub に接続しているすべてのクライアント管理のアプリケーションには、適切なアクセス許可を持つトークンが含まれている必要があります。

## 次のステップ

この記事には、開発環境およびテスト環境を構成する IT プロフェッショナルと開発者向けの具体的な情報が含まれています。Azure IoT Hub サービスについてさらに学習するには、次のリンクを使用してください。

- [What is Azure IoT Hub? (Azure IoT Hub とは)][lnk-iothub]
- ["Security" in the Azure IoT Hub developer guide (「Azure IoT Hub 開発者ガイド」の「セキュリティ」)][lnk-devguide]セクションには、IoT Hub でのトークンおよびアクセス許可システムに関する詳細な情報が記載されています。
- 「[Manage IoT Hub through the Azure Portal (Azure ポータルを使用した IoT Hub の管理)][lnk-manage-portal]」には、Azure ポータルを使用して IoT Hub を管理する方法が記載されています。

[lnk-iothub]: iot-hub-what-is-iot-hub.md
[lnk-devguide]: iot-hub-devguide.md#security
[lnk-manage-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_1203_2015-->