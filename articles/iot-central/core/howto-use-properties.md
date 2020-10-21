---
title: Azure IoT Central ソリューションでプロパティを使用する
description: Azure IoT Central ソリューションで読み取り専用および書き込み可能なプロパティを使用する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a750a98c27fd62288993b2203acc2032ccf39d71
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999760"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Azure IoT Central ソリューションでプロパティを使用する

この記事では、Azure IoT Central アプリケーションでデバイス テンプレートに定義されているデバイス プロパティを使用する方法について説明します。

プロパティは、特定の時点の値を表します。 たとえば、デバイスはプロパティを使用して、到達しようとしているターゲット温度を報告できます。 プロパティを使用して、デバイスと Azure IoT Central アプリケーションとの間で状態を同期させることもできます。 Azure IoT Central から、書き込み可能なプロパティを設定できます。

Azure IoT Central アプリケーションで、クラウドのプロパティを定義することもできます。 クラウドのプロパティ値は、デバイスと交換されることはなく、この記事の範囲外です。

## <a name="define-your-properties"></a>プロパティを定義する

プロパティは、デバイスの状態を表すデータ フィールドです。 プロパティは、デバイスのオンまたはオフの状態など、デバイスの持続的状態を表すために使用します。 プロパティでは、デバイスのソフトウェア バージョンなど、デバイスの基本的なプロパティを表すこともできます。 プロパティは、読み取り専用または書き込み可能として宣言できます。

次のスクリーンショットは、Azure IoT Central アプリケーションでのプロパティ定義を示しています。

![Azure IoT Central アプリケーションでのプロパティ定義を示すスクリーンショット。](./media/howto-use-properties/property-definition.png)

プロパティ機能の構成設定を次の表に示します。

| フィールド           | 説明                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 表示名    | ダッシュボードとフォームで使用されるプロパティ値の表示名。                                                                                                                                                              |
| 名前            | プロパティの名前。 Azure IoT Central によって表示名からこのフィールドの値が生成されますが、必要に応じて独自の値を選択できます。 このフィールドは英数字にする必要があります。                                                 |
| 機能の種類 | プロパティ。                                                                                                                                                                                                                          |
| セマンティックの種類   | テレメトリのセマンティックの種類 (温度、状態、イベントなど)。 セマンティックの種類の選択によって、次のどのフィールドを使用できるかが決まります。                                                                       |
| スキーマ          | プロパティのデータ型 (double、string、vector など)。 使用できる選択肢は、セマンティックの種類によって決まります。 スキーマは、イベントおよび状態のセマンティックの種類には使用できません。                                               |
| 書き込み可能       | プロパティが書き込み可能でない場合、デバイスから Azure IoT Central にプロパティ値を報告することができます。 プロパティが書き込み可能な場合、デバイスから Azure IoT Central にプロパティ値を報告することができます。 この場合は Azure IoT Central からデバイスにプロパティの更新を送信できます。 |
| 重大度        | イベントのセマンティックの種類にのみ使用できます。 重大度は、**エラー**、**情報**、または**警告**です。                                                                                                                         |
| 状態の値    | 状態のセマンティックの種類にのみ使用できます。 考えられる状態の値を定義します。それぞれに表示名、名前、列挙型、および値があります。                                                                                   |
| ユニット            | プロパティ値の単位 (**mph**、 **%** 、 **&deg; C** など)。                                                                                                                                                              |
| 表示単位    | ダッシュボードとフォームで使用する表示単位。                                                                                                                                                                                    |
| 解説         | プロパティ機能に関するコメント。                                                                                                                                                                                        |
| 説明     | プロパティ機能の説明。                                                                                                                                                                                          |

プロパティは、次に示すように、デバイス テンプレートのインターフェイスで定義することもできます。

``` json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

この例には 5 つのプロパティがあります。 次に示すように、これらのプロパティは、UI のプロパティ定義に関連付けることができます。

* 機能の種類 `Property` を指定する `@type`
* プロパティ値の `name`。
* `schema` はプロパティのデータ型を指定します。 この値は、double、integer、Boolean、string などのプリミティブ型にすることができます。 複合オブジェクト型、配列、およびマップもサポートされています。
* `writable`: 既定では、プロパティは読み取り専用です。 このフィールドを使用することで、プロパティを書き込み可能としてマークできます。

表示名や説明などのオプション フィールドを使用すると、インターフェイスや機能に詳細を追加できます。

プロパティを作成するときには、Object や Enum などの複雑な**スキーマ**型を指定できます。

![機能を追加する方法を示すスクリーンショット。](./media/howto-use-properties/property.png)

**Object** などの複雑な**スキーマ**を選択するときには、オブジェクトも定義する必要があります。

![オブジェクトを定義する方法を示すスクリーンショット。](./media/howto-use-properties/object.png)

次のコードは、Object プロパティ型の定義を示しています。 このオブジェクトには、文字列型と整数型の 2 つのフィールドがあります。

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>読み取り専用プロパティを実装する

既定では、プロパティは読み取り専用です。 読み取り専用のプロパティは、デバイスがプロパティ値の更新を Azure IoT Central アプリケーションに報告することを意味します。 Azure IoT Central アプリケーションでは、読み取り専用のプロパティの値を設定することはできません。

Azure IoT Central では、デバイス ツインを使用して、デバイスと Azure IoT Central アプリケーションの間でプロパティ値を同期します。 デバイスのプロパティ値では、デバイス ツインによってレポートされるプロパティが使用されます。 詳細については、[デバイス ツイン](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)に関するページを参照してください。

デバイス機能モデルからの次のスニペットは、読み取り専用のプロパティ型の定義を示しています。

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

読み取り専用プロパティは、デバイスによって Azure IoT Central に送信されます。 これらのプロパティは JSON ペイロードとして送信されます。 詳細については、[ペイロード](./concepts-telemetry-properties-commands.md)に関するページを参照してください。

Azure IoT device SDK を使用して、Azure IoT Central アプリケーションにプロパティの更新を送信できます。

デバイス ツインのプロパティは、次の関数を使用して Azure IoT Central アプリケーションに送信できます。

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

この記事では、簡単にするために Node.js を使用します。 デバイス アプリケーションの例の詳細については、次のチュートリアルを参照してください。

* [クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する (Node.js)](tutorial-connect-device-nodejs.md)
* [クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する (Python)](tutorial-connect-device-python.md)

Azure IoT Central アプリケーションの次のビューは、表示できるプロパティを示しています。 このビューでは、 **[デバイス モデル]** プロパティは自動的に "_読み取り専用のデバイス プロパティ_" になります。

![読み取り専用のプロパティのビューを示すスクリーンショット。](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>書き込み可能なプロパティを実装する

書き込み可能なプロパティは、フォーム上の Azure IoT Central アプリケーションの演算子によって設定されます。 このプロパティは Azure IoT Central からデバイスに送信されます。 Azure IoT Central では、デバイスからの確認応答を待機します。

デバイス機能モデルからの次のスニペットは、書き込み可能なプロパティ型の定義を示しています。

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。

``` json
{ "Brightness Level": 2 }
```

デバイス側で応答する書き込み可能なプロパティを定義して処理するに、次のコードを使用できます。

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

応答メッセージには、`ac` フィールドと `av` フィールドが含まれている必要があります。 `ad` フィールドは省略可能です。 例については、以下のスニペットを参照してください。

* `ac` は、次の表の値を使用する数値フィールドです。
* `av` は、デバイスに送信されるバージョン番号です。
* `ad` は、オプションの文字列での説明です。

| 値 | Label | 説明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 完了 | プロパティの変更操作が正常に完了しました。 |
| `'ac': 202` または `'ac': 201` | Pending | プロパティの変更操作が保留中または進行中です。 |
| `'ac': 4xx` | エラー | 要求されたプロパティの変更は、有効でなかったか、エラーが発生しました。 |
| `'ac': 5xx` | エラー | 要求された変更の処理中に、デバイスで予期しないエラーが発生しました。 |


デバイス ツインの詳細については、「[バックエンド サービスからデバイスを構成する](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)」を参照してください。

オペレーターが Azure IoT Central アプリケーションで書き込み可能なプロパティを設定すると、アプリケーションではデバイス ツインによって必要とされるプロパティを使用して、デバイスに値を送信します。 次に、デバイスによってデバイス ツインの reported プロパティが使用され、応答が行われます。 Azure IoT Central は、reported プロパティの値を受け取ると、"**承認済み**" の状態でプロパティ ビューを更新します。

次のビューは、書き込み可能なプロパティを示しています。 値を入力して **[保存]** を選択すると、初期状態は "**保留中**" になります。 デバイス側が変更を受け入れると、状態は "**承認済み**" に変わります。

!["保留中" の状態を示すスクリーンショット。](./media/howto-use-properties/status-pending.png)

!["承認済み" のプロパティを示すスクリーンショット。](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>次の手順

Azure IoT Central アプリケーションでプロパティを使用する方法を習得したら、次は以下を参照してください。

* [Payloads](concepts-telemetry-properties-commands.md)
* [クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する (Node.js)](tutorial-connect-device-nodejs.md)
