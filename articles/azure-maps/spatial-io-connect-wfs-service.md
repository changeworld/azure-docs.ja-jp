---
title: Web Feature Service (WFS) サービスに接続する | Microsoft Azure Maps
description: WFS サービスに接続し、Azure Maps Web SDK と空間 IO モジュールを使用して WFS サービスにクエリを実行する方法について説明します。
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 18ac583837c7cb8b2dabbfa6f7d7210c8afe3fcb
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402756"
---
# <a name="connect-to-a-wfs-service"></a>WFS サービスへの接続

Web Feature Service (WFS) は、Open Geospatial Consortium (OGC) によって定義されている、標準化された API を持つ空間データに対してクエリを実行するための Web サービスです。 空間 IO モジュールで `WfsClient` クラスを使用すると、開発者は WFS サービスに接続し、サービスからデータにクエリを実行できます。

`WfsClient` クラスでは、次のフィーチャーがサポートされています。

- サポートされているバージョン: `1.0.0`、`1.1.0`、`2.0.0`
- サポートされているフィルター演算子: バイナリ比較、論理、数値、値、`bbox`。
- 要求は `HTTP GET` のみを使用して作成されます。
- サポートされている操作:

    | | |
    | :-- | :-- |
    | GetCapabilities | 有効な WFS 操作とパラメーターを使用してメタデータ ドキュメントを生成します |
    | GetFeature | データ ソースからフィーチャーの選択を返します |
    | DescribeFeatureType | サポートされているフィーチャーの種類を返します |

## <a name="using-the-wfs-client"></a>WFS クライアントの使用

空間 IO モジュールで `atlas.io.ogc.WfsClient` クラスを使用すると、WFS サービスに対してクエリを実行し、応答を GeoJSON オブジェクトに変換することが簡単になります。 この GeoJSON オブジェクトはその後、他のマッピングのために使用できます。

次のコードは、WFS サービスに対してクエリを行い、返されたフィーチャーをマップにレンダリングします。

<br/>

<iframe height='700' scrolling='no' title='Simple WFS example (単純な WFS の例)' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>単純な WFS の例</a>」を参照してください。
</iframe>

## <a name="supported-filters"></a>サポートされているフィルター

WFS 標準の仕様では、OGC フィルターが使用されます。 次のフィルターは、呼び出されるサービスでこれらのフィルターもサポートされているという前提で、WFS クライアントによってサポートされています。 カスタム フィルター文字列は `CustomFilter` クラスに渡すことができます。

**論理演算子**

- `And`
- `Or`
- `Not`

**値演算子**

- `GmlObjectId`
- `ResourceId`

**数値演算子**

- `Add`
- `Sub`
- `Mul`
- `Div`

**比較演算子**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

次のコードは、WFS クライアントでのさまざまなフィルターの使用方法を示しています。

<br/>

<iframe height='500' scrolling='no' title= 'WFS filter examples (WFS フィルターの例)' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS フィルターの例</a>」を参照してください。
</iframe>

## <a name="wfs-service-explorer"></a>WFS サービス エクスプローラー

次のコードは、WFS クライアントを使用して WFS サービスを探索します。 サービス内のプロパティの型レイヤーを選択し、関連付けられている凡例を表示します。

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS サービス エクスプローラー' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS サービス エクスプローラー</a>」を参照してください。
</iframe>

また、プロキシ サービスを使用して、CORS が有効になっていないドメインでホストされているリソースを読み込むこともできます。 まず、プロキシ サービスの URL を保持する変数を定義し、WFS クライアントに `proxyService` オプションを設定します。 ユーザーに対してプロキシ サービス オプションをレンダリングするには、UI にユーザー入力を追加します。 入力がクリックされたときに、サービス URL を読み込みます。 次のスニペットは、プロキシ サービスの使用方法を示しています。

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

次の HTML コード スニペットは、上記の JavaScript コードに対応しています。

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [コア操作の活用](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [サポートされるデータ形式の詳細](spatial-io-supported-data-format-details.md)
