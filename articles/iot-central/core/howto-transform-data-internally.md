---
title: Azure IoT Central 内のデータの変換 | Microsoft Docs
description: IoT デバイスから送信されるデータにはさまざまな形式があり、一部は変換が必要です。 この記事では、エクスポートする前に IoT Central 内のデータを変換する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 10/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c886fd71399f84f04e7ac83ff24059dd658f0a4b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579376"
---
# <a name="transform-data-inside-your-iot-central-application-for-export"></a>IoT Central アプリケーション内のデータをエクスポート用に変換する

:::image type="content" source="media/howto-transform-data-internally/transformations.png" alt-text="データ エクスポート変換オプションを示す図。" border="false":::

IoT デバイスから送信されるデータにはさまざまな形式があります。 IoT ソリューションでデバイス データを使用するには、デバイス データを他のサービスにエクスポートする前に変換することが必要になる場合があります。

この記事では、IoT Central アプリケーションでデータ エクスポート定義の一部としてデバイス データを変換する方法について説明します。

IoT Central データ エクスポート定義の変換により、デバイス データを変換先にエクスポートする前に、その形式と構造を操作できます。 エクスポート定義では、変換先別に変換を指定できます。 各メッセージはその変換を経ることで、出力レコードが作成され、それが変換先にエクスポートされます。

変換を使用することで、JSON ペイロードの再構築、フィールド名の変更、フィールドのフィルター処理、テレメトリ値に対する単純な計算の実行ができます。 たとえば、変換を使用して、Azure Data Explorer テーブルなどの変換先のスキーマに一致する表形式にメッセージを変換します。

## <a name="add-a-transformation"></a>変換を追加する

データ エクスポートの変換先に変換を追加するには、次のスクリーンショットに示すように、 **[+ 変換]** を選択します。

:::image type="content" source="media/howto-transform-data-internally/add-transformation.png" alt-text="変換先に変換を追加する方法を示すスクリーンショット。":::

変換は **[データ変換]** パネルで指定できます。 **[1. Add your input message]\(1. 入力メッセージの追加\)** セクションには、変換するサンプル メッセージを入力できます。 デバイス テンプレートを選択して、サンプル メッセージを生成することもできます。 **[2. Build transformation query]\(2. 変換クエリの作成\)** セクションでは、入力メッセージを変換するクエリを入力できます。 **[3. Preview output messages(s)]\(3. 出力メッセージのプレビュー\)** セクションには、変換の結果が表示されます。

:::image type="content" source="media/howto-transform-data-internally/transformation-editor.png" alt-text="IoT Central の変換エディターのスクリーンショット。":::

> [!TIP]
> 入力メッセージの形式がわからない場合は、クエリとして `.` を使用して、Webhook などの変換先にメッセージをそのままエクスポートします。 その後、Webhook が受信したメッセージを* **[1. 入力メッセージの追加]** に貼り付けます。 次に、このメッセージを処理して必要な出力形式にする変換クエリを作成します。

## <a name="build-a-transformation-query"></a>変換クエリを作成する

変換エンジンは、オープンソースの [JQ](https://stedolan.github.io/jq/) JSON プロセッサを使用して、JSON ペイロードを再構築してフォーマットします。 変換を指定するには、JQ の組み込みフィルター、関数、機能などを使用できる JQ クエリを作成します。 いくつかのクエリの例については、「[変換クエリの例](#example-transformation-queries)」をご覧ください。 JQ クエリの作成の詳細については、[JQ のマニュアル](https://stedolan.github.io/jq/manual/)をご覧ください。

## <a name="pre-transformation-message-structure"></a>変換前のメッセージ構造

IoT Central から次のデータ ストリームをエクスポートできます。テレメトリ、プロパティの変更、デバイス接続イベント、デバイス ライフサイクル イベント、デバイス テンプレートのライフサイクル イベント。 各種類のデータには、テレメトリ値、アプリケーション情報、デバイス メタデータ、プロパティ値などの情報を含む特定の構造があります。

次の例は、テレメトリ メッセージの形を示しています。 このデータはすべて、変換に使用できます。 メッセージの構造は、他のメッセージ型と似ていますが、型固有のフィールドもあります。 **[入力メッセージの追加]** 機能を使用すると、アプリケーションのデバイス テンプレートに基づいてサンプル メッセージを生成できます。

```json
{
  "applicationId": "93d68c98-9a22-4b28-94d1-06625d4c3d0f",
  "device": {
    "id": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "name": "Scripted Device - 31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "cloudProperties": [],
    "properties": {
      "reported": [
        {
          "id": "urn:smartKneeBrace:Smart_Vitals_Patch_wr:FirmwareVersion:1",
          "name": "FirmwareVersion",
          "value": 1.0
        }
      ]
    },
    "templateId": "urn:sbq3croo:modelDefinition:nf7st1wn3",
    "templateName": "Smart Knee Brace"
  },
  "telemetry": [
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
        "name": "Acceleration",
        "value": {
          "x": 19.212770659918583,
          "y": 20.596296675217335,
          "z": 54.04859440697045
        }
      },
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
        "name": "RangeOfMotion",
        "value": 110
      }
  ],
  "enqueuedTime": "2021-03-23T19:55:56.971Z",
  "enrichments": {
      "your-enrichment-key": "enrichment-value"
  },
  "messageProperties": {
      "prop1": "prop-value"
  },
  "messageSource": "telemetry"
}
```

> [!TIP]
> IoT Central アプリケーション UI の **[入力メッセージの追加]** 機能を使用して、プロパティの変更など、他のデータ エクスポートの種類向けのサンプル メッセージ構造を表示します。

## <a name="example-transformation-queries"></a>変換クエリの例

次のクエリの例では、前のセクションで示したテレメトリ メッセージを使用します。

**例 1**: 次の JQ クエリは、入力メッセージからテレメトリの各部分を個別の出力メッセージとして出力します。

```jq
.telemetry[]
```

JSON 出力:

```json
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
  "name": "Acceleration",
  "value": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  }
},
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
  "name": "RangeOfMotion",
  "value": 110
}
```

> [!TIP]
> テレメトリの種類の配列を使用して出力を 1 つのメッセージに変更するには、クエリ `.telemetry` を使用します。

**例 2**: 次の JQ クエリは、入力テレメトリ配列を、キーとしてテレメトリ名を持つオブジェクトに変換します。

```jq
.telemetry | map({ key: .name, value: .value }) | from_entries
```

JSON 出力:

```json
{
  "Acceleration": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  },
  "RangeOfMotion": 110
}
```

**例 3**: 次の JQ クエリでは、 **RangeOfMotion** テレメトリ値を検索し、式 `rad = degree * pi / 180` を使用して次数からラジアンに変換します。 このクエリでは、`iotc` モジュールをインポートして使用する方法も示しています。

```jq
import "iotc" as iotc;
{
  rangeOfMotion: (
    .telemetry
    | iotc::find(.name == "RangeOfMotion").value
    | . * 3.14159265358979323846 / 180
  )
}
```

JSON 出力:

```json
{
  "rangeOfMotion": 1.9198621771937625
}
```

**例 4**: 入力メッセージを表形式で操作するために、エクスポートされた各メッセージを 1 つ以上の *行* にマップできます。 行の出力は、列名がキーで、列値が値である JSON オブジェクトとして論理的に表されます。

```json
{
    "<column 1 name>": "<column 1 value>",
    "<column 2 name>": "<column 2 value>",
    ...
}
```

> [!TIP]
> Azure Data Explorer にエクスポートする場合は、表形式を使用します。

次の JQ クエリは、さまざまなデバイスの **rangeOfMotion** テレメトリを格納するテーブルに行を書き込みます。 このクエリは、次の列を含むテーブルにデバイス ID、エンキューされた時刻、モーションの範囲などをマップします。

```jq
import "iotc" as iotc;
{
    deviceId: .deviceId,
    timestamp: .enqueuedTime,
    rangeOfMotion: .telemetry | iotc::find(.name == "RangeOfMotion").value
}
```

JSON 形式の出力:

```json
{
  "deviceId": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
  "timestamp": "2021-03-23T19:55:56.971Z",
  "rangeOfMotion": 110
}
```

## <a name="iot-central-module"></a>IoT Central モジュール

JQ モジュールは、カスタム関数のコレクションです。 変換クエリの一部として、関数を含む組み込み IoT Central 固有モジュールをインポートして、クエリの作成をさらに簡単にすることができます。 IoT Central モジュールをインポートするには、次のディレクティブを使用します。

```jq
import "iotc" as iotc;
```

IoT Central モジュールには、次の関数が含まれています。

`find(expression)`: `find` 関数を使用すると、テレメトリ値やプロパティ エントリなどの特定の配列要素をペイロードで検索できます。 関数入力は配列であり、パラメーターは配列内の各要素に対して実行する JQ フィルターを定義します。 関数は、フィルターが true と評価した各配列要素を返します。

たとえば、`RangeOfMotion` という名前の特定のテレメトリ値を検索するには、次のように指定します。

```jq
.telemetry | iotc::find(.name == "RangeOfMotion")
```

## <a name="scenarios"></a>シナリオ

次のシナリオでは、変換機能を使用して、特定の変換先のデバイス データ形式をカスタマイズします。

### <a name="scenario-1-export-device-data-to-azure-data-explorer"></a>シナリオ 1: Azure Data Explorer にデバイス データをエクスポートする

このシナリオでは、Azure Data Explorer の固定スキーマに一致するようにデバイス データを変換します。各テレメトリ値はテーブルの列として表示され、各行は 1 つのメッセージを表します。 次に例を示します。

| deviceId | Timestamp | T1 | T2 |T3 |
| :------------- | :---------- | :----------- | :---------- | :----------- |
| "31edabe6-e0b9-4c83-b0df-d12e95745b9f" |"2021-03-23T19:55:56.971Z | 1.18898 | 1.434709 | 2.97008 |

このテーブルと互換性のあるデータをエクスポートするには、エクスポートされた各メッセージが次のオブジェクトのように表示される必要があります。 オブジェクトは 1 つの行を表し、キーは列名、値は各列に配置する値です。

```json
{
    "Timestamp": <value-of-Timestamp>,
    "DeviceId": <value-of-deviceId>,
    "T1": <value-of-T1>,
    "T2": <value-of-T2>,
    "T3": <value-of-T3>,
}
```

このシナリオでは、デバイスは `t1` 、`t2`、`t3` のテレメトリ値を、次の例のような入力メッセージで送信します。

```json
{
  "applicationId": "c57fe8d9-d15d-4659-9814-d3cc38ca9e1b",
  "enqueuedTime": "1933-01-26T03:10:44.480001324Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t1;1",
      "name": "t1",
      "value": 1.1889838348731093e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t2;1",
      "name": "t2",
      "value": 1.4347093391531383e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t3;1",
      "name": "t3",
      "value": 2.9700885230380616e+307
    }
  ],
  "device": {
    "id": "oozrnl1zs857",
    "name": "haptic alarm",
    "templateId": "dtmi:modelDefinition:nhhbjotee:qytxnp8hi",
    "templateName": "hapticsensors",
    "properties": {
      "reported": []
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": false,
    "blocked": false,
    "provisioned": true
  }
}
```

次の JQ クエリでは `T1`、 `T2`、`T3` テレメトリ値、`Timestamp` および `deviceId` を、Azure Data Explorer テーブルスキーマと一致するキーと値のペアを持つメッセージとして出力します。

```jq
import "iotc" as iotc;
{
  deviceId: .device.id,
  Timestamp: .enqueuedTime,
  T1: .telemetry | iotc::find(.name == "t1").value,
  T2: .telemetry | iotc::find(.name == "t2").value,
  T3: .telemetry | iotc::find(.name == "t3").value,
}
```

JSON 出力:

```json
{
  "T1": 1.1889838348731093e+308,
  "T2": 1.4347093391531383e+308,
  "T3": 2.9700885230380616e+307,
  "Timestamp": "1933-01-26T03:10:44.480001324Z",
  "deviceId": "oozrnl1zs857"
}
```

Azure Data Explorer クラスターとデータベースをエクスポート先として追加する方法の詳細については、 「[Azure Storage Explorer の宛先を作成する](howto-export-data.md#create-an-azure-data-explorer-destination)」をご覧ください。

### <a name="scenario-2-breaking-apart-a-telemetry-array"></a>シナリオ 2: テレメトリ配列を分割する

このシナリオでは、デバイスは次のテレメトリの配列を1つのメッセージで送信します。

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:sample1:data;1",
      "name": "data",
      "value": [
        {
          "id": "subdevice1",
          "values": {
              "running": true,
              "cycleCount": 2315
          }
        },
        {
          "id": "subdevice2",
          "values": {
              "running": false,
              "cycleCount": 824567
          }
        }
      ]
    },
    {
      "id": "dtmi:sample1:parentStatus;1",
      "name": "parentStatus",
      "value": "healthy"
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:sample1:prop;1",
          "name": "Connectivity",
          "value": "Tenetur ut quasi minus ratione voluptatem."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

このデバイス データを次のテーブル スキーマと一致するように変換します。

| cycleCount | deviceId | enqueuedTime | parentStatus | 実行中 | subdeviceId |
| :--------- | :------- | :----------- | :----------- | :------ | :---------- |
| 2315   | "9xwhr7khkfri" | "1909-10-10T07:11:56.078161042Z" | "healthy" | true | "subdevice1" |
| 824567 | "9xwhr7khkfri" | "1909-10-10T07:11:56.078161042Z" | "healthy" | false | "subdevice2" |

次の JQ クエリでは、メッセージ内のサブデバイス エントリごとに個別の出力メッセージが作成され、基本メッセージと親デバイスからの一般的ないくつかの情報が組み込まれます。 このクエリでは、出力がフラット化され、1 つのメッセージとして着信したデータ内の論理除算が分離されます。

```jq
import "iotc" as iotc;
{
    enqueuedTime: .enqueuedTime,
    deviceId: .device.id,
    parentStatus: .telemetry | iotc::find(.name == "parentStatus").value
} + (
    .telemetry
    | iotc::find(.name == "data").value[]
    | {
        subdeviceId: .id,
        running: .values.running,
        cycleCount: .values.cycleCount
    }
)
```

JSON 出力:

```json
{
    "cycleCount": 2315,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": true,
    "subdeviceId": "subdevice1"
},
{
    "cycleCount": 824567,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": false,
    "subdeviceId": "subdevice2"
}
```

### <a name="scenario-3-power-bi-streaming"></a>シナリオ 3: Power BI ストリーミング

Power BI のリアルタイム ストリーミング機能を使用すると、低待機時間でリアルタイムに更新されるダッシュボードにデータを表示できます。 詳細については、「[Power BI のリアルタイム ストリーミング](/power-bi/connect-data/service-real-time-streaming)」をご覧ください。

IoT Central を Power BI ストリーミングで使用するには、要求本文を特定の形式で送信する Webhook エクスポートを設定します。 この例では、次のスキーマで Power BI ストリーミング データセットがある場合を想定しています。

```json
[
  {
    "bloodPressureDiastolic": 161438124,
    "bloodPressureSystolic": -966387879,
    "deviceId": "9xwhr7khkfri",
    "deviceName": "wireless port",
    "heartRate": -633994413,
    "heartRateVariability": -37514094,
    "respiratoryRate": 1582211310,
    "timestamp": "1909-10-10T07:11:56.078161042Z"
  }
]
```

Webhook エクスポート先を作成するには、Power BI ストリーミング データセット用の REST API URL エンドポイントが必要です。

このシナリオでは、デバイスは次の例のようなテレメトリ メッセージを送信します。

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
      "name": "HeartRate",
      "value": -633994413
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
      "name": "RespiratoryRate",
      "value": 1582211310
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
      "name": "HeartRateVariability",
      "value": -37514094
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
      "name": "BodyTemperature",
      "value": 5.323322666478241e+307
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
      "name": "FallDetection",
      "value": "Earum est nobis at voluptas id qui."
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
      "name": "BloodPressure",
      "value": {
        "Diastolic": 161438124,
        "Systolic": -966387879
      }
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
          "name": "DeviceStatus",
          "value": "Id optio iste vero et neque sit."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

次の JQ クエリは、入力メッセージを、Webhook が Power BI ストリーミング データセットに送信するのに適した形式に変換します。 この例には、特定のデバイス テンプレートのメッセージのみを出力するフィルター条件が含まれています。 データ エクスポート フィルター機能を使用して、デバイス テンプレートでフィルター処理できます。

```jq
import "iotc" as iotc;
if .device.templateId == "dtmi:hpzy1kfcbt2:umua7dplmbd" then 
    [{
        deviceId: .device.id,
        timestamp: .enqueuedTime,
        deviceName: .device.name,
        bloodPressureSystolic: .telemetry | iotc::find(.name == "BloodPressure").value.Systolic,
        bloodPressureDiastolic: .telemetry | iotc::find(.name == "BloodPressure").value.Diastolic,
        heartRate: .telemetry | iotc::find(.name == "HeartRate").value,
        heartRateVariability: .telemetry | iotc::find(.name == "HeartRateVariability").value,
        respiratoryRate: .telemetry | iotc::find(.name == "RespiratoryRate").value
    }]
else
    empty
end
```

JSON 出力:

```json
{
  "bloodPressureDiastolic": 161438124,
  "bloodPressureSystolic": -966387879,
  "deviceId": "9xwhr7khkfri",
  "deviceName": "wireless port",
  "heartRate": -633994413,
  "heartRateVariability": -37514094,
  "respiratoryRate": 1582211310,
  "timestamp": "1909-10-10T07:11:56.078161042Z"
}
```

### <a name="scenario-4-export-data-to-azure-data-explorer-and-visualize-it-in-power-bi"></a>シナリオ 4: データを Azure Data Explorer にエクスポートして Power BI で視覚化する

このシナリオでは、データを Azure Data Explorer にエクスポートしてから、 コネクタを使用して、Power BI のデータを視覚化します。 Azure Data Explorer クラスターとデータベースをエクスポート先として追加する方法の詳細については、 「[Azure Storage Explorer の宛先を作成する](howto-export-data.md#create-an-azure-data-explorer-destination)」をご覧ください。

このシナリオでは、次のスキーマがある Azure Data Explorer テーブルを使用します。

``` kusto
.create table smartvitalspatch (
  EnqueuedTime:datetime,
  Message:string,
  Application:string,
  Device:string,
  Simulated:boolean,
  Template:string,
  Module:string,
  Component:string,
  Capability:string,
  Value:dynamic
)
```

このシナリオでは、デバイスは次の例のようなテレメトリ メッセージを送信します。

```json
{
    "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "messageSource": "telemetry",
    "telemetry": [
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
            "name": "HeartRate",
            "value": -633994413
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
            "name": "RespiratoryRate",
            "value": 1582211310
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
            "name": "HeartRateVariability",
            "value": -37514094
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
            "name": "BodyTemperature",
            "value": 5.323322666478241e+307
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
            "name": "FallDetection",
            "value": "Earum est nobis at voluptas id qui."
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
            "name": "BloodPressure",
            "value": {
                "Diastolic": 161438124,
                "Systolic": -966387879
            }
        }
    ],
    "device": {
        "id": "9xwhr7khkfri",
        "name": "wireless port",
        "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
        "templateName": "Smart Vitals Patch",
        "properties": {
            "reported": [
                {
                    "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
                    "name": "DeviceStatus",
                    "value": "Id optio iste vero et neque sit."
                }
            ]
        },
        "cloudProperties": [],
        "simulated": true,
        "approved": true,
        "blocked": false,
        "provisioned": false
    }
}
```

次の JQ クエリは、各テレメトリ値の個別の出力メッセージに入力メッセージを変換します。 この変換では、Azure Data Explorer テーブル スキーマに一致する出力が生成されます。 変換では、エンティティ属性値スキーマが使用されます。各行には 1 つのテレメトリ値が保持され、テレメトリの名前は同じ行内の個別の列の値です。

```jq
. as $in | .telemetry[] | {
  EnqueuedTime: $in.enqueuedTime,
  Message: $in.messageId,
  Application: $in.applicationId,
  Device: $in.device.id,
  Simulated: $in.device.simulated,
  Template: ($in.device.templateName // ""),
  Module: ($in.module // ""),
  Component: ($in.component // ""),
  Capability: .name,
  Value: .value
}
```

JSON 出力:

```json
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -633994413
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "RespiratoryRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 1582211310
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRateVariability",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -37514094
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BodyTemperature",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 5.323322666478241e+307
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "FallDetection",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": "Earum est nobis at voluptas id qui."
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BloodPressure",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": {
      "Diastolic": 161438124,
      "Systolic": -966387879
  }
}
```

出力データは、Azure Data Explorer クラスターにエクスポートされます。 エクスポートされたデータを Power BI で視覚化するには、次の手順を実行します。

1. Power BI アプリケーションをインストールします。 デスクトップ Power BI アプリケーションは、「[データから洞察を得て、Power BI Desktop でアクションに移す](https://powerbi.microsoft.com/desktop/)」からダウンロードできます。
1. Power BI Desktop の [IoT Central ADX Connector.pbit](https://github.com/Azure-Samples/iot-central-docs-samples/raw/master/azure-data-explorer-power-bi/IoT%20Central%20ADX%20Connector.pbit) ファイルを GitHub からダウンロードします。
1. Power BI Desktop アプリを使用して、前のステップでダウンロードした *IoT Central ADX Connector.pbit* ファイルを開きます。 ダイアログが表示されたら、事前にメモを作成した Azure Data Explorer クラスター、データベース、テーブルなどの情報を入力します。

これで、Power BI でデータを視覚化できます。

:::image type="content" source="media/howto-transform-data-internally/powerbi-report.png" alt-text="IoT Central からのデータを表示した Power BI レポートのスクリーンショット。" border="false":::

## <a name="next-steps"></a>次のステップ

IoT Central でデータを変換する方法を理解できたので、次のステップとして [IoT Central で分析を使用する方法](./howto-create-analytics.md)を学習することをお勧めします
