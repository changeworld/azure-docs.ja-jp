---
title: Android マップへのバブル レイヤーの追加 | Microsoft Azure Maps
description: マップ上のポイントを固定サイズの円としてレンダリングする方法について説明します。 この目的で Azure Maps Android SDK を使用してバブル レイヤーを追加し、カスタマイズする方法を参照してください。
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 7506a2083a34832ee3f6f6222f86d35d10228728
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681379"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>マップにバブル レイヤーを追加する (Android SDK)

この記事では、データ ソースからのポイント データをマップ上のバブル レイヤーにレンダリングする方法について説明します。 バブル レイヤーは、固定ピクセル半径を持つ円としてポイントをマップ上にレンダリングするものです。

> [!TIP]
> バブル レイヤーの既定では、データ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` オプションを `eq(geometryType(), "Point")` に設定します。 MultiPoint フィーチャーも含める場合は、レイヤーの `filter` オプションを `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` に設定します。

## <a name="prerequisites"></a>前提条件

必ず、[クイック スタート:Android アプリの作成](quick-android-map.md)に関するドキュメントの手順を完了してください。 この記事のコード ブロックは、マップ `onReady` イベント ハンドラーに挿入できます。

## <a name="add-a-bubble-layer"></a>バブル レイヤーを追加する

次のコードでは、ポイントの配列がデータ ソースに読み込まれます。 次に、データ ポイントがバブル レイヤーに接続されます。 バブル レイヤーでは、各バブルが 5 ピクセルの半径、白の塗りつぶしの色でレンダリングされます。 また、青のストロークの色、6 ピクセルのストロークの幅になります。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

次のスクリーンショットは、上記のコードによって、バブル レイヤーにポイントがレンダリングされている状態を示しています。

![バブル レイヤーを使用してレンダリングされたポイントを含むマップ](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>バブル レイヤーでラベルを表示する

このコードは、バブル レイヤーを使用してマップ上にポイントをレンダリングする方法を示しています。 また、シンボル レイヤーを使用して、ラベルをレンダリングする方法を示しています。 シンボル レイヤーのアイコンを非表示にするには、`iconImage` オプションを `"none"` に設定します。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

次のスクリーンショットは、上記のコードにより、バブル レイヤーにポイントがレンダリングされ、シンボル レイヤーを使ってポイントのテキスト ラベルがレンダリングされた状態を示しています。

![バブル レイヤーを使用してレンダリングされたポイントと、シンボル レイヤーを使用したテキスト ラベルを含むマップ](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [フィーチャーの情報を表示する](display-feature-information-android.md)
