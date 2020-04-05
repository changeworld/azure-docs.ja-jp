---
title: データ処理とユーザー定義関数 - Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins によるデータ処理、マッチャー、ユーザー定義関数の概要。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231379"
---
# <a name="data-processing-and-user-defined-functions"></a>データ処理とユーザー定義関数

Azure Digital Twins は高度な計算機能を備えています。 開発者はカスタム関数を定義し、受信したテレメトリ メッセージに対して実行し、事前定義したエンドポイントにイベントを送信できます。

## <a name="data-processing-flow"></a>データ処理フロー

デバイスによって Azure Digital Twins にテレメトリ データが送信された後で、開発者は "*検証*"、"*照合*"、"*計算*"、"*ディスパッチ*" という 4 つのフェーズでデータを処理できます。

[![Azure Digital Twins のデータ処理フロー](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. 検証フェーズでは、受信したテレメトリ メッセージが一般的に理解できる[データ転送オブジェクト](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5)の形式に変換されます。 このフェーズでは、デバイスとセンサーも検証されます。
1. 照合フェーズでは、実行する適切なユーザー定義関数が検索されます。 事前定義したマッチャーによって、受信したテレメトリ メッセージからのデバイス、センサー、空間情報に基づき、ユーザー定義関数が検索されます。
1. 計算フェーズでは、前のフェーズで一致したユーザー定義関数が実行されます。 これらの関数により、空間グラフノードで計算値が読み取られたり、更新されたりすることがあります。また、これらの関数ではカスタム通知を送信できます。
1. ディスパッチ フェーズでは、計算フェーズからグラフに定義されているエンドポイントにカスタム通知が送信されます。

## <a name="data-processing-objects"></a>データ処理オブジェクト

Azure Digital Twins のデータ処理は、"*マッチャー*"、"*ユーザー定義関数*"、"*ロールの割り当て*" という 3 つのオブジェクトの定義で構成されています。

[![Azure Digital Twins のデータ処理オブジェクト](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>マッチャー

マッチャーで定義される一連の条件により、受信したセンサー テレメトリに基づいて実行するアクションが判断されます。 一致を判断する条件には、センサー、センサーの親デバイス、センサーの親空間からのパラメーターが含まれることがあります。 条件は [JSON パス](https://jsonpath.com/)に対する比較として表現されます。概要を以下に示します。

- エスケープされた String 値 **によって表されるデータ型**Temperature`\"Temperature\"` のすべてのセンサー
- ポートに `01` がある
- 拡張プロパティ キー **Manufacturer** がエスケープされた String 値 `\"Contoso\"` に設定されているデバイスに属する
- エスケープされた String `\"Venue\"` によって指定された型の空間に属する
- 親 **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD` の子孫である

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
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

ユーザー定義関数は、隔離された Azure Digital Twins 環境内で実行されるカスタム関数です。 ユーザー定義関数は、生のセンサー テレメトリ メッセージの受信時に、それにアクセスします。 また、ユーザー定義関数は、空間グラフとディスパッチャー サービスにもアクセスします。 ユーザー定義関数がグラフ内に登録されたら、([前述](#matchers)の) マッチャーを作成し、ユーザー定義関数の実行タイミングを指定する必要があります。 たとえば、Azure Digital Twins が特定のセンサーから新しいテレメトリを受信すると、一致したユーザー定義関数では、直前の数回分のセンサー読み取り値から移動平均を計算できます。

ユーザー定義関数は、JavaScript で記述できます。 ヘルパー メソッドは、ユーザー定義の実行環境でグラフとやり取りします。 開発者は、センサー テレメトリ メッセージに対してコードのカスタム スニペットを実行できます。 たとえば、次のようになります。

- グラフ内のセンサー オブジェクトに直接、センサー読み取りを設定します。
- グラフの空間内でさまざまなセンサー読み取りに基づいてアクションを実行します。
- 受信したセンサー読み取りについて特定の条件が満たされるとき、通知を作成します。
- 通知を送信する前に、センサー読み取りにグラフ メタデータを添付します。

詳細については、[ユーザー定義関数を使用する方法](./how-to-user-defined-functions.md)に関するページを参照してください。

#### <a name="examples"></a>例

[Digital Twins の C# サンプルに関する GitHub リポジトリ](https://github.com/Azure-Samples/digital-twins-samples-csharp/)には、ユーザー定義関数の例がいくつか記載されています。
- [こちらの関数](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js)では、二酸化炭素、モーション、および温度の値を取得し、それらの値が範囲内である部屋が使用可能かどうかを判定します。 [Digital Twins のチュートリアル](tutorial-facilities-udf.md)では、この関数について詳しく説明しています。 
- [こちらの関数](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js)では、複数のモーション センサーからデータを取得し、いずれのセンサーからもモーションが検出されなかった場合に、スペースが使用可能であると判定します。 [クイック スタート](quickstart-view-occupancy-dotnet.md) (または[チュートリアル](tutorial-facilities-setup.md)) で使用されているユーザー定義関数は、ファイルのコメント セクションで説明されている変更を加えることで、簡単に置き換えることができます。 

### <a name="role-assignment"></a>ロール割り当て

ユーザー定義関数の動作は、サービス内のデータのセキュリティを保護するために、Azure Digital Twins の[ロールベースのアクセス制御](./security-role-based-access-control.md)の対象になります。 ロールの割り当てによって、どのユーザー定義関数が空間グラフおよびそのエンティティとやり取りする適切なアクセス許可を持つかが定義されます。 たとえば、ユーザー定義関数は、特定の空間のグラフ データに対する *CREATE*、*READ*、*UPDATE*、または *DELETE* の機能とアクセス許可を持つ場合があります。 ユーザー定義関数からグラフにデータが要求されるときやユーザー定義関数によってアクションが試行されるとき、ユーザー定義関数のアクセス レベルが確認されます。 詳細については、[ロールベースのアクセス制御](./security-create-manage-role-assignments.md)に関するページを参照してください。

ロールが割り当てられていないユーザー定義関数がマッチャーによってトリガーされることがあります。 その場合、ユーザー定義関数によるグラフからのデータ読み取りは失敗します。

## <a name="next-steps"></a>次のステップ

- 他の Azure サービスにイベントやテレメトリ メッセージをルーティングする方法の詳細については、[イベントとメッセージのルーティング](./concepts-events-routing.md)に関するページを参照してください。

- マッチャー、ユーザー定義関数、ロールの割り当ての作成方法の詳細については、[ユーザー定義関数の使用ガイド](./how-to-user-defined-functions.md)に関するページを参照してください。

- [ユーザー定義関数クライアント ライブラリ リファレンス ドキュメント](./reference-user-defined-functions-client-library.md)を参照してください。
