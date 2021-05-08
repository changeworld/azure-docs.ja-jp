---
title: マップにタイル レイヤーを追加する | Microsoft Azure Maps
description: マップに画像をスーパーインポーズする方法について説明します。 Azure Maps Web SDK を使用して気象レーダーのオーバーレイが含まれるタイル レイヤーをマップに追加する例を参照してください。
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: e0fda77be23f6ea16d5e64b5d4796813c53f0e94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608104"
---
# <a name="add-a-tile-layer-to-a-map"></a>マップにタイル レイヤーを追加する

この記事では、タイル レイヤーをマップに重ねて配置する方法について説明します。 タイル レイヤーを使用すると、Azure Maps ベース マップ タイルの上に画像を重ねることができます。 Azure Maps のタイル システムの詳細については、「[ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)」を参照してください。

タイル レイヤーでは、サーバーからタイルが読み込まれます。 これらの画像は、事前にレンダリングすることも、動的にレンダリングすることもできます。 事前にレンダリングされた画像は、タイル レイヤーが認識する名前付け規則を使用して、サーバー上の他の画像と同様に保存されます。 動的にレンダリングされた画像は、ほぼリアルタイムで画像を読み込むためのサービスを使用します。 Azure Maps の [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) クラスでは、3 種類のタイル サービスの名前付け規則がサポートされています。

* X、Y、ズーム表記 - X はタイル グリッド内のタイルの列の位置、Y はそのタイルの行の位置、ズーム表記はズーム レベルに基づく値です。
* Quadkey 表記 - x、y、ズーム情報を組み合わせて、1 つの文字列値にします。 この文字列値は、1 つのタイルの一意識別子になります。
* 境界ボックス -  境界ボックス座標形式 `{west},{south},{east},{north}` を使用して画像を指定します。 この形式は、[Web マッピング サービス (WMS)](https://www.opengeospatial.org/standards/wms) で一般的に使用されています。

> [!TIP]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) は、マップ上で大規模なデータ セットを視覚化する場合に適しています。 画像からタイル レイヤーを生成できるだけでなく、ベクター データもタイル レイヤーとしてレンダリングできます。 ベクター データをタイル レイヤーとしてレンダリングすることで、マップ コントロールでは、タイルが表すベクター データよりもファイル サイズの小さいタイルを読み込むだけで済みます。 この手法は、何百万行ものデータをマップにレンダリングする場合によく使われています。

タイル レイヤーに渡されるタイル URL は、TileJSON リソースへの http または https URL、または次のパラメーターを使用するタイル URL テンプレートにする必要があります。

* `{x}` - タイルの X 位置。 `{y}` と `{z}` も必要です。
* `{y}` - タイルの Y 位置。 `{x}` と `{z}` も必要です。
* `{z}` - タイルのズーム レベル。 `{x}` と `{y}` も必要です。
* `{quadkey}` - Bing Maps タイル システムの名前付け規則に基づくタイルの quadkey 識別子。
* `{bbox-epsg-3857}` - EPSG 3857 空間参照系の `{west},{south},{east},{north}` 形式の境界ボックス文字列。
* `{subdomain}` - サブドメインの値のプレースホルダー。この値が指定されると、`subdomain` が追加されます。
* `{azMapsDomain}` - タイル要求のドメインと認証を、マップで使用されているものと同じ値に合わせるためのプレースホルダー。

## <a name="add-a-tile-layer"></a>タイル レイヤーを追加する

 このサンプルは、タイルのセットを指すタイル レイヤーを作成する方法を示しています。 このサンプルでは、x、y、ズーム タイル システムを使用します。 このタイル レイヤーのソースは、クラウド ソーシングされた海図が含まれている [OpenSeaMap プロジェクト](https://openseamap.org/index.php)です。 レーダー データを表示する際、マップ内を移動したときに、市区町村のラベルがはっきりと確認できることが理想的です。 この動作は、`labels` レイヤーの下にタイル レイヤーを挿入することによって実装できます。

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='X、Y、Z を使用するタイル レイヤー' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tile Layer using X, Y, and Z</a>」Pen を表示します。
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC Web マッピング サービス (WMS) を追加する

Web マッピング サービス (WMTS) は、Open Geospatial Consortium (OGC) 標準で、マップ データのイメージを処理します。 Azure Maps で使用できるこの形式のオープン データセットは数多くあります。 この種類のサービスが `EPSG:3857` 座標参照系 (CRS) をサポートしている場合は、そのサービスをタイル レイヤーで使用できます。 WMS サービスを使用する場合は、幅と高さのパラメーターを、そのサービスでサポートされているものと同じ値に設定し、必ず `tileSize` オプションにも同じ値を設定してください。 書式設定された URL で、 `{bbox-epsg-3857}` プレースホルダーを使用してサービスの `BBOX` パラメーターを設定します。

次のスクリーンショットは、上記のコードで、[アメリカ地質調査所 (USGS)](https://mrdata.usgs.gov/) の地質データの Web マッピング サービスを、地図上でラベルの下にオーバーレイしています。

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="WMS タイル レイヤー" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/BapjZqr'>WMS タイル レイヤー</a>」Pen を表示します。
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC Web マッピング タイル サービス (WMTS) を追加する

Web マッピング タイル サービス (WMTS) は、Open Geospatial Consortium (OGC) 標準で、マップのタイルベースのオーバーレイを処理します。 Azure Maps で使用できるこの形式のオープン データセットは数多くあります。 この種類のサービスが `EPSG:3857` または `GoogleMapsCompatible` 座標参照系 (CRS) をサポートしている場合は、そのサービスをタイル レイヤーで使用できます。 WMTS サービスを使用する場合は、幅と高さのパラメーターを、そのサービスでサポートされているものと同じ値に設定し、必ず `tileSize` オプションにも同じ値を設定してください。 書式設定された URL で、以下のプレースホルダーを適宜置換します。

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

次のスクリーンショットは、上記のコードで、[アメリカ地質調査所 (USGS) 全国地図](https://viewer.nationalmap.gov/services/)の画像の Web マッピング タイル サービスを、地図上で道路とラベルの下にオーバーレイしています。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="WMTS タイル レイヤー" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/BapjZVY'>WMTS タイル レイヤー</a>」Pen を表示します。
</iframe>

## <a name="customize-a-tile-layer"></a>タイル レイヤーをカスタマイズする

タイル レイヤー クラスには多くのスタイル オプションがあります。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='タイル レイヤーのオプション' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tile Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [イメージ レイヤーを追加する](./map-add-image-layer.md)
