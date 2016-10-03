<properties
 pageTitle="リモート監視ソリューションのデバイス情報メタデータ | Microsoft Azure"
 description="Azure IoT リモート監視の事前構成済みソリューションとそのアーキテクチャの説明です。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# リモート監視構成済みソリューションのデバイス情報メタデータ

Azure IoT Suite リモート監視構成済みソリューションは、デバイス メタデータを管理する手法を示します。この記事では、次のことがわかるように、このソリューションの手法について概説します。

- ソリューションが格納するデバイス メタデータ。
- ソリューションがデバイス メタデータを管理する方法。

## Context

リモート監視構成済みソリューションでは、対象のデバイスからクラウドにデータを送信できるようにする [Azure IoT Hub][lnk-iot-hub] を使用します。IoT Hub には、IoT Hub へのアクセスを制御するための[デバイス ID レジストリ][lnk-identity-registry]が含まれています。IoT Hub デバイス ID レジストリは、デバイス情報メタデータが格納される、リモート監視ソリューション固有の*デバイス レジストリ*とは異なります。リモート監視ソリューションでは、[DocumentDB][lnk-docdb] データベースを使用して、デバイス情報メタデータを格納するためのデバイス レジストリが実装されます。一般的な IoT ソリューションでのデバイス レジストリの役割については、『[Microsoft Azure IoT Reference Architecture (Microsoft Azure IoT リファレンス アーキテクチャ)][lnk-ref-arch]』で説明されています。

> [AZURE.NOTE] リモート監視構成済みソリューションは、デバイス ID レジストリとデバイス レジストリの同期状態を維持します。どちらのレジストリも、同一のデバイス ID を使用して、対象の IoT Hub に接続された各デバイスを一意に識別します。

[IoT Hub デバイス管理プレビュー][lnk-dm-preview]を使用すると、この記事で説明するデバイス情報管理機能に似た機能を IoT Hub に追加できます。現在、リモート監視ソリューションでは IoT Hub の一般公開 (GA) 機能のみを利用できます。

## デバイス情報メタデータ

デバイス レジストリ DocumentDB データベースに格納されるデバイス情報メタデータ JSON ドキュメントの構造は、次のようになっています。

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: これらのプロパティはデバイス自体によって書き込まれます。また、デバイスはこのデータに対する権限を持ちます。その他のデバイス プロパティの例としては、製造元、モデル番号、シリアル番号が挙げられます。
- **DeviceID**: 一意のデバイス ID。この値は、IoT Hub デバイス ID レジストリのものと同じです。
- **HubEnabledState**: IoT Hub のデバイスの状態。この値は、デバイスが初めて接続されるまでは **null** に設定されています。ソリューション ポータルでは、**null** の値は、デバイスが "登録されていますが、存在していない" ことを表します。
- **CreatedTime**: デバイスが作成された時刻。
- **DeviceState**: デバイスによって報告される状態。
- **UpdatedTime**: ソリューション ポータルによるデバイスの最終更新時刻。
- **SystemProperties**: システム プロパティは、ソリューション ポータルによって書き込まれます。デバイスはこれらのプロパティの情報を持ちません。システム プロパティの一例としては、サービスを管理する SIM 対応デバイスによってソリューションが承認され、そのデバイスに接続される場合の **ICCID** があります。
- **Commands**: デバイスでサポートされているコマンドの一覧。この情報は、デバイスからソリューションに提供されます。
- **CommandHistory**: リモート監視ソリューションによってデバイスに送信されたコマンドと、それらのコマンドの状態の一覧。
- **IsSimulatedDevice**: シミュレーションされたデバイスであるかどうかを示すフラグ。
- **id**: このデバイス ドキュメントの一意の DocumentDB 識別子。

> [AZURE.NOTE] デバイス情報には、デバイスから IoT Hub に送信されたテレメトリを示すメタデータを含めることもできます。リモート監視ソリューションでは、このテレメトリ メタデータを使用して、ダッシュボードでの[動的テレメトリ][lnk-dynamic-telemetry]の表示方法をカスタマイズできます。

## ライフサイクル

ソリューション ポータルで初めてデバイスを作成するとき、先ほどのようにソリューションによってデバイス レジストリのエントリが作成されます。当初は情報の多くが消去されており、**HubEnabledState** は **null** に設定されています。またこの時点で、デバイス ID レジストリのデバイスのエントリも作成されます。これにより、デバイスが IoT Hub での認証に使用するキーが生成されます。

初めてソリューションに接続するとき、デバイスはデバイス情報メッセージを送信します。このデバイス情報メッセージには、デバイスの製造元、モデル番号、シリアル番号などのデバイス プロパティが含まれています。また、すべてのコマンド パラメーターの情報が付属した、デバイスでサポートされているコマンドの一覧も含まれています。このメッセージを受信すると、ソリューションはデバイス レジストリ内のデバイス情報メタデータを更新します。

### ソリューション ポータルでのデバイス情報の表示と編集

ソリューション ポータルのデバイス一覧には、**[状態]**、**[DeviceId]**、**[製造元]**、**[モデル番号]**、**[シリアル番号]**、**[ファームウェア]**、**[プラットフォーム]**、**[プロセッサ]**、**[インストールされている RAM]** の各プロパティが、列として表示されています。**[緯度]** と **[経度]** のデバイス プロパティによって、ダッシュボードの Bing 地図内での位置が定まります。

![Device list][img-device-list]

ソリューション ポータルで **[デバイスの詳細]** ウィンドウの **[編集]** をクリックすると、これらのプロパティをすべて編集できます。これらのプロパティを編集すると、DocumentDB データベース内のデバイスのレコードが更新されます。ただし、更新されたデバイス情報メッセージをデバイスから送信する場合は、ソリューション ポータルで加えた変更が上書きされます。ソリューション ポータルでは、**DeviceId**、**Hostname**、**HubEnabledState**、**CreatedTime**、**DeviceState**、**UpdatedTime** の各プロパティは編集できません。これらのプロパティに対する権限を持つのがデバイスのみであるためです。

![Device edit][img-device-edit]

ソリューション ポータルを使用して、ソリューションからデバイスを削除できます。デバイスを削除すると、ソリューションによってソリューションのデバイス レジストリからデバイス情報メタデータが削除され、IoT Hub デバイス ID レジストリのデバイス エントリが削除されます。デバイスを削除するには、無効にしておく必要があります。

![Device remove][img-device-remove]

## デバイス情報メッセージの処理

デバイスによって送信されるデバイス情報メッセージは、テレメトリ メッセージとは異なります。デバイス情報メッセージには、デバイス プロパティ、デバイスが応答できるコマンド、コマンド履歴などの情報が含まれます。IoT Hub 自体は、デバイス情報メッセージに含まれたメタデータを把握しません。このメッセージは、デバイスからクラウドへのメッセージを処理するのと同じ方法で処理されます。リモート監視ソリューションでは、[Azure Stream Analytics][lnk-stream-analytics] \(ASA) ジョブが IoT Hub からのメッセージを読み取ります。**DeviceInfo** Stream Analytics ジョブは、**"ObjectType": "DeviceInfo"** が含まれたメッセージをフィルター処理し、Web ジョブで実行される **EventProcessorHost** ホスト インスタンスにそれらを転送します。**EventProcessorHost** インスタンス内のロジックは、デバイス ID を使用して特定のデバイスの DocumentDB レコードを特定し、そのレコードを更新します。デバイス レジストリのレコードには現在、デバイス プロパティ、コマンド、コマンド履歴などの情報が含まれています。

> [AZURE.NOTE] デバイス情報メッセージは、標準的なデバイスからクラウドへのメッセージです。このソリューションでは、ASA クエリを使用してデバイス情報メッセージとテレメトリ メッセージが区別されます。

## デバイス情報レコードの例

リモート監視構成済みソリューションでは、2 種類のデバイス情報レコードが使用されます。ソリューションと共にデプロイされた、シミュレートされたデバイスのレコードと、ソリューションに接続するカスタム デバイスのレコードです。

### シミュレートされたデバイス

次の例は、シミュレートされたデバイスの JSON デバイス情報レコードを示します。このレコードには、デバイスが **DeviceInfo** メッセージを IoT Hub に送信したことを示す **UpdatedTime** の値セットがあります。レコードには一般的なデバイス プロパティがいくつか含まれており、シミュレートされたデバイスがサポートする 6 つのコマンドが定義されています。また、**IsSimulatedDevice** フラグが **1** に設定されています。

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### カスタム デバイス

次の例は、カスタム デバイスの JSON デバイス情報レコードを示します。ここでは、**IsSimulatedDevice** フラグが **0** に設定されています。このカスタム デバイスが 2 つのコマンドをサポートしていることと、ソリューション ポータルからデバイスに **SetTemperature** コマンドが送信されたことがわかります。

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

次の例は、デバイス情報メタデータを更新するためにデバイスから送信された JSON **DeviceInfo** メッセージを示しています。

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## 次のステップ

構成済みのソリューションをカスタマイズする方法を学習し終えたので、次のリンク先で IoT Suite の構成済みのソリューションのその他の機能のいくつかについて調べることができます。

- [予測的なメンテナンスの構成済みソリューションの概要][lnk-predictive-overview]
- [IoT スイートに関してよく寄せられる質問][lnk-faq]
- [IoT の徹底的なセキュリティ][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

<!---HONumber=AcomDC_0914_2016-->