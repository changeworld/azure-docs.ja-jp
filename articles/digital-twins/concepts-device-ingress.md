---
title: Azure Digital Twins でのデバイスの接続とテレメトリのイングレス | Microsoft Docs
description: Azure Digital Twins でのデバイスのオンボードの概要です
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 7eddea7e0d57b89318232da6f086bbe2f649ee77
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211929"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>デバイスの接続性とテレメトリの受信

デバイスとセンサーによって送信されるテレメトリ データが、IoT ソリューションのバックボーンを形成します。 そのため、これらのさまざまなリソースを表し、場所のコンテキスト内でそれらを管理することは、IoT アプリの開発における主な考慮事項です。 Azure Digital Twins は、デバイスやセンサーと空間インテリジェンス グラフを統合することにより、IoT ソリューションの開発を簡単にします。

最初に、IoT Hub リソースを空間グラフのルートに作成し、ルート空間の下にあるすべてのデバイスがメッセージを送信できるようにする必要があります。 IoT ハブを作成し、センサーを備えたデバイスを Digital Twins インスタンスに登録すると、デバイスは [Azure IoT Device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks) 経由で Digital Twins サービスへのデータの送信を始めることができます。

デバイスのオンボードの詳細な手順については、[Digital Twins のデプロイと構成に関するチュートリアル](tutorial-facilities-setup.md)のページをご覧ください。 手順の概要は次のとおりです。

- [Azure portal](https://portal.azure.com) から Azure Digital Twins インスタンスをデプロイします
- グラフに空間を作成します
- IoT Hub リソースを作成し、グラフ内の空間に割り当てます
- グラフでデバイスとセンサーを作成し、上の手順で作成した空間に割り当てます
- 条件に基づいてテレメトリ メッセージをフィルター処理するマッチャーを作成します
- テレメトリ メッセージのカスタム処理用の[ユーザー定義関数](concepts-user-defined-functions.md)を作成し、グラフの空間に割り当てます
- ユーザー定義関数がグラフ データにアクセスできるように、ロールを割り当てます
- Digital Twins Management API から IoT Hub デバイスの接続文字列を取得します
- Azure IoT Device SDK を使用してデバイスにデバイス接続文字列を構成します

以下では、Digital Twins Management API から IoT Hub デバイスの接続文字列を取得する方法と、IoT Hub のテレメトリ メッセージ形式を使用してセンサー ベースのテレメトリを送信する方法について説明します。 Digital Twins では、データが適切な空間コンテキスト内で処理されてルーティングされるように、受信したテレメトリの各部分が空間グラフ内のセンサーと関連付けられている必要があります。

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Management API から IoT Hub デバイスの接続文字列を取得する

`includes=ConnectionString` パラメーターを指定して Device API で GET 呼び出しを行い、IoT Hub デバイスの接続文字列を取得します。 デバイスの GUID またはハードウェアの ID でフィルター処理を行い、特定のデバイスを検索できます。

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| カスタム属性の名前 | 置換後の文字列 |
| --- | --- |
| *yourManagementApiUrl* | Management API の完全な URL パス |
| *yourDeviceGuid* | デバイス ID |
| *yourDeviceHardwareId* | デバイスのハードウェア ID |

応答のペイロードで、デバイスの *connectionString* プロパティをコピーします。これは、Azure IoT Device SDK を呼び出して Azure Digital Twins にデータを送信するときに使用します。

## <a name="device-to-cloud-message"></a>device-to-cloud メッセージ

ソリューションのニーズに合わせて、デバイスのメッセージ形式とペイロードをカスタマイズできます。 [Azure IoT Device Client Message クラスの Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) によってサポートされるバイト配列またはストリームにシリアル化できる任意のデータ コントラクトを使用することができます。 対応するユーザー定義関数でデータ コントラクトをデコードする限り、メッセージでは任意のカスタム バイナリ形式を使用できます。 device-to-cloud メッセージの唯一の要件は、メッセージが処理エンジンに適切にルーティングされるように、一連のプロパティ (下記参照) を維持することです。

### <a name="telemetry-properties"></a>テレメトリのプロパティ

**Message** のペイロードの内容はサイズが最大 256 KB の任意のデータですが、予期される [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) についてはいくつかの要件があります。 以下の手順では、システムでサポートされる必須および省略可能のプロパティが反映されます。

| プロパティ名 | 値 | 必須 | 説明 |
|---|---|---|---|
| *DigitalTwins-Telemetry* | 1.0 | [はい] | システムに対してメッセージを示す定数値 |
| *DigitalTwins-SensorHardwareId* | `string(72)` | [はい] | **Message** を送信しているセンサーの一意識別子。 この値は、それを処理するシステムでのオブジェクトの **HardwareId** プロパティと一致する必要があります。 たとえば、`00FF0643BE88-CO2` のように指定します。 |
| *CreationTimeUtc* | `string` | いいえ  | ペイロードのサンプリング日時を示す [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 形式の日付文字列。 たとえば、`2018-09-20T07:35:00.8587882-07:00` のように指定します。 |
| *CorrelationId* | `string` | いいえ  | システム全体でイベントをトレースするために使用できる UUID。 たとえば、`cec16751-ab27-405d-8fe6-c68e1412ce1f` のように指定します。

### <a name="sending-your-message-to-digital-twins"></a>Digital Twins へのメッセージの送信

DeviceClient の [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) または [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) の呼び出しを使用して、メッセージを Digital Twins サービスに送信します。

## <a name="next-steps"></a>次の手順

Azure Digital Twins のデータ処理およびユーザー定義関数の機能については、[Azure Digital Twins でのデータ処理とユーザー定義関数](concepts-user-defined-functions.md)に関するページをご覧ください。

