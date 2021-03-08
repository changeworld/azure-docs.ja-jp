---
title: Android マップへの線レイヤーの追加 | Microsoft Azure Maps
description: マップに線を追加する方法について説明します。 Azure Maps Android SDK を使用し、マップに線レイヤーを追加したり、シンボルやカラー グラデーションで線をカスタマイズしたりする例について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 3e68be79a4405af103512a9009187857a0d9af39
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681355"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>マップに線レイヤーを追加する (Android SDK)

線レイヤーを使用すると、マップ上にパスまたはルートとして `LineString` および `MultiLineString` フィーチャーをレンダリングできます。 また、線レイヤーを使用して、`Polygon` および `MultiPolygon` フィーチャーのアウトラインをレンダリングすることもできます。 データ ソースは、レンダリングするデータを提供するために線レイヤーに接続されます。

> [!TIP]
> 線レイヤーの既定では、データ ソース内の多角形と線の座標がレンダリングされます。 LineString ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` オプションを `eq(geometryType(), "LineString")` に設定します。 MultiLineString フィーチャーも含める場合は、レイヤーの `filter` オプションを `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` に設定します。

## <a name="prerequisites"></a>[前提条件]

必ず、[クイック スタート:Android アプリの作成](quick-android-map.md)に関するドキュメントの手順を完了してください。 この記事のコード ブロックは、マップ `onReady` イベント ハンドラーに挿入できます。

## <a name="add-a-line-layer"></a>線レイヤーを追加する

次のコードは、線の作成方法を示しています。 データ ソースに線を追加した後、`LineLayer` クラスを使用して、線レイヤーで描画します。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

次のスクリーンショットは、上記のコードによって線レイヤーに線が描画されていることを示しています。

![線レイヤーを使用して描画された線を持つマップ](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>データドライブ線スタイル

次のコードでは、2 つの線フィーチャーを作成し、各線にプロパティとして速度制限の値を追加します。 線レイヤーでは、速度制限の値に基づいて、データドライブ スタイル式による色が使用されます。 線データは道路に沿ってオーバーレイされるため、次のコードではラベル レイヤーの下に線レイヤーを追加して、道路ラベルを明確に読み取れるようにします。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

次のスクリーンショットは、上記のコードによって線レイヤーに 2 つの線が描画されていることを示しています。それぞれの色は、線フィーチャーのプロパティに基づいてデータドリブン スタイルの式から取得されます。

![線レイヤーに描画されたデータドライブの線を持つマップ](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-symbols-along-a-line"></a>線に沿ってシンボルを追加する

このサンプルでは、マップ上の線に沿って矢印アイコンを追加する方法を示します。 シンボル レイヤーを使用する場合は、`symbolPlacement` オプションを `SymbolPlacement.LINE` に設定します。 このオプションを選択すると、線に沿って記号がレンダリングされ、アイコンが回転します (0 度 = 右)。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498))));

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

このサンプルでは、次の画像がアプリのドローアブル フォルダーに読み込まれています。

| ![紫色の矢印アイコンの画像](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

次のスクリーンショットは、上記のコードによって線とともに矢印アイコンが表示されていることを示しています。

![線レイヤーに描画されたデータドライブの線と矢印を持つマップ](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
