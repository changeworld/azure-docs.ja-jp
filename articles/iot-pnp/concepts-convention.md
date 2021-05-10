---
title: IoT プラグ アンド プレイ規則 | Microsoft Docs
description: IoT プラグ アンド プレイからデバイスに対して、テレメトリとプロパティを送信し、コマンドとプロパティの更新を処理するときに、使用することが想定される規則の説明。
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a8dfb3cacf798559273361ec75cab4570a8a5228
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582682"
---
# <a name="iot-plug-and-play-conventions"></a>IoT プラグ アンド プレイ規則

IoT プラグ アンド プレイ デバイスは、IoT ハブとメッセージを交換するとき、一連の規則に従う必要があります。 IoT プラグ アンド プレイ デバイスは、MQTT プロトコルを使用して IoT Hub と通信します。

デバイスには[モジュール](../iot-hub/iot-hub-devguide-module-twins.md)を含めるか、IoT Edge ランタイムによってホストされる [IoT Edge モジュール](../iot-edge/about-iot-edge.md)内にデバイスを実装できます。

IoT プラグ アンド プレイ デバイスが実装するテレメトリ、プロパティ、コマンドを、[Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) _モデル_ で記述します。 この記事では、次の 2 種類のモデルを参照します。

- **コンポーネントなし** - コンポーネントのないモデル。 このモデルでは、テレメトリ、プロパティ、およびコマンドを、メイン インターフェイスのコンテンツ セクションで最上位レベルのプロパティとして宣言します。 Azure IoT エクスプローラー ツールでは、このモデルは 1 つの _既定のコンポーネント_ として表示されます。
- **複数のコンポーネント** - 2 つ以上のインターフェイスで構成されるモデル。 テレメトリ、プロパティ、およびコマンドを持つ _既定のコンポーネント_ として表示されるメイン インターフェイス。 追加のテレメトリ、プロパティ、およびコマンドを備えたコンポーネントとして宣言された 1 つ以上のインターフェイス。

詳細については、「[IoT プラグ アンド プレイ モデリング ガイド](concepts-modeling-guide.md)」を参照してください。

## <a name="identify-the-model"></a>モデルを識別する

実装するモデルをアナウンスするために、IoT プラグ アンド プレイ デバイスまたはモジュールでは、`USERNAME` フィールドに `model-id` を追加することにより、MQTT 接続パケットにモデル ID を含めます。

デバイスまたはモジュールに実装されるモデルを識別するには、サービスを使用して以下でモデル ID を取得できます。

- デバイス ツインの `modelId` フィールド。
- デジタル ツインの `$metadata.$model` フィールド。
- デジタル ツインの変更通知

## <a name="telemetry"></a>テレメトリ

コンポーネントのないデバイスから送信されたテレメトリは、追加のメタデータが必要ありません。 システムによって `dt-dataschema` プロパティが追加されます。

複数のコンポーネント デバイスからテレメトリが送信された場合、メッセージ プロパティとして `$.sub` が追加されます。 システムによって、`dt-subject` および `dt-dataschema` プロパティが追加されます。

## <a name="read-only-properties"></a>読み取り専用プロパティ

### <a name="sample-no-component-read-only-property"></a>コンポーネントなしの読み取り専用プロパティのサンプル

デバイスまたはモジュールからは、DTDL v2 の規則に従う有効な JSON を送信できます。

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

reported プロパティのペイロードの例:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>複数のコンポーネントの読み取り専用プロパティのサンプル

この要素からコンポーネントを参照していることを示すために、デバイスまたはモジュールは `{"__t": "c"}` マーカーを追加する必要があります。

コンポーネントを参照する DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

コンポーネントを定義する DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

reported プロパティのペイロードの例:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>書き込み可能なプロパティ

デバイスまたはモジュールでは、reported プロパティを送信することで、プロパティを受け取ったことを確認する必要があります。 報告されたプロパティには次のものが含まれます。

- `value` - プロパティの実際の値 (通常は受信した値。ただし、デバイスは別の値を報告するように決定できます)。
- `ac` - HTTP 状態コードを使用する受信確認コード。
- `av` - 目的のプロパティの `$version` を参照する確認バージョン。 この値は、必要なプロパティの JSON ペイロードで見つけることができます。
- `ad` - 省略可能な受信確認の説明。

デバイスが起動すると、そのデバイスは、デバイス ツインを要求し、書き込み可能なプロパティの更新があるかどうかを確認する必要があります。 デバイスがオフラインの間に書き込み可能なプロパティのバージョンが追加された場合、デバイスは reported プロパティ応答を送信して、更新を受け取ったことを確認する必要があります。

デバイスが必要な初期プロパティをハブから受け取っていない場合は、そのデバイスは初回起動時に reported プロパティの初期値を送信できます。 この場合、デバイスは `av` を `1` に設定する必要があります。 次に例を示します。

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

デバイスは、reported プロパティを使用して、ハブに他の情報を提供できます。 たとえば、デバイスは次のような一連の進行中メッセージを使用して応答できます。

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

デバイスが目標温度に達すると、次のメッセージが送信されます。

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

デバイスは次のようなエラーを報告できます。

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>コンポーネントなしの書き込み可能プロパティのサンプル

デバイスは、1 つのペイロードで複数の reported プロパティを受け取ると、reported プロパティ応答を複数のペイロードに送信できます。

デバイスまたはモジュールからは、DTDL v2 の規則に従う有効な JSON を送信できます。

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

必要なプロパティのペイロードの例:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

reported プロパティの最初のペイロードの例:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

reported プロパティの 2 番目のペイロードの例:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>複数のコンポーネントの書き込み可能プロパティのサンプル

この要素からコンポーネントを参照していることを示すために、デバイスまたはモジュールは `{"__t": "c"}` マーカーを追加する必要があります。

マーカーは、コンポーネントで定義されたプロパティの更新についてのみ送信されます。 既定のコンポーネントで定義されているプロパティの更新には、マーカーは含まれません。[コンポーネントなしの書き込み可能プロパティのサンプル](#sample-no-component-writable-property)を参照してください。

デバイスは、1 つのペイロードで複数の reported プロパティを受け取ると、reported プロパティ応答を複数のペイロードに送信できます。

デバイスまたはモジュールでは、reported プロパティを送信することで、プロパティを受け取ったことを確認する必要があります。

コンポーネントを参照する DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

コンポーネントを定義する DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

必要なプロパティのペイロードの例:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

reported プロパティの最初のペイロードの例:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

reported プロパティの 2 番目のペイロードの例:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>コマンド

プレフィックスなしでコマンド名を使用するコンポーネント インターフェイスはありません。

デバイスまたはモジュールでは、複数のコンポーネント インターフェイスで、`componentName*commandName` という形式のコマンド名を使用します。

## <a name="next-steps"></a>次のステップ

IoT プラグ アンド プレイ規則について学習したので、いくつかの追加リソースを次に示します。

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C デバイス SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT プラグ アンド プレイ モデリング ガイド](concepts-modeling-guide.md)