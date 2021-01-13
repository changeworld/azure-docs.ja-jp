---
title: Android マップへの多角形レイヤーの追加 | Microsoft Azure Maps
description: マップに多角形または円を追加する方法について説明します。 Azure Maps Android SDK を使用して幾何学図形をカスタマイズし、それらの更新や維持を容易にする方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679456"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>マップに多角形レイヤーを追加する (Android SDK)

この記事では、多角形レイヤーを使用してマップ上に `Polygon` および `MultiPolygon` フィーチャー ジオメトリの領域をレンダリングする方法を示します。

## <a name="prerequisites"></a>前提条件

必ず、[クイック スタート:Android アプリの作成](quick-android-map.md)に関するドキュメントの手順を完了してください。 この記事のコード ブロックは、マップ `onReady` イベント ハンドラーに挿入できます。

## <a name="use-a-polygon-layer"></a>多角形レイヤーを使用する

多角形レイヤーがデータ ソースに接続されていると、マップに読み込まれるときに、`Polygon` および `MultiPolygon` フィーチャーを含む領域がレンダリングされます。 多角形を作成し、データ ソースに追加し、`PolygonLayer` クラスを使用して多角形レイヤーでレンダリングします。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

次のスクリーンショットは、上記のコードによって、多角形レイヤーを使用して多角形の領域がレンダリングされている状態を示しています。

![塗りつぶし領域がレンダリングされた多角形](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>多角形と線レイヤーを同時に使用する

線レイヤーを使用して、多角形のアウトラインをレンダリングします。 次のコード サンプルでは、前の例のように多角形がレンダリングされますが、今度は線レイヤーが追加されます。 この線レイヤーは、データ ソースに接続される 2 番目のレイヤーです。  

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

次のスクリーンショットは、アウトラインが線レイヤーを使用してレンダリングされた多角形が、上記のコードによってレンダリングされている状態を示しています。

![塗りつぶし領域とアウトラインがレンダリングされた多角形](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> 線レイヤーを使用して多角形のアウトラインを設定する場合は、ポイントの各配列の起点および終点が同じになるように、必ず多角形のすべてのリングを閉じてください。 このように行わないと、線レイヤーは多角形の最後の点を最初の点に接続できなくなる可能性があります。

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)
