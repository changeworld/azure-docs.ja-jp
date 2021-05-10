---
title: IoT プラグ アンド プレイ デバイス モデルについて理解する | Microsoft Docs
description: IoT プラグ アンド プレイ デバイス用の Digital Twins Definition Language (DTDL) モデリング言語について説明します。 この記事では、プリミティブおよび複合データ型、コンポーネントと継承を使用する再使用パターン、セマンティック型について説明します。 この記事では、デバイス ツイン モデル識別子と、モデル作成のためのツール サポートの選択に関するガイダンスを提供します。
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 85888370106f34c723be4e47738114f7df33dcf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057475"
---
# <a name="iot-plug-and-play-modeling-guide"></a>IoT プラグ アンド プレイ モデリング ガイド

IoT プラグ アンド プレイの中核となるのは、デバイスの機能を IoT プラグ アンド プレイ対応アプリケーションに説明するデバイス _モデル_ です。 このモデルは、次の内容を定義するインターフェイスのセットとして構成されます。

- デバイスまたは他のエンティティの読み取り専用および書き込み可能な状態を表す _プロパティ_。 たとえば、デバイスのシリアル番号は読み取り専用のプロパティであり、サーモスタットでの目標温度は書き込み可能なプロパティとなります。
- デバイスによって出力されるデータを定義する "_テレメトリ_" フィールド。このデータはセンサー読み取り値の通常のストリーム、偶発的なエラー、または情報メッセージのいずれかです。
- デバイス上で実行できる関数または操作を記述した "_コマンド_"。 たとえば、コマンドでは、ゲートウェイを再起動したり、リモート カメラを使用して写真を撮影したりすることが可能です。

IoT プラグ アンド プレイがデバイス モデルをどのように使用するかについての詳細は、「[IoT プラグ アンド プレイ デバイス開発者ガイド](concepts-developer-guide-device.md)」と「[IoT プラグ アンド プレイ サービス開発者ガイド](concepts-developer-guide-service.md)」を参照してください。

モデルを定義するには、Digital Twins Definition Language (DTDL) を使用します。 DTDL が使用する JSON バリアントを [JSON-LD](https://json-ld.org/) と呼びます。 次のスニペットは、以下のようなサーモスタット デバイスのモデルを示しています。

- 一意のモデル ID: `dtmi:com:example:Thermostat;1` がある。
- 温度テレメトリを送信する。
- ターゲット温度を設定するための書き込み可能プロパティがある。
- 前回の再起動以降の最高温度を報告する読み取り専用プロパティがある。
- 一定期間の最高、最低、および平均の温度を要求するコマンドに応答する。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

サーモスタット モデルには 1 つのインターフェイスがあります。 この記事の後の例で示している、より複雑なモデルでは、コンポーネントと継承を使用しています。

この記事では独自のモデルを設計および作成する方法と、データ型、モデル構造、ツールなどのトピックについて説明しています。

詳しくは、[Digital Twins Definition Language v2](https://github.com/Azure/opendigitaltwins-dtdl) の仕様をご覧ください。

## <a name="model-structure"></a>モデルの構造

プロパティ、テレメトリ、コマンドがインターフェイスにグループ化されています。 このセクションでは、インターフェイスを使用して、単純および複雑な各モデルをコンポーネントと継承を使って記述する方法について説明します。

### <a name="model-ids"></a>モデル ID

すべてのインターフェイスには、一意のデジタル ツイン モデル識別子 (DTMI) があります。 複雑なモデルでは、DTMI を使用してコンポーネントを識別します。 アプリケーションは、デバイスが送信してリポジトリ内のモデル定義を見つけるための DTMI を使用できます。

DTMI は、[IoT プラグ アンド プレイ モデル リポジトリ](https://github.com/Azure/iot-plugandplay-models)で要求される次の命名規則に従っている必要があります。

- DTMI プレフィックスは `dtmi:` である。
- DTMI サフィックスは、モデルのバージョン番号 (例: `;2`) である。
- DTMI の本文は、モデルが格納されているモデル リポジトリ内のフォルダーとファイルにマップされている。 バージョン番号はファイル名の一部です。

たとえば、DTMI `dtmi:com:Example:Thermostat;2` によって識別されるモデルは *dtmi/com/example/thermostat-2.json* ファイルに格納されます。

次のスニペットは、一意の DTMI によるインターフェイス定義の概要を示しています。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>コンポーネントなし

単純なモデル (先ほど示したサーモスタットなど) では、埋め込みおよびカスケード コンポーネントを使用しません。 テレメトリ、プロパティ、コマンドがインターフェイスの `contents` ノードで定義されます。

次の例は、コンポーネントを使用しない単純なモデルの一部を示しています。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Azure IoT Explorer や IoT Central デバイス テンプレート デザイナーなどのツールは、サーモスタットのようなスタンドアロン インターフェイスに "_既定のコンポーネント_" のラベルを付けます。

次のスクリーンショットは、Azure IoT Explorer ツールでモデルがどのように表示されるかを示しています。

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Azure IoT Explorer の既定のコンポーネント":::

次のスクリーンショットは、IoT Central デバイス テンプレート デザイナーでモデルが既定のコンポーネントとして表示される様子を示しています。 **[ID の表示]** を選択して、モデルの DTMI を表示します。

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="IoT Central デザイナーでのサーモスタット モデルを示すスクリーンショット":::

モデル ID は、次のスクリーンショットに示すように、デバイス ツインのプロパティに格納されます。

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="デジタル ツインのプロパティのモデル ID":::

コンポーネントのない DTDL モデルは、1 つのセットのテレメトリ、プロパティ、およびコマンドを持つデバイスまたは IoT Edge モジュール用に簡略化されたモデルです。 コンポーネントを使用しないモデルでは、既存のデバイスまたはモジュールを IoT プラグ アンド プレイ デバイスまたはモジュールに簡単に移行することができます。コンポーネントを定義することなく、実際のデバイスまたはモジュールを記述する DTDL モデルを作成します。

> [!TIP]
> モジュールは、デバイス [モジュール](../iot-hub/iot-hub-devguide-module-twins.md)または [IoT Edge モジュール](../iot-edge/about-iot-edge.md)が可能です。

### <a name="reuse"></a>再利用する

インターフェイス定義を再利用するには、2 つの方法があります。 モデル内で複数のコンポーネントを使用して、他のインターフェイス定義を参照します。 継承を使用して、既存のインターフェイス定義を拡張します。

### <a name="multiple-components"></a>複数のコンポーネント

コンポーネントを使用すると、他のインターフェイスのアセンブリとしてモデル インターフェイスを作成できます。

たとえば、[Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) インターフェイスがモデルとして定義されているとします。 [Temperature Controller モデル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)を定義するときに、このインターフェイスを 1 つ以上のコンポーネントとして組み込むことができます。 次の例では、これらのコンポーネントを `thermostat1` および `thermostat2` と呼んでいます。

複数のコンポーネントを持つ DTDL モデルの場合は、2 つ以上のコンポーネント セクションがあります。 次のスニペットに示すように、各セクションでは `@type` に `Component` が設定され、スキーマを明示的に参照しています。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

このモデルでは、contents セクションに 3 つのコンポーネントが定義されています (2 つの `Thermostat` コンポーネントと 1 つの `DeviceInformation` コンポーネント)。 コンテンツ セクションには、プロパティ、テレメトリ、およびコマンドの定義も含まれています。

次のスクリーンショットは、このモデルが IoT Central でどのように表示されるかを示しています。 気温コントローラーのプロパティ、テレメトリ、およびコマンドの定義は、最上位の **既定のコンポーネント** に表示されます。 各サーモスタットのプロパティ、テレメトリ、およびコマンドの定義は、コンポーネント定義に表示されます。

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="IoT Central での温度コントローラー デバイス テンプレートを示すスクリーンショット。":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="IoT Central での温度コントローラー デバイス テンプレートのサーモスタット コンポーネントを示すスクリーンショット。":::

コンポーネントと対話するデバイス コードを記述する方法については、「[IoT プラグ アンド プレイ デバイス開発者ガイド](concepts-developer-guide-device.md)」を参照してください。

デバイス上のコンポーネントと対話するサービス コードを記述する方法については、「[IoT プラグ アンド プレイ サービス開発者ガイド](concepts-developer-guide-service.md)」を参照してください。

### <a name="inheritance"></a>継承

継承を使用すると、基底インターフェイスの機能を再利用して、インターフェイスの機能を拡張できます。 たとえば、いくつかのデバイス モデルがシリアル番号などの一般的な機能を共有できます。

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="サーモスタットと、基底インターフェイスの機能を共有するフロー コントローラーを示す、デバイス モデル内の継承の例。" border="false":::

次のスニペットは、上の図で示した継承関係を、`extends` キーワードを使用して定義する DTML モデルを示しています。

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

次のスクリーンショットは、IoT Central デバイス テンプレート環境におけるこのモデルを示しています。

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="IoT Central でのインターフェイス継承を示すスクリーンショット":::

デバイスまたはサービス側コードを記述するとき、継承されたインターフェイスを操作するために特別なことをする必要はありません。 このセクションで示している例では、デバイス コードにより、シリアル番号がサーモスタット インターフェイスの一部であるかのように報告されます。

### <a name="tips"></a>ヒント

モデルを作成するときに、コンポーネントと継承を組み合わせることができます。 次の図では、`thermostat` モデルが `baseDevice` インターフェイスから継承している様子を示しています。 `baseDevice` インターフェイスのコンポーネントは、それ自体が別のインターフェイスから継承しています。

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="コンポーネントと継承の両方を使用するモデルを示す図。" border="false":::

次のスニペットは、上の図で示した継承関係とコンポーネントの使用方法を、`extends` と `component` のキーワードを使用して定義する DTML モデルを示しています。

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>データ型

データ型を使用して、テレメトリ、プロパティ、およびコマンド パラメーターを定義します。 データ型には、プリミティブと複合があります。 複合データ型は、プリミティブ、または他の複合型を使用します。 複合型の最大深度は 5 レベルです。

### <a name="primitive-types"></a>プリミティブ型

次の表は、使用できる一連のプリミティブ型を示しています。

| プリミティブ型 | 説明 |
| --- | --- |
| `boolean` | ブール値 |
| `date` | [RFC 3339 のセクション 5.6](https://tools.ietf.org/html/rfc3339#section-5.6) で定義されている完全な日付 |
| `dateTime` | [RFC 3339](https://tools.ietf.org/html/rfc3339) で定義されている日付/時刻 |
| `double` | IEEE 8 バイトの浮動小数点 |
| `duration` | ISO 8601 形式の期間 |
| `float` | IEEE 4 バイトの浮動小数点 |
| `integer` | 4 バイトの符号付き整数 |
| `long` | 8 バイトの符号付き整数 |
| `string` | UTF8 文字列 |
| `time` | [RFC 3339 のセクション 5.6](https://tools.ietf.org/html/rfc3339#section-5.6) で定義されている完全な時刻 |

次のスニペットは、`schema` フィールドで `double` 型を使用するテレメトリ定義の例を示しています。

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>複合データ型

複合データ型は "*配列*"、"*列挙*"、"*マップ*"、"*オブジェクト*" のうちのいずれか、あるいは地理空間型のうちのいずれかです。

#### <a name="arrays"></a>配列

配列はインデックス可能なデータ型であり、すべての要素が同じ型です。 要素型は、プリミティブまたは複合のいずれかの型にできます。

次のスニペットは、`schema` フィールドで `Array` 型を使用するテレメトリ定義の例を示しています。 配列の要素はブール値です。

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>列挙

列挙は、値にマップする名前付きラベルのセットを持つ型を表します。 値は整数にも文字列にもできますが、ラベルは常に文字列です。

次のスニペットは、`schema` フィールドで `Enum` 型を使用するテレメトリ定義の例を示しています。 列挙の中の値は整数です。

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

マップとは、キーと値のペアを持つ型で、値の型はすべて同じです。 マップの中のキーは、文字列でなければなりません。 マップの中の値は、あらゆる型 (別の複合型を含む) にできます。

次のスニペットは、`schema` フィールドで `Map` 型を使用するプロパティ定義の例を示しています。 マップの中の値は文字列です。

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objects

オブジェクト型は、名前付きフィールドで構成されています。 オブジェクト マップの中のフィールドの型は、プリミティブと複合のいずれかにできます。

次のスニペットは、`schema` フィールドで `Object` 型を使用するテレメトリ定義の例を示しています。 オブジェクトの中のフィールドは `dateTime`、`duration`、および `string` の型です。

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>地理空間型

DTDL が提供する、[GeoJSON](https://geojson.org/) に基づいた、地理空間データ構造をモデル化するための一連の地理空間型は `point`、`multiPoint`、`lineString`、`multiLineString`、`polygon`、`multiPolygon` などです。 これらの型は、事前定義された、入れ子構造の配列、オブジェクト、列挙です。

次のスニペットは、`schema` フィールドで `point` 型を使用するテレメトリ定義の例を示しています。

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

地理空間型は配列ベースであるため、現在、プロパティ定義では使用できません。

## <a name="semantic-types"></a>セマンティック型

プロパティまたはテレメトリ定義のデータ型は、デバイスがサービスと交換するデータの形式を指定します。 セマンティック型は、アプリケーションが値を処理または表示する方法を決定するために使用できるテレメトリとプロパティに関する情報を提供します。 それぞれのセマンティック型には 1 つ以上の単位が関連付けられています。 たとえば、摂氏と華氏は、温度のセマンティック型の単位です。 IoT Central のダッシュボードと分析では、セマンティック型情報を使用して、テレメトリまたはプロパティ値をプロットする方法と単位の表示方法を決定することができます。 モデル パーサーを使用してセマンティック型を読み取る方法については、「[Digital Twins モデル パーサーについて理解する](concepts-model-parser.md)」を参照してください。

次のスニペットは、セマンティック型情報を含むテレメトリ定義の例を示しています。 セマンティック型 `Temperature` は `@type` 配列に追加されており、`unit` の値である `degreeCelsius` はセマンティック型の有効な単位の 1 つです。

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>ローカリゼーション

IoT Central などのアプリケーションは、モデルの情報を使用して、IoT プラグ アンド プレイ デバイスと交換されるデータの周囲に UI を動的にビルドします。 たとえば、ダッシュボード上のタイルには、テレメトリ、プロパティ、コマンドの名前と説明を表示できます。

モデルの中のオプションの `description` および `displayName` フィールドで保持される文字列は、UI で使用することが目的です。 これらのフィールドで保持できるローカライズされた文字列を、アプリケーションが使用してローカライズされた UI を表示できます。

次のスニペットは、ローカライズされた文字列を含む温度テレメトリ定義の例を示しています。

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

ローカライズされた文字列の追加は省略可能です。 次の例には、1 つの既定の言語のみがあります。

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>ライフサイクルとツール

デバイス モデルの 4 つのライフサイクル ステージは、作成、パブリケーション、使用、バージョン管理です。

### <a name="author"></a>Author

DTML デバイス モデルは、テキスト エディターで作成できる JSON ドキュメントです。 ただし、IoT Central では、デバイス テンプレート GUI 環境を使用して DTML モデルを作成できます。 IoT Central では、次のことができます。

- プロパティ、テレメトリ、コマンドを定義するインターフェイスを作成する。
- コンポーネントを使用して複数のインターフェイスをまとめて組み立てる。
- インターフェイス間の継承関係を定義する。
- DTML モデル ファイルをインポートおよびエクスポートする。

詳しくは、「[Azure IoT Central アプリケーションで新しい IoT デバイスの種類を定義する](../iot-central/core/howto-set-up-template.md)」を参照してください。

[Visual Studio Code 用の DTDL エディター](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)を使用すると、構文検証とオートコンプリートを備えたテキストベースの編集環境を使用して、モデル作成操作をより細かく制御できます。

### <a name="publish"></a>公開

DTML モデルを共有可能および検出可能にするには、デバイス モデル リポジトリに公開します。

モデルをパブリック リポジトリに公開する前に、`dmr-client` ツールを使用してモデルを検証できます。

詳細については、「[デバイス モデル リポジトリ](concepts-model-repository.md)」を参照してください。

### <a name="use"></a>用途

IoT Central などのアプリケーションは、デバイス モデルを使用します。 IoT Central では、モデルはデバイスの機能を記述しているデバイス テンプレートの一部です。 IoT Central は、デバイス テンプレートを使用して、ダッシュボードや分析を含むデバイスの UI を動的にビルドします。

カスタム ソリューションで [Digital Twins モデル パーサー](concepts-model-parser.md)を使用して、モデルを実装するデバイスの機能を理解できます。 詳しくは、「[IoT ソリューションで IoT プラグ アンド プレイを使用する](concepts-model-discovery.md)」をご覧ください。

### <a name="version"></a>バージョン

モデルを使用するデバイスとサーバー側ソリューションが動作し続けるようにするために、公開されたモデルは変更できません。

DTMI にはバージョン番号が含まれており、これを使用して、モデルの複数のバージョンを作成することができます。 デバイスとサーバー側ソリューションは、使用するように設計されている特定のバージョンを使用できます。

IoT Central は、デバイス モデルに関して、より多くのバージョン管理ルールを実装しています。 IoT Central でデバイス テンプレートとそのモデルをバージョン管理すると、デバイスを以前のバージョンから新しいバージョンに移行できます。 ただし、移行したデバイスは、ファームウェアをアップグレードしないと、新しい機能を使用することができません。 詳しくは、「[デバイス テンプレートの新しいバージョンを作成する](../iot-central/core/howto-version-device-template.md)」を参照してください。

## <a name="limits-and-constraints"></a>制限および制約

次の一覧では、モデルに対するいくつかの重要な制約と制限をまとめています。

- 現在、配列、マップ、およびオブジェクトの最大深度は 5 レベルです。
- プロパティ定義で配列を使用することはできません。
- インターフェイスは 10 レベルの深度まで拡張できます。
- インターフェイスは、最大で 2 つの、他のインターフェイスまで拡張できます。
- コンポーネントが他のコンポーネントを含むことはできません。

## <a name="next-steps"></a>次のステップ

ここまでで、デバイスのモデリングについて学習しました。その他のリソースを次に示します。

- [DTDL 作成ツールをインストールして使用する](howto-use-dtdl-authoring-tools.md)
- [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [モデルのリポジトリ](./concepts-model-repository.md)
