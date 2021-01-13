---
title: Android マップにシンボル レイヤーを追加する | Microsoft Azure Maps
description: マップにマーカーを追加する方法について説明します。 データ ソースのポイントベースのデータを含むシンボル レイヤーを、Azure Maps Android SDK を使用して追加する例を参照してください。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679355"
---
# <a name="add-a-symbol-layer-android-sdk"></a>シンボル レイヤーを追加する (Android SDK)

この記事では、Azure Maps Android SDK を使用して、マップ上にデータ ソースからのポイント データをシンボル レイヤーとしてレンダリングする方法を示します。 シンボル レイヤーは、ポイントを画像とテキストとしてマップ上にレンダリングします。

> [!TIP]
> シンボル レイヤーでは、既定ではデータ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` オプションを `eq(geometryType(), "Point")` に設定します。 MultiPoint フィーチャーも含める場合は、レイヤーの `filter` オプションを `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` に設定します。

## <a name="prerequisites"></a>[前提条件]

必ず、[クイック スタート:Android アプリの作成](quick-android-map.md)に関するドキュメントの手順を完了してください。 この記事のコード ブロックは、マップ `onReady` イベント ハンドラーに挿入できます。

## <a name="add-a-symbol-layer"></a>シンボル レイヤーを追加する

マップにシンボル レイヤーを追加する前に、いくつかの手順を行う必要があります。 まず、データ ソースを作成してマップに追加します。 シンボル レイヤーを追加します。 次に、データ ソースからデータを取得するために、データ ソースをシンボル レイヤーに渡します。 最後に、データをデータ ソースに追加して、レンダリングするものが存在するようにします。

次のコードは、マップが読み込まれた後に、そこに追加する必要がある内容を示しています。 このサンプルでは、シンボル レイヤーを使用してマップ上に 1 つのポイントがレンダリングされます。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

マップに追加できるポイント データには、次の 3 種類があります。

- GeoJSON Point ジオメトリ - このオブジェクトには、ポイントの座標だけが含まれ、それ以外は含まれません。 `Point.fromLngLat` 静的メソッドを使用すると、これらのオブジェクトを簡単に作成できます。
- GeoJSON MultiPoint ジオメトリ - このオブジェクトには、複数のポイントの座標が含まれ、それ以外は含まれません。 ポイントの配列を `MultiPoint` クラスに渡して、これらのオブジェクトを作成します。
- GeoJSON 機能 - このオブジェクトは、すべての GeoJSON ジオメトリと、ジオメトリに関連するメタデータを含むプロパティのセットで構成されます。

データの作成とマップへの追加の詳細については、「[データ ソースを作成する](create-data-source-android-sdk.md)」のドキュメントをご覧ください。

次のコード サンプルは、GeoJSON Point ジオメトリを作成して GeoJSON フィーチャーに渡し、`title` 値をそのプロパティに追加させます。 `title` プロパティは、マップ上のシンボル アイコンの上にテキストとして表示されます。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

次のスクリーンショットでは、上記のコードにより、シンボル レイヤーでアイコンとテキスト ラベルを使ってポイント フィーチャーがレンダリングされた状態を示しています。

![ポイント フィーチャーのアイコンとテキスト ラベルを表示したシンボル レイヤーを使用してポイントがレンダリングされたマップ](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> 既定では、重なっているシンボルがシンボル レイヤーによって非表示になることで、シンボルのレンダリングが最適化されます。 拡大すると、非表示のシンボルが表示されるようになります。 この機能を無効にして、すべてのシンボルを常にレンダリングするには、`iconAllowOverlap` および `textAllowOverlap` オプションを `true` に設定します。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>シンボル レイヤーにカスタム アイコンを追加する

シンボル レイヤーは WebGL を使用してレンダリングされます。 このため、アイコンの画像などのすべてのリソースを WebGL コンテキストに読み込む必要があります。 このサンプルでは、マップ リソースにカスタム アイコンを追加する方法を示します。 このアイコンを使用して、ポイント データをカスタム シンボルでマップ上にレンダリングします。 シンボル レイヤーの `textField` プロパティに式を指定する必要があります。 ここでは、気温プロパティをレンダリングします。 気温は数値であるため、文字列に変換する必要があります。 加えて、そこに "°F" を追加したいと思います。 `concat(Expression.toString(get("temperature")), literal("°F"))` という式を使えば、この連結を実行できます。

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

このサンプルでは、次の画像がアプリのドローアブル フォルダーに読み込まれています。

| ![にわか雨の天気アイコンの画像](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

次のスクリーンショットでは、上記のコードにより、シンボル レイヤーでカスタム アイコンと書式設定されたテキスト ラベルを使ってポイント フィーチャーがレンダリングされた状態を示しています。

![ポイント フィーチャーのカスタム アイコンと書式設定されたテキスト ラベルを表示したシンボル レイヤーを使用してポイントがレンダリングされたマップ](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> シンボル レイヤーを使用してテキストをレンダリングするだけの場合は、アイコンのオプションの `iconImage` プロパティを `"none"` に設定することでアイコンを非表示にすることができます。

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [フィーチャーの情報を表示する](display-feature-information-android.md)
