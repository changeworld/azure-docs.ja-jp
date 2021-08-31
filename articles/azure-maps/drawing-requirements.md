---
title: Microsoft Azure Maps Creator の Drawing パッケージの要件
description: 施設の設計ファイルをマップ データに変換するための Drawing パッケージの要件について説明します
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/02/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 2e6b380b040697a56179f5fec7f7d10796f035fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746650"
---
# <a name="drawing-package-requirements"></a>Drawing パッケージの要件

[Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)を使用することにより、アップロードした Drawing パッケージをマップ データに変換できます。 この記事では、Conversion API の Drawing パッケージの要件について説明します。 サンプル パッケージを表示するには、サンプルの [Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)をダウンロードします。

描画パッケージを準備する方法のガイドについては、「[Conversion Drawing Package Guide ](drawing-package-guide.md) (変換描画パッケージ ガイド)」を参照してください。


## <a name="prerequisites"></a>前提条件

この Drawing パッケージには、DWG 形式で保存された図面が含まれています。これは、Autodesk の AutoCAD® ソフトウェアのネイティブ ファイル形式です。

任意の CAD ソフトウェアを選択して、Drawing パッケージで図面を作成することができます。  

[Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)を使用すると、Drawing パッケージをマップ データに変換できます。 Conversion サービスは、AutoCAD DWG ファイル形式 `AC1032` で機能します。


## <a name="glossary-of-terms"></a>用語集

この記事を読む際に簡単に参照できるように、重要な用語と定義を次に示します。

| 期間  | 定義 |
|:-------|:------------|
| レイヤー | 描画ファイルからの AutoCAD DWG レイヤー。|
| Entity | 描画ファイルからの AutoCAD DWG レイヤー。 |
| Xref  | 外部参照としてプライマリ図面にアタッチされた AutoCAD DWG ファイル形式のファイル。  |
| Level | 設定した高度にある建物の領域。 たとえば、ビルのフロアなどです。 |
| 機能 | 変換サービスから生成される、ジオメトリとメタデータの情報を組み合わせたオブジェクトのインスタンス。 |
| 地物クラス | 地物の一般的なブループリント。 たとえば、*ユニット* は地物クラスであり、*オフィス* は地物です。 |

## <a name="drawing-package-structure"></a>Drawing パッケージの構造

Drawing パッケージは、次のファイルを含む .zip アーカイブです。

- AutoCAD DWG ファイル形式の DWG ファイル。
- Drawing パッケージ内の DWG ファイルを記述する _manifest.json_ ファイル。

Drawing パッケージは、.zip 拡張子の付いた 1 つのアーカイブ ファイルに圧縮する必要があります。 DWG ファイルはパッケージ内で任意の方法で編成できますが、マニフェスト ファイルは zip 圧縮されたパッケージのルート ディレクトリに配置する必要があります。 次のセクションでは、DWG ファイル、マニフェスト ファイル、およびこれらのファイルのコンテンツの要件について詳しく説明します。 サンプル パッケージを表示するには、[サンプルの Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)をダウンロードします。

## <a name="dwg-file-conversion-process"></a>DWG ファイル変換プロセス

[Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)は、各 DWG ファイルに対して次の処理を実行します。

- 地物クラスの抽出
    - Levels
    - Units
    - ゾーン
    - 開口部
    - 壁
    - 垂直貫入
- "*施設*" 地物の生成  
- 他の地物から参照される既定のカテゴリとなる地物の最小のセットを生成します。
    - room
    - structure
    - wall
    - opening.door
    - ゾーン
    - facility
    
## <a name="dwg-file-requirements"></a>DWG ファイルの要件

施設のレベルごとに 1 つの DWG ファイルが必要です。 1 つのレベルのすべてのデータは、1 つの DWG ファイルに含まれている必要があります。  外部参照 (_xrefs_) は、親図面にバインドする必要があります。 例えば、3 つのレベルを持つ施設の場合、Drawing パッケージ内に 3 つの DWG ファイルがあります。

各 DWG ファイルは、次の要件に従う必要があります。

- DWG ファイルは、_Exterior_ および _Unit_ レイヤーを定義する必要があります。 必要に応じて、_Wall_、_Door_、_UnitLabel_、_Zone_、および _ZoneLabel_ のレイヤーを定義することもできます。
- DWG ファイルには、複数のレベルの地物を含めることはできません。
- DWG ファイルには、複数の施設の地物を含めることはできません。
- DWG は、Drawing パッケージ内の他の DWG ファイルと同じ測定系と測定単位を参照する必要があります。

## <a name="dwg-layer-requirements"></a>DWG レイヤーの要件

各 DWG レイヤーは、次の規則に従う必要があります。

- レイヤーに含めることができるのは、1 クラスの地物のみです。 たとえば、ユニットと壁を同じレイヤーに含めることはできません。
- 1 つのクラスの地物を複数のレイヤーで表すことができます。
- 自己交差するポリゴンは許可されていますが、自動的に修復されます。 これらが修復されると、[Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)から警告が生成されます。 修復された結果は期待された結果と一致しない可能性があるため、手動で検査することをお勧めします。
- 各レイヤーには、サポートされるエンティティ型のリストがあります。 その他のエンティティ型がレイヤー内にあった場合は無視されます。 たとえば、壁のレイヤーでは、テキスト エンティティはサポートされていません。

レイヤーごとにサポートされているエンティティの種類と変換されたマップ地物の概要を次の表に示します。 レイヤーにサポートされていないエンティティ型が含まれている場合、[Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)ではそのエンティティは無視されます。  

| レイヤー | エンティティの種類 | 変換された地物 |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | 多角形、ポリライン (閉)、円、楕円 (閉) | Levels
| [単位](#unit-layer) |  多角形、ポリライン (閉)、円、楕円 (閉) |  ユニットと垂直貫入
| [Wall](#wall-layer)  | 多角形、ポリライン (閉)、円、楕円 (閉)、構造体 |
| [Door](#door-layer) | 多角形、ポリライン、線、円弧、円 | 開口部
| [ゾーン](#zone-layer) | 多角形、ポリライン (閉)、円、楕円 (閉) | ゾーン
| [UnitLabel](#unitlabel-layer) | テキスト (単一行) | 適用不可。 このレイヤーでは、Unit レイヤーのユニット地物にのみプロパティを追加できます。 詳細については、「[UnitLabel レイヤー](#unitlabel-layer)」を参照してください。
| [ZoneLabel](#zonelabel-layer) | テキスト (単一行) | 適用不可。 このレイヤーでは、ZonesLayer のゾーン地物にのみプロパティを追加できます。 詳細については、「[ZoneLabel レイヤー](#zonelabel-layer)」を参照してください。

以下のセクションでは、各レイヤーの要件について説明します。

### <a name="exterior-layer"></a>Exterior レイヤー

各レベルの DWG ファイルには、そのレベルの境界を定義するレイヤーが含まれている必要があります。 このレイヤーは *Exterior* レイヤーと呼ばれます。 たとえば、施設に 2 つのレベルが含まれている場合、施設には 2 つの DWG ファイルが必要であり、各ファイルには Exterior レイヤーがあります。

Exterior レイヤーにあるエンティティ図面の数に関係なく、[最終的な施設データセット](tutorial-creator-indoor-maps.md#create-a-feature-stateset)には、各 DWG ファイルの 1 レベルの地物のみが含まれます。 追加として:

- 外装は、多角形、ポリライン (閉)、円、または 楕円 (閉) として描画する必要があります。
- 外装は重ねることができますが、1 つのジオメトリに分解されます。
- 結果として得られるレベル地物は、4 平方メートル以上である必要があります。
- 結果として得られるレベル地物は、400,000 平方メートル以下である必要があります。

レイヤーに複数の重なり合うポリラインが含まれている場合、ポリラインは 1 つのレベル地物に分解されます。 代わりに、レイヤーに複数の重ならないポリラインが含まれている場合、結果のレベル地物は複数の多角形で表現されます。

Exterior レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)の Outline レイヤーとして確認することができます。

### <a name="unit-layer"></a>Unit レイヤー

各レベルの DWG ファイルでは、ユニットを含むレイヤーが定義されます。 ユニットは、オフィス、廊下、階段、エレベーターなど、建物内の移動可能なスペースです。 `VerticalPenetrationCategory` プロパティが定義されている場合、エレベーターや階段など、複数のレベルにまたがる移動可能なユニットは、垂直貫入地物に変換されます。 互いに重なる垂直貫入地物には、1 つの `setid` が割り当てられます。

Unit レイヤーは次の要件に従う必要があります。

- ユニットは、多角形、ポリライン (閉)、円、または 楕円 (閉) として描画する必要があります。
- ユニットは、施設の外装の境界内に収まっている必要があります。
- ユニットの一部を重ねることはできません。
- ユニットに自己交差するジオメトリを含めることはできません。

UnitLabel レイヤーにテキスト オブジェクトを作成してユニットに名前を付け、ユニットの境界内にオブジェクトを配置します。 詳細については、「[UnitLabel レイヤー](#unitlabel-layer)」を参照してください。

Unit レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)で確認できます。

### <a name="wall-layer"></a>Wall レイヤー

各レベルの DWG ファイルには、壁、柱、その他の建築構造の物理的な範囲を定義するレイヤーを含めることができます。

- 壁は、多角形、ポリライン (閉)、円、または 楕円 (閉) として描画する必要があります。
- 1 つまたは複数の Wall レイヤーには、建築構造として解釈されるジオメトリのみを含める必要があります。

Wall レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)で確認できます。

### <a name="door-layer"></a>Door レイヤー

ドアを含む DWG レイヤーを含めることができます。 各ドアは、Unit レイヤーのユニットの端と重なる必要があります。

Azure Maps データセットのドアの開口部は、複数のユニットの境界と重なる単一の線分として表されます。 次の図は、Door レイヤーのジオメトリをデータセットの開口部地物に変換する方法を示しています。

![開口部を生成する手順を示す 4 つのグラフィックス](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zone レイヤー

各レベルの DWG ファイルには、ゾーンの物理的な範囲を定義する Zone レイヤーを含めることができます。 ゾーンは、名前を付けてレンダリングできる、移動不可能な領域です。 ゾーンは複数のレベルにまたがることができ、zoneSetId プロパティを使用してグループ化されます。

- ゾーンは、多角形、ポリライン (閉)、または 楕円 (閉) として描画する必要があります。
- ゾーンは重複していてもかまいません。
- ゾーンは、施設の外装の内側または外側に配置することができます。

ZoneLabel レイヤーにテキスト オブジェクトを作成し、ゾーンの境界内にテキスト オブジェクトを配置して、ゾーンに名前を付けます。 詳細については、「[ZoneLabel レイヤー](#zonelabel-layer)」を参照してください。

Zone レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)で確認できます。

### <a name="unitlabel-layer"></a>UnitLabel レイヤー

各レベルの DWG ファイルには、UnitLabel レイヤーを含めることができます。 UnitLabel レイヤーでは、Unit レイヤーから抽出されたユニットに name プロパティを追加します。 name プロパティを持つユニットには、マニフェスト ファイルで詳細をさらに指定できます。

- ユニット ラベルは単一行のテキスト エンティティである必要があります。
- ユニット ラベルは、ユニットの境界内に完全に収まる必要があります。
- UnitLabel レイヤーのユニットに複数のテキスト エンティティを含めることはできません。

UnitLabel レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)で確認できます。

### <a name="zonelabel-layer"></a>ZoneLabel レイヤー

各レベルの DWG ファイルには、ZoneLabel レイヤーを含めることができます。 このレイヤーでは、Zone レイヤーから抽出されたゾーンに name プロパティを追加します。 name プロパティを持つゾーンには、マニフェスト ファイルで詳細をさらに指定できます。

- ゾーン ラベルは単一行のテキスト エンティティである必要があります。
- ゾーン ラベルは、ユニットの境界内に収まる必要があります。
- ZoneLabel レイヤーのゾーンに複数のテキスト エンティティを含めることはできません。

ZoneLabel レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)で確認できます。

## <a name="manifest-file-requirements"></a>マニフェスト ファイルの要件

zip フォルダーには、ディレクトリのルート レベルにマニフェスト ファイルが格納されている必要があります。また、ファイル名は **manifest.json** にする必要があります。 これには、[Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)でコンテンツを解析できる DWG ファイルについて記述されています。 マニフェストに指定されたファイルのみが取り込まれます。 zip フォルダー内にあっても、マニフェストに適切に登録されていないファイルは無視されます。

マニフェスト ファイルの `buildingLevels` オブジェクト内のファイル パスは、zip フォルダーのルートからの相対パスにする必要があります。 DWG ファイル名は、施設レベルの名前と正確に一致している必要があります。 たとえば、"Basement" レベルの DWG ファイルは "Basement.dwg" です。 level 2 の DWG ファイルは "level_2.dwg" です。 レベル名にスペースが含まれている場合は、アンダースコアを使用します。

マニフェスト オブジェクトの使用には要件がありますが、すべてのオブジェクトが必要なわけではありません。 [Azure Maps Conversion サービス](/rest/api/maps/v2/conversion) バージョン 1.1 の必須オブジェクトと省略可能なオブジェクトを次の表に示します。

>[!NOTE]
> 特に指定されていない限り、プロパティの型が string のすべてのプロパティでは最大 1,000 文字を使用できます。


| Object | 必須 | 説明 |
| :----- | :------- | :------- |
| `version` | true |マニフェスト スキーマのバージョン。 現時点では、バージョン 1.1 のみがサポートされています。|
| `directoryInfo` | true | 施設の地理情報と連絡先情報の概要を示します。 また、居住者の地理情報と連絡先情報の概要を示すためにも使用できます。 |
| `buildingLevels` | true | 建物のレベルと、レベルの設計を含むファイルを指定します。 |
| `georeference` | true | 施設の図面の数値的な地理情報が含まれています。 |
| `dwgLayers` | true | レイヤーの名前が列挙されています。各レイヤーにはその地物の名前が列挙されています。 |
| `unitProperties` | false | ユニット地物に関する、より多くのメタデータを挿入するために使用できます。 |
| `zoneProperties` | false | ゾーン地物に関する、より多くのメタデータを挿入するために使用できます。 |

以下のセクションでは、各オブジェクトの要件について詳しく説明します。

### `directoryInfo`

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
| `name`      | string | true   |  建物の名前。 |
| `streetAddress`|    string |    false    | 建物の住所。 |
|`unit`     | string    |  false    |  建物内のユニット。 |
| `locality` |    string |    false |    都市、町、エリア、近隣、または地域の名前。|
| `adminDivisions` |    文字列の JSON 配列 |    false     | 住所の表記を含む配列。 例: (国、州) ISO 3166 の国番号と ISO 3166-2 の州または領域番号を使用します。 |
| `postalCode` |    string    | false    | 郵便物の仕分用番号。 |
| `hoursOfOperation` |    string |     false | [OSM の Opening Hours](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) 形式に準拠しています。 |
| `phone`    | string |    false |    建物に関連付けられている電話番号。 |
| `website`    | string |    false    | 建物に関連付けられている Web サイト。 |
| `nonPublic` |    [bool]    | false | 建物が一般公開されているかどうかを指定するフラグ。 |
| `anchorLatitude` | numeric |    false | 施設アンカー (画鋲) の緯度。 |
| `anchorLongitude` | numeric |    false | 施設アンカー (画鋲) の経度。 |
| `anchorHeightAboveSeaLevel`  | numeric | false | 施設の地表レベルを基準とする 1 階の高さ (メートル単位)。 |
| `defaultLevelVerticalExtent` | numeric | false | レベルの `verticalExtent` が定義されていない場合に使用する、この施設のレベルの既定の高さ (厚さ)。 |

### `buildingLevels`

`buildingLevels` オブジェクトには、建物レベルの JSON 配列が含まれています。

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`levelName`    |string    |true |    わかりやすいレベル名。 次に例を示します。フロア 1、ロビー、ブルー パーキング、または地下。|
|`ordinal` | 整数 (integer) |    true | レベルの垂直方向の順序を決定します。 すべての施設には、ordinal 0 のレベルが必要です。 |
|`heightAboveFacilityAnchor` | numeric | false |    アンカーより上のレベルの高さ (メートル単位)。 |
| `verticalExtent` | numeric | false | レベルの床から天井までの高さ (厚さ) (メートル単位)。 |
|`filename` |    string |    true |    建物レベルの CAD 図面のファイル システム パス。 建物の zip ファイルのルートを基準とした相対パスにする必要があります。 |

### `georeference`

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`lat`    | numeric |    true |    施設図面の原点の緯度を表す 10 進数表現。 起点の座標は WGS84 Web メルカトル (`EPSG:3857`) 内にある必要があります。|
|`lon`    |numeric|    true|    施設図面の原点の経度を表す 10 進数表現。 起点の座標は WGS84 Web メルカトル (`EPSG:3857`) 内にある必要があります。 |
|`angle`|    numeric|    true|   真北と図面の垂直 (Y) 軸との間の時計回りの角度 (度単位)。   |

### `dwgLayers`

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`exterior`    |文字列の配列|    true|    外装の建物プロファイルを定義するレイヤーの名前。|
|`unit`|    文字列の配列|    true|    ユニットを定義するレイヤーの名前。|
|`wall`|    文字列の配列    |false|    壁を定義するレイヤーの名前。|
|`door`    |文字列の配列|    false   | ドアを定義するレイヤーの名前。|
|`unitLabel`    |文字列の配列|    false    |ユニットの名前を定義するレイヤーの名前。|
|`zone` | 文字列の配列    | false    | ゾーンを定義するレイヤーの名前。|
|`zoneLabel` | 文字列の配列 |     false |    ゾーンの名前を定義するレイヤーの名前。|

### `unitProperties`

`unitProperties` オブジェクトには、ユニットのプロパティの JSON 配列が含まれています。

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|`unitName`    |string    |true    |この `unitProperty` レコードに関連付けるユニットの名前。 このレコードは、`unitName` と一致するラベルが `unitLabel` レイヤーで見つかった場合にのみ有効です。 |
|`categoryName`|    string|    false    |ユニットの目的。 指定されたレンダリング スタイルで使用できる値の一覧については、[こちら](https://atlas.microsoft.com/sdk/javascript/indoor/0.1/categories.json)を参照してください。 |
|`occupants`    |directoryInfo オブジェクトの配列 |false    |ユニットの occupants の一覧。 |
|`nameAlt`|    string|    false|    ユニットの代替名。 |
|`nameSubtitle`|    string    |false|    ユニットのサブタイトル。 |
|`addressRoomNumber`|    string|    false|    ユニットの部屋、ユニット、アパートメント、またはスイートの番号。|
|`verticalPenetrationCategory`|    string|    false| このプロパティが定義されている場合、結果の地物はユニットではなく垂直貫入 (VRT) になります。 垂直貫入を使用すると、その上のレベルまたは下のレベルにある他の垂直貫入地物に移動できます。 垂直貫入は[カテゴリ](https://aka.ms/pa-indoor-spacecategories)名です。 このプロパティが定義されている場合、`categoryName` プロパティは `verticalPenetrationCategory` でオーバーライドされます。 |
|`verticalPenetrationDirection`|    string|    false    |`verticalPenetrationCategory` が定義されている場合は、必要に応じて有効な移動方向を定義します。 使用できる値は、`lowToHigh`、`highToLow`、`both`、`closed` です。 既定値は `both` です。 値は、大文字小文字が区別されます。|
| `nonPublic` | [bool] | false | ユニットが一般公開されているかどうかを示します。 |
| `isRoutable` | [bool] | false | このプロパティが `false` に設定されている場合、ユニットへの移動も通過もできません。 既定値は `true` です。 |
| `isOpenArea` | [bool] | false | ユニットに開口部をアタッチすることなく、移動エージェントはユニットに入ることができます。 既定では、この値は開口部がないユニットでは `true` に設定され、開口部があるユニットでは `false` に設定されています。 開口部がないユニットに対して `isOpenArea` を手動で `false` に設定すると、結果として得られるユニットに移動エージェントが到達できないため、警告が表示されます。|

### `zoneProperties`

`zoneProperties` オブジェクトには、ゾーンのプロパティの JSON 配列が含まれています。

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|zoneName        |string    |true    |`zoneProperty` レコードに関連付けるゾーンの名前。 このレコードは、`zoneName` と一致するラベルがゾーンの `zoneLabel` レイヤーで見つかった場合にのみ有効です。  |
|categoryName|    string|    false    |ゾーンの目的。 指定されたレンダリング スタイルで使用できる値の一覧については、[こちら](https://atlas.microsoft.com/sdk/javascript/indoor/0.1/categories.json)を参照してください。|
|zoneNameAlt|    string|    false    |ゾーンの代替名。  |
|zoneNameSubtitle|    string |    false    |ゾーンのサブタイトル。 |
|zoneSetId|    string |    false    | 複数のゾーン間の関係を確立して、それらがグループとしてクエリまたは選択できるようにするためのセット ID。 たとえば、複数のレベルにまたがるゾーンです。 |

### <a name="sample-drawing-package-manifest"></a>サンプル Drawing パッケージ マニフェスト

サンプル Drawing パッケージのマニフェスト ファイルを下に示します。 パッケージ全体をダウンロードするには、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)を参照してください。

#### <a name="manifest-file"></a>マニフェスト ファイル:

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>次の手順

Drawing パッケージが要件を満たしたら、[Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)を使用してパッケージをマップ データセットに変換することができます。 次に、そのデータセットを使用し、屋内マップ モジュールを使って屋内マップを生成できます。

> [!div class="nextstepaction"]
> [Creator Facility Ontology](creator-facility-ontology.md)

> [!div class="nextstepaction"]
> [屋内マップ用の Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [描画パッケージ ガイド](drawing-package-guide.md)

> [!div class="nextstepaction"]
>[フロア ガイド用の Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [チュートリアル:Creator 屋内マップを作成する](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [屋内マップの動的スタイル設定](indoor-map-dynamic-styling.md)