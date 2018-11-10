---
title: Azure Digital Twins によるデータ処理とユーザー定義関数 |Microsoft Docs
description: Azure Digital Twins によるデータ処理、マッチャー、ユーザー定義関数
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: a45f82b142ee4f4c9c88ea755607b88323feaae5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210127"
---
# <a name="data-processing-and-user-defined-functions"></a>データ処理とユーザー定義関数

Azure Digital Twins は高度な計算機能を備えています。 開発者はカスタム関数を定義し、受信したテレメトリ メッセージに対して実行し、事前定義したエンドポイントにイベントを送信できます。

## <a name="data-processing-flow"></a>データ処理フロー

デバイスが Digital Twins にテレメトリ データを送信すると、開発者は_検証_、_照合_、_計算_、_ディスパッチ_という 4 つのフェーズでデータを処理できます。

![Digital Twins データ処理フロー][1]

1. _検証_フェーズでは、受信したテレメトリ メッセージが一般的に理解できる[**データ転送オブジェクト**](https://en.wikipedia.org/wiki/Data_transfer_object)の形式に変換されます。 このフェーズでは、デバイスとセンサーも検証されます。
1. _照合_フェーズでは、実行するユーザー定義関数が検索されます。 事前定義したマッチャーによって、受信したテレメトリ メッセージからのデバイス、センサー、スペース情報に基づき、ユーザー定義関数が検索されます。
1. _計算_フェーズでは、前のフェーズで一致したユーザー定義関数が実行されます。 これらの関数により、空間グラフノードで計算値が読み取られたり、更新されたりすることがあります。また、これらの関数ではカスタム通知を送信できます。
1. _ディスパッチ_フェーズでは、計算フェーズからグラフに定義されているエンドポイントにカスタム通知が送信されます。

## <a name="data-processing-objects"></a>データ処理オブジェクト

Azure Digital Twins のデータ処理は、_マッチャー_、_ユーザー定義関数_、_ロールの割り当て_という 3 つのオブジェクトの定義で構成されています。

![Digital Twins データ処理フロー][2]

### <a name="matchers"></a>マッチャー

_マッチャー_で定義される一連の条件により、受信したセンサー テレメトリに基づいて実行するアクションが判断されます。 一致を判断するこのような条件には、センサー、センサーの親デバイス、センサーの親空間からのパラメーターが含まれることがあります。 条件は [JSON パス](http://jsonpath.com/)に対する比較として表現されます。概要を以下に示します。

- データ型 **Temperature** のあらゆるセンサー。
- ポートに `01` がある。
- 拡張プロパティ キー **Manufacturer** が値 `"GoodCorp"` に設定されているデバイスに属する。
- 型 `"Venue"` の空間に属する。
- 親 **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD` の子孫である。

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON パスでは大文字と小文字が区別されます。
> - JSON ペイロードは、次によって返されるペイロードと同じです。
>   - センサーの `/sensors/{id}?includes=properties,types`。
>   - センサーの親デバイスの `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`。
>   - センサーの親空間の `/spaces/{id}?includes=properties,types,location,timezone`。
> - 比較では、大文字と小文字は区別されません。

### <a name="user-defined-functions"></a>ユーザー定義関数

_ユーザー定義関数_ (_UDF_) は、Azure Digital Twins の隔離された環境内で実行されるカスタム関数です。 UDF は未加工のセンサー テレメトリ メッセージ、空間グラフ、ディスパッチャー サービスにアクセスできます。 UDF がグラフ内に登録されたら、(前述の) マッチャーを作成し、UDF の実行タイミングを指定する必要があります。 Digital Twins が所与のセンサーから新しいテレメトリを受信すると、一致した UDF では、たとえば、最後の数回分のセンサー読み取り値から移動平均を計算できます。

UDF は JavaScript で記述できます。UDF を利用すると、開発者はセンサー テレメトリ メッセージに対してコードのカスタム スニペットを実行できます。 ユーザー定義の実行環境でグラフとやりとりするヘルパー メソッドもあります。 UDF を利用して開発者ができること:

- グラフ内のセンサー オブジェクトに直接、センサー読み取りを設定します。
- グラフの空間内でさまざまなセンサー読み取りに基づいてアクションを実行します。
- 受信したセンサー読み取りについて特定の条件が満たされるとき、通知を作成します。
- 通知を送信する前に、センサー読み取りにグラフ メタデータを添付します。

詳細については、[ユーザー定義関数の使用方法](how-to-user-defined-functions.md)に関するページを参照してください。

### <a name="role-assignment"></a>ロール割り当て

UDF の操作は、サービス内でデータをセキュリティ保護するために、Digital Twins のロールベースのアクセス制御に左右されます。 ロールの割り当てによって、空間グラフとやりとりするためのアクセス許可が所与の UDF に与えられます。 たとえば、UDF によって、所与の空間の下、グラフ データの作成、読み取り、更新、削除が試行されます。 UDF からグラフにデータが要求されるときや UDF によってアクションが試行されるとき、UDF のアクセス レベルが確認されます。 詳細については、[ロールベースのアクセス制御](security-create-manage-role-assignments.md)に関するページを参照してください。

ロールが割り当てられていない UDF がマッチャーによってトリガーされることがあります。 その場合、UDF では、グラフからのデータ読み取りが失敗します。

## <a name="next-steps"></a>次の手順

他の Azure サービスにイベントやテレメトリ メッセージを送信する方法の詳細については、[イベントとメッセージのルーティング](concepts-events-routing.md)に関するページを参照してください。

マッチャー、ユーザー定義関数、ロールの割り当ての作成方法の詳細については、[ユーザー定義関数の使用ガイド](how-to-user-defined-functions.md)に関するページを参照してください。

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
