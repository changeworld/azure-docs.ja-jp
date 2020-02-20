---
title: マップのデータ ソースを作成する | Microsoft Azure Maps
description: この記事では、データ ソースを作成し、Microsoft Azure Maps Web SDK を使用してマップに追加する方法を示します。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190229"
---
# <a name="create-a-data-source"></a>データ ソースを作成する

Azure Maps Web SDK では、データがデータ ソースに格納されます。 データ ソースを使用すると、クエリとレンダリングのデータ操作が最適化されます。 現在、次の 2 種類のデータ ソースがあります。

**GeoJSON データ ソース**

GeoJSON ベースのデータ ソースは、`DataSource` クラスを使用してローカルでデータを読み込んで格納します。 GeoJSON データは、[atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) 名前空間に、手動で作成するかヘルパー クラスを使用して作成できます。 `DataSource` クラスには、ローカルまたはリモートの GeoJSON ファイルをインポートするための関数が用意されています。 リモートの GeoJSON ファイルは、CORS が有効なエンドポイント上でホストされている必要があります。 `DataSource` クラスには、ポイント データをクラスター化するための機能があります。 データは、`DataSource` クラスを使用して、簡単に追加、削除、および更新できます。


> [!TIP]
> `DataSource`内のすべてのデータを上書きするとします。 `clear` 関数を呼び出してから `add` 関数を呼び出すと、マップで再レンダリングが 2 回行われ、それによって少しの遅延が発生する場合があります。 代わりに、データ ソース内のすべてのデータを削除して置換する `setShapes` 関数を使用します。これにより、マップの再レンダリングが 1 回だけトリガーされます。

**ベクター タイル ソース**

ベクター タイル ソースには、ベクター タイル レイヤーにアクセスする方法が記述されています。 ベクター タイル ソースをインスタンス化するには、[VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) クラスを使用します。 ベクター タイル レイヤーはタイル レイヤーに似ていますが、同じではありません。 タイル レイヤーはラスター イメージです。 ベクター タイル レイヤーは、PBF 形式の圧縮ファイルです。 この圧縮ファイルには、ベクター マップ データと 1 つ以上のレイヤーが含まれています。 このファイルは、各レイヤーのスタイルに基づいて、クライアントでレンダリングおよびスタイル設定できます。 ベクター タイルのデータには、ポイント、線、および多角形の形式で地理的特徴が含まれています。 ベクター タイル レイヤーの使用には、ラスター タイル レイヤーよりも優れている点がいくつかあります。

 - ベクター タイルのファイル サイズは、通常、同等のラスター タイルよりはるかに小さくなります。 そのため、使用される帯域幅が少なくなります。 つまり、待機時間の短縮、より高速なマップ、ユーザー エクスペリエンスの向上を意味します。
 - ベクター タイルはクライアント上でレンダリングされるため、表示されているデバイスの解像度に適応できます。 結果として、レンダリングされるマップはより明確に定義され、鮮明なラベルが表示されます。
 - ベクター マップ内のデータのスタイルを変更しても、クライアントに新しいスタイルを適用できるので、データを再度ダウンロードする必要はありません。 これに対し、ラスター タイル レイヤーのスタイルを変更するには、通常、タイルをサーバーから読み込み、新しいスタイルを適用する必要があります。
 - データはベクター形式で配信されるので、データを準備するために必要なサーバー側の処理が少なくなります。 つまり、新しいデータをより速く使用できるようになります。

ベクター ソースを使用するすべてのレイヤーで `sourceLayer` 値を指定する必要があります。

作成されたデータ ソースは、`map.sources` プロパティ ([SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)) を使用してマップに追加できます。 次のコードは、`DataSource` を作成してマップに追加する方法を示しています。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps は、オープン スタンダードである [Mapbox Vector Tile 仕様](https://github.com/mapbox/vector-tile-spec)に準拠しています。

## <a name="connecting-a-data-source-to-a-layer"></a>レイヤーへのデータ ソースの接続

データは、レンダリング レイヤーを使用してマップ上にレンダリングされます。 1 つのデータ ソースは、1 つ以上のレンダリング レイヤーから参照できます。 次のレンダリング レイヤーでは、データ ソースが必要です。

- [バブル レイヤー](map-add-bubble-layer.md) - ポイント データをマップ上で拡大縮小された円としてレンダリングします。
- [シンボル レイヤー](map-add-pin.md) - ポイント データをアイコンやテキストとしてレンダリングします。
- [ヒート マップ レイヤー](map-add-heat-map-layer.md) - ポイント データを密度ヒート マップとしてレンダリングします。
- [線レイヤー](map-add-shape.md) - 線をレンダリングしたり、多角形のアウトラインをレンダリングしたりします。 
- [多角形レイヤー](map-add-shape.md) - 純色または画像パターンで多角形の領域を塗りつぶします。

次のコードは、データ ソースを作成し、マップに追加してバブル レイヤーに接続する方法を示しています。 次に、GeoJSON ポイント データをリモートの場所からデータ ソースにインポートします。 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

これらのデータ ソースに接続せず、レンダリングするためのデータを直接読み込む追加のレンダリング レイヤーがあります。 

- [画像レイヤー](map-add-image-layer.md) - マップの上に 1 つの画像をオーバーレイし、指定した座標のセットにその角をバインドします。
- [タイル レイヤー](map-add-tile-layer.md) - マップの上にラスター タイル レイヤーを重ねて表示します。

## <a name="one-data-source-with-multiple-layers"></a>複数のレイヤーがある 1 つのデータ ソース

複数のレイヤーを 1 つのデータ ソースに接続できます。 このオプションが役に立つさまざまなシナリオがあります。 たとえば、ユーザーが多角形を描画するシナリオを考えてみます。 ユーザーがポイントをマップに追加するときに、多角形領域をレンダリングして塗りつぶす必要があります。 多角形の輪郭を描画するスタイル指定された線を追加すると、ユーザーが描画するときに多角形のエッジが見やすくなります。 多角形内の個々の位置を簡単に編集するために、各位置の上にピンやマーカーなどのハンドルを追加することもできます。

![1 つのデータ ソースのデータをレンダリングする複数のレイヤーが表示されるマップ](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

ほとんどのマッピング プラットフォームで、多角形オブジェクト、線オブジェクト、および多角形内の各位置に対するピンが必要になります。 多角形を変更するときは、線とピンを手動で更新する必要がありますが、これはすぐに複雑になる可能性があります。

Azure Maps で必要なものは、次のコードで示すように、データ ソース内の 1 つの多角形だけです。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [ポップアップを追加する](map-add-popup.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)

> [!div class="nextstepaction"]
> [ヒート マップを追加する](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)