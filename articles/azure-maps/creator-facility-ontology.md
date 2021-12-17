---
title: Microsoft Azure Maps Creator での施設オントロジー
description: Azure Maps Creator の地物クラスを記述する施設オントロジー
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/14/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
zone_pivot_groups: facility-ontology-schema
ms.openlocfilehash: 3ef3d9dd9d992e17c66516de8d13978e6c7c4821
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450801"
---
# <a name="facility-ontology"></a>施設オントロジー

施設オントロジーでは、Azure Maps Creator を使用して施設データを Creator のデータセットに内部的に格納する方法を定義します。  施設データの内部構造を定義するだけでなく、施設オントロジーも、WFS API を介して外部に公開されます。 WFS API を使用して、データセット内の施設データに対してクエリを実行する場合、応答形式は、そのデータセットに指定されているオントロジーによって定義されます。

大まかには、施設オントロジーを使用して、データセットを地物クラスに分割します。 すべての地物クラスでは、`ID` や `Geometry` など、共通のプロパティ セットが共有されます。  各地物クラスでは、共通のプロパティ セットに加えて、一連のプロパティも定義されます。 各プロパティは、そのデータ型と制約によって定義されます。 一部の地物クラスには、他の地物クラスに依存するプロパティがあります。 依存プロパティは、別の地物クラスの `ID` として評価されます。  

## <a name="changes-and-revisions"></a>変更とリビジョン

:::zone pivot="facility-ontology-v1"

Facility 1.0 には、[Azure Maps サービス](https://aka.ms/AzureMaps)の facility 地物クラス定義のリビジョンが含まれます。

:::zone-end

:::zone pivot="facility-ontology-v2"

Facility 2.0 には、[Azure Maps サービス](https://aka.ms/AzureMaps)の facility 地物クラス定義のリビジョンが含まれます。

:::zone-end

### <a name="major-changes"></a>重大な変更

:::zone pivot="facility-ontology-v1"

次の制約検証チェックが修正されました。

* `lineElement` および `areaElement` 地物クラスの `isObstruction = true` の排他性 "*または*" `obstructionArea` の有無の制約検証チェック。

* `category` 地物クラスの `isRoutable = true` の排他性 "*または*" `routeThroughBehavior` の有無の制約検証チェック。
:::zone-end

:::zone pivot="facility-ontology-v2"

* 壁や柱などを保持する structure 地物クラスが追加されました。
* ルーティング シナリオを強化するように設計された属性がクリーンアップされました。 現在のルーティング エンジンでは、これらはサポートされていません。

:::zone-end

## <a name="unit"></a>unit

`unit` 地物クラスでは、ナビゲーション エージェントによって占有およびスキャンでき、重なり合っていない物理的な領域を定義します。 `unit` には、廊下、部屋、中庭などがあります。

**ジオメトリの種類**: 多角形

:::zone pivot="facility-ontology-v1"

| プロパティ           | Type                        | 必須 | 説明                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        | [category.Id](#category)     |true      | [`category`](#category) 地物の ID。|
|`isOpenArea`        | boolean (既定値は `null` です)。                    |false     | ユニットが開口領域であるかどうかを表します。 `true` に設定すると、[構造](#structure)は、ユニット境界を囲まないため、ナビゲーション エージェントでは、[`opening`](#opening) を必要とせずに `unit` に入ることができます。 既定では、ユニットは物理的な壁で囲まれ、開口地物がユニットの境界に配置されている場合のみ開かれます。 開口領域のユニットに壁が必要な場合、`isObstruction` プロパティを `true` に設定して、[`lineElement`](#lineelement) または [`areaElement`](#areaelement) として表すことができます。|
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |ユニットを横断することができる移動エージェントの種類を示します。 指定しない場合、ユニットは任意のナビゲーション エージェントによってスキャン可能であると見なされます。 |
|`isRoutable`        | boolean (既定値は `null` です)。                      | false    |  ユニットがルーティング グラフの一部であるかどうかを決定します。 `true` に設定すると、ユニットをルーティング エクスペリエンス内の出発地および目的地、または中間地点として使用できます。 |
|`routeThroughBehavior`        | enum ["disallowed", "allowed", "preferred"] |  false     | ユニットを通過できるかどうかを決定します。 指定しない場合、`categoryId` プロパティで参照される category 地物の値が継承されます。 指定した場合、category 地物で指定された値はオーバーライドされます。 |
|`nonPublic`        |  boolean| false       | `true` の場合、ユニットは、特権のあるユーザーによってのみナビゲートできます。  既定値は `false` です。 |
| `levelId`          | [level.Id](#level)        | true     | level 地物の ID。 |
|`occupants`         |  [directoryInfo.Id](#directoryinfo) の配列 |    false |    [directoryInfo](#directoryinfo) 地物の ID。 地物内の 1 つまたは複数の居住者を表すために使用されます。 |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | true     | [directoryInfo](#directoryinfo) 地物の ID。 地物の住所を表すために使用されます。|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | true     | ユニットの部屋、ユニット、アパートメント、またはスイートの番号。|
|`name` |    string |    false |    ローカル言語による地物の名前。 許容される最大の長さは 1,000 です。 |
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。  許容される最大の長さは 1,000 です。|
|`nameAlt` |    string |    false |   地物に使用される代替名。 許容される最大の長さは 1,000 です。 |
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

:::zone-end

:::zone pivot="facility-ontology-v2"

| プロパティ           | Type                        | 必須 | 説明                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        | [category.Id](#category)     |true      | [`category`](#category) 地物の ID。|
|`isOpenArea`        | boolean (既定値は `null` です)。                    |false     | ユニットが開口領域であるかどうかを表します。 `true` に設定すると、[構造](#structure)は、ユニット境界を囲まないため、ナビゲーション エージェントでは、[`opening`](#opening) を必要とせずに `unit` に入ることができます。 既定では、ユニットは物理的な壁で囲まれ、開口地物がユニットの境界に配置されている場合のみ開かれます。 開口領域のユニットに壁が必要な場合、`isObstruction` プロパティを `true` に設定して、[`lineElement`](#lineelement) または [`areaElement`](#areaelement) として表すことができます。|
|`isRoutable`        | boolean (既定値は `null` です)。                     | false    |  ユニットがルーティング グラフの一部であるかどうかを決定します。 `true` に設定すると、ユニットをルーティング エクスペリエンス内の出発地および目的地、または中間地点として使用できます。 |
| `levelId`          | [level.Id](#level)        | true     | level 地物の ID。 |
|`occupants`         |  [directoryInfo.Id](#directoryinfo) の配列 |    false |    [directoryInfo](#directoryinfo) 地物の ID。 地物内の 1 つまたは複数の居住者を表すために使用されます。 |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | true     | [directoryInfo](#directoryinfo) 地物の ID。 地物の住所を表すために使用されます。|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | true     | ユニットの部屋、ユニット、アパートメント、またはスイートの番号。|
|`name` |    string |    false |    ローカル言語による地物の名前。  許容される最大の長さは 1,000 です。|
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。  許容される最大の長さは 1,000 です。|
|`nameAlt` |    string |    false |   地物に使用される代替名。  許容される最大の長さは 1,000 です。|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

:::zone-end

:::zone pivot="facility-ontology-v2"

## <a name="structure"></a>structure

`structure` 地物クラスでは、重なり合っておらず、ナビゲートできない物理的な領域を定義します。 壁、柱などを指定できます。

**ジオメトリの種類**: 多角形

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 地物の ID。|
| `levelId`          |  [level.Id](#level)            | true     | [`level`](#level) 地物の ID。 |
|`name` |    string |    false |    ローカル言語による地物の名前。 許容される最大の長さは 1,000 です。 |
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。 許容される最大の長さは 1,000 です。 |
|`nameAlt` |    string |    false |   地物に使用される代替名。  許容される最大の長さは 1,000 です。|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

:::zone-end

## <a name="zone"></a>ゾーン

`zone` 地物クラスでは、Wi-Fi ゾーンや避難場所などの仮想領域を定義します。 ゾーンは目的地として使用できますが、通り抜け用ではありません。

**ジオメトリの種類**: 多角形

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 地物の ID。|
| `setId`          | string         | true     |複数レベルのゾーンを表す zone 地物に必須。 `setId` は、複数のレベルにまたがるゾーンの一意の ID です。 `setId` を使用すると、フロアによってカバレッジが異なるゾーンを、異なるジオメトリを使用してさまざまなレベルで表すことができます。 `setId` には任意の文字列を指定でき、大文字と小文字が区別されます。 `setId` を GUID に設定することをお勧めします。  許容される最大の長さは 1,000 です。|
| `levelId`          |  [level.Id](#level)             | true     | [`level`](#level) 地物の ID。 |
|`name` |    string |    false |    ローカル言語による地物の名前。  許容される最大の長さは 1,000 です。|
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。  許容される最大の長さは 1,000 です。|
|`nameAlt` |    string |    false |   地物に使用される代替名。 許容される最大の長さは 1,000 です。 |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

## <a name="level"></a>レベル

`level` クラス地物では、設定した高度にある建物の領域を定義します。 たとえば、[`units`](#unit) などの一連の地物を含む建物のフロア。  

**ジオメトリの種類**: MultiPolygon

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        | [category.Id](#category)    |true      | [`category`](#category) 地物の ID。|
| `ordinal`          | 整数 (integer)        | true     | レベル番号。 移動方向に役立つフロアの相対的な順序を決定するために [`verticalPenetration`](#verticalpenetration) 地物によって使用されるレベル番号。 一般的な方法としては、1 階を 0 として始めます。 階が上がるごとに +1 を、階が下がるごとに -1 を加算します。 より上の物理フロアがより大きな順序値で表される限り、任意の数値でモデル化できます。 |
| `abbreviatedName`          | string        | false     | エレベーターのボタンに見られるような 4 文字の省略されたレベル名。  許容される最大の長さは 1,000 です。|
| `heightAboveFacilityAnchor`          | double         | false     | [`facility.anchorHeightAboveSeaLevel`](#facility)より上のレベルのフロアの垂直距離 (メートル)。 |
| `verticalExtent`          | double         | false     | レベルの垂直範囲 (メートル)。 指定しない場合、既定で [`facility.defaultLevelVerticalExtent`](#facility) に設定されます。|
|`name` |    string |    false |    ローカル言語による地物の名前。  許容される最大の長さは 1,000 です。|
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。  許容される最大の長さは 1,000 です。|
|`nameAlt` |    string |    false |   地物に使用される代替名。  許容される最大の長さは 1,000 です。|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

## <a name="facility"></a>facility

`facility` 地物クラスでは、サイト、建物のフットプリントなどの領域を定義します。

**ジオメトリの種類**: MultiPolygon

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 地物の ID。|
|`occupants`         | [directoryInfo.Id](#directoryinfo) の配列 |    false |    [directoryInfo](#directoryinfo) 地物の ID。 地物内の 1 つまたは複数の居住者を表すために使用されます。 |
|`addressId`         | [directoryInfo.Id](#directoryinfo)  | true     | [directoryInfo](#directoryinfo) 地物の ID。 地物の住所を表すために使用されます。|
|`name` |    string |    false |    ローカル言語による地物の名前。 許容される最大の長さは 1,000 です。 |
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。 許容される最大の長さは 1,000 です。 |
|`nameAlt` |    string |    false |   地物に使用される代替名。  許容される最大の長さは 1,000 です。|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|
|`anchorHeightAboveSeaLevel` |  double | false | 海面より上のアンカー ポイントの高さ (メートル)。 海面は、EGM 2008 で定義されています。|
|`defaultLevelVerticalExtent` |  double| false | レベルの垂直範囲の既定値 (メートル)。|

## <a name="verticalpenetration"></a>verticalPenetration

`verticalPenetration` クラス地物では、セットで使用される場合に、レベル間の垂直方向の移動方法を表す領域を定義します。 階段、エレベーターなどのモデル化に使用できます。 ジオメトリは、ユニットおよびその他の垂直貫入地物をオーバーラップできます。

**ジオメトリの種類**: 多角形

:::zone pivot="facility-ontology-v1"

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 地物の ID。|
| `setId`          | string       | true     | 複数のレベルをつなぐためには、垂直貫入地物をセットで使用する必要があります。 同じセット内の垂直貫入地物は、同一であると見なされます。 `setId` には任意の文字列を指定でき、大文字と小文字が区別されます。 `setId` として GUID を使用することをお勧めします。  許容される最大の長さは 1,000 です。|
| `levelId`          | [level.Id](#level)         | true     | level 地物の ID。 |
|`direction`         |  string enum [ "both", "lowToHigh", "highToLow", "closed" ]| false     | この地物で許可される移動方向。 [`level`](#level) 地物の順序属性は、低い順序および高い順序を決定するために使用されます。|
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |ユニットを横断することができる移動エージェントの種類を示します。 指定しない場合、ユニットは任意のナビゲーション エージェントによってスキャン可能です。 |
|`nonPublic`        |  boolean| false       | `true` の場合、ユニットは、特権のあるユーザーによってのみナビゲートできます。  既定値は `false` です。 |
|`name` |    string |    false |    ローカル言語による地物の名前。  許容される最大の長さは 1,000 です。|
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。  許容される最大の長さは 1,000 です。|
|`nameAlt` |    string |    false |   地物に使用される代替名。 許容される最大の長さは 1,000 です。 |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

:::zone-end

:::zone pivot="facility-ontology-v2"

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 地物の ID。|
| `setId`          | string       | true     | 複数のレベルをつなぐためには、垂直貫入地物をセットで使用する必要があります。 同じセット内の垂直貫入地物がつながれます。 `setId` には任意の文字列を指定でき、大文字と小文字が区別されます。 `setId` として GUID を使用することをお勧めします。 許容される最大の長さは 1,000 です。 |
| `levelId`          | [level.Id](#level)         | true     | level 地物の ID。 |
|`direction`         |  string enum [ "both", "lowToHigh", "highToLow", "closed" ]| false     | この地物で許可される移動方向。 [`level`](#level) 地物の順序属性は、低い順序および高い順序を決定するために使用されます。|
|`name` |    string |    false |    ローカル言語による地物の名前。  許容される最大の長さは 1,000 です。|
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。  許容される最大の長さは 1,000 です。|
|`nameAlt` |    string |    false |   地物に使用される代替名。 許容される最大の長さは 1,000 です。 |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

:::zone-end

## <a name="opening"></a>開いています

`opening` クラス地物では、2 つのユニット間、または `unit` と `verticalPenetration` の間のスキャン可能な境界を定義します。

**ジオメトリの種類**: LineString

:::zone pivot="facility-ontology-v1"

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        |[category.Id](#category)     |true      | category 地物の ID。|
| `levelId`          | [level.Id](#level)        | true     | level 地物の ID。 |
| `isConnectedToVerticalPenetration` | boolean | false | この地物を、その左右いずれかにある `verticalPenetration` 地物につなぐかどうか。 既定値は `false` です。 |
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |ユニットを横断することができる移動エージェントの種類を示します。 指定しない場合、ユニットは任意のナビゲーション エージェントによってスキャン可能です。 |
| `accessRightToLeft`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | 開口部を右から左に通過する際のアクセス方法。 左および右は、地物ジオメトリの頂点によって決定され、最初の頂点に立ち、2 番目の頂点に向かいます。 このプロパティを省略すると、アクセス制限がないことを意味します。|
| `accessLeftToRight`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | 開口部を左から右に通過する際のアクセス方法。 左および右は、地物ジオメトリの頂点によって決定され、最初の頂点に立ち、2 番目の頂点に向かいます。 このプロパティを省略すると、アクセス制限がないことを意味します。|
| `isEmergency` | boolean | false | `true` の場合、開口部は緊急時にのみナビゲートできます。 既定値は `false` です |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

:::zone-end

:::zone pivot="facility-ontology-v2"

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        |[category.Id](#category)     |true      | category 地物の ID。|
| `levelId`          | [level.Id](#level)        | true     | level 地物の ID。 |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

:::zone-end

## <a name="directoryinfo"></a>directoryInfo

`directoryInfo` オブジェクト クラス地物では、ユニット、施設、またはユニットや施設の居住者の名前、住所、電話番号、Web サイト、営業時間を定義します。

**ジオメトリの種類**: なし

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`streetAddress`        |string    |false    |住所の番地部分。  許容される最大の長さは 1,000 です。 |
|`unit`        |string    |false    |住所のユニット番号部分。  許容される最大の長さは 1,000 です。 |
|`locality`|    string|    false    |住所の地域。 たとえば、市、地方自治体、村など。 許容される最大の長さは 1,000 です。|
|`adminDivisions`|    string|    false    |住所の行政区分の部分。最小から最大の順 (郡、都道府県、国)。 例: ["King", "Washington", "USA" ] または ["West Godavari", "Andhra Pradesh", "IND" ]。 許容される最大の長さは 1,000 です。|
|`postalCode`|    string |    false    |住所の郵便番号部分。 許容される最大の長さは 1,000 です。|
|`name` |    string |    false |    ローカル言語による地物の名前。  許容される最大の長さは 1,000 です。|
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。 許容される最大の長さは 1,000 です。 |
|`nameAlt` |    string |    false |   地物に使用される代替名。 許容される最大の長さは 1,000 です。 |
|`phoneNumber` |    string |    false |    電話番号。 許容される最大の長さは 1,000 です。 |
|`website` |    string |    false |  Web サイトの URL。 許容される最大の長さは 1,000 です。 |
|`hoursOfOperation` |    string |    false |   [オープン ストリート マップ仕様](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification)に従ったテキストとしての営業時間。 許容される最大の長さは 1,000 です。 |

## <a name="pointelement"></a>pointElement

`pointElement` は、救急用具やスプリンクラー ヘッドなどのユニット内のポイント地物を定義するクラス地物です。

**ジオメトリの種類**: MultiPoint

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        |[category.Id](#category)      |true      | [`category`](#category) 地物の ID。|
| `unitId`          | string     | true     | この地物を含む [`unit`](#unit) 地物の ID。  許容される最大の長さは 1,000 です。|
| `isObstruction`          | boolean (既定値は `null` です)。 | false     | `true` の場合、この地物は、含まれているユニット地物までルーティングする間に回避される障害物を表します。 |
|`name` |    string |    false |    ローカル言語による地物の名前。  許容される最大の長さは 1,000 です。|
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。 許容される最大の長さは 1,000 です。 |
|`nameAlt` |    string |    false |   地物に使用される代替名。  許容される最大の長さは 1,000 です。|

## <a name="lineelement"></a>lineElement

`lineElement` は、仕切り壁や窓などのユニット内の line 地物を定義するクラス地物です。

**ジオメトリの種類**: LinearMultiString

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        |[category.Id](#category)      |true      | [`category`](#category) 地物の ID。|
| `unitId`          | string     | true     | この地物を含む [`unit`](#unit) 地物の ID。 許容される最大の長さは 1,000 です。 |
| `isObstruction`          | boolean (既定値は `null` です)。| false     | `true` の場合、この地物は、含まれているユニット地物までルーティングする間に回避される障害物を表します。 |
|`name` |    string |    false |    ローカル言語による地物の名前。 許容される最大の長さは 1,000 です。 |
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。 許容される最大の長さは 1,000 です。 |
|`nameAlt` |    string |    false |   地物に使用される代替名。 許容される最大の長さは 1,000 です。 |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|
|`obstructionArea` |   [Polygon](/rest/api/maps/wfs/get-feature#featuregeojson)| false | ルーティング中に回避される地物の簡略化されたジオメトリ (ライン ジオメトリが複雑な場合)。 `isObstruction` を true に設定する必要があります。|

## <a name="areaelement"></a>areaElement

`areaElement` は、下に開いている領域や、ユニット内の島などの障害物など、ユニット内の多角形地物を定義するクラス地物です。

**ジオメトリの種類**: MultiPolygon

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生した ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | 地物を別のデータセット内 (内部データベース内など) の別の地物と関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`categoryId`        |[category.Id](#category)      |true      | [`category`](#category) 地物の ID。|
| `unitId`          | string     | true     | この地物を含む [`unit`](#unit) 地物の ID。 許容される最大の長さは 1,000 です。 |
| `isObstruction`          | boolean | false     | `true` の場合、この地物は、含まれているユニット地物までルーティングする間に回避される障害物を表します。 |
|`obstructionArea` |  geometry: ["Polygon","MultiPolygon" ]| false | ルーティング中に回避される地物の簡略化されたジオメトリ (ライン ジオメトリが複雑な場合)。 `isObstruction` を true に設定する必要があります。|
|`name` |    string |    false |    ローカル言語による地物の名前。 許容される最大の長さは 1,000 です。 |
|`nameSubtitle` |    string |    false |   地物の `name` の下に表示されるサブタイトル。 名前を別の言語で表示するためなどに使用できます。  許容される最大の長さは 1,000 です。|
|`nameAlt` |    string |    false |   地物に使用される代替名。  許容される最大の長さは 1,000 です。|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 地物をポイントとして表す [GeoJSON Point ジオメトリ](/rest/api/maps/wfs/get-feature#featuregeojson)。 地物のラベルを位置付けるために使用できます。|

## <a name="category"></a>category

`category` クラス地物では、カテゴリ名を定義します。 例: "room.conference"。

**ジオメトリの種類**: なし

:::zone pivot="facility-ontology-v1"

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生したカテゴリの元の ID。 許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | カテゴリを別のデータセット内 (内部データベース内など) の別のカテゴリと関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`name` |    string |    true |   カテゴリの名前。 "." を使用してカテゴリの階層を表すことをお勧めします。 例: "room.conference", "room.privateoffice"。 許容される最大の長さは 1,000 です。 |
| `routeThroughBehavior` | boolean | false | 地物を通り抜けに使用できるかどうかを決定します。|
|`isRoutable`        | boolean (既定値は `null` です)。                  | false    |  地物をルーティング グラフの一部にする必要があるかどうかを決定します。 `true` に設定すると、ユニットをルーティング エクスペリエンス内の出発地および目的地、または中間地点として使用できます。 |

:::zone-end

:::zone pivot="facility-ontology-v2"

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`originalId`        | string     |true      | クライアント データから派生したカテゴリの元の ID。  許容される最大の長さは 1,000 です。|
|`externalId`        | string     |true      | カテゴリを別のデータセット内 (内部データベース内など) の別のカテゴリと関連付けるためにクライアントによって使用される ID。 許容される最大の長さは 1,000 です。|
|`name` |    string |    true |   カテゴリの名前。 "." を使用してカテゴリの階層を表すことをお勧めします。 例: "room.conference", "room.privateoffice"。 許容される最大の長さは 1,000 です。 |

:::zone-end
