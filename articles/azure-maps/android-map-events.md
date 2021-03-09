---
title: Android マップでマップ イベントを処理する | Microsoft Azure Maps
description: ユーザーがマップとやりとりするときに発生するイベントについて学習します。 サポートされているすべてのマップ イベントの一覧を表示します。 Azure Maps Android SDK を使用してイベントを処理する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681327"
---
# <a name="interact-with-the-map-android-sdk"></a>マップを操作する (Android SDK)

この記事では、マップ イベント マネージャーを使用する方法について説明します。

## <a name="interact-with-the-map"></a>マップの操作

マップでは、`events` プロパティを使用してすべてのイベントが管理されます。 次の表に、サポートされているすべてのマップ イベントを一覧表示します。

| Event                  | イベント ハンドラーの形式 | [説明] |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>マップが "アイドル" 状態になる前にレンダリングされた最後のフレームの後に発生します。<ul><li>カメラの切り替えが進行中ではありません。</li><li>現在要求されているすべてのタイルが読み込まれました。</li><li>すべてのフェード/切り替えアニメーションが完了しました。</li></ul></p> |
| `OnCameraMove`         | `()`                 | ユーザーの操作またはメソッドの結果として、ビュー間でのアニメーション化された遷移中に繰り返し発生します。 |
| `OnCameraMoveCanceled` | `()`                 | カメラに対する移動要求が取り消されたときに発生します。 |
| `OnCameraMoveStarted`  | `(int reason)`       | ユーザーの操作またはメソッドの結果として、マップでビュー間の遷移が開始される直前に発生します。 イベント リスナーの `reason` 引数は、カメラの移動がどのように開始されたかに関する詳細を示す整数値を返します。 考えられる理由の一覧を次に示します。<ul><li>1: ジェスチャ</li><li>2: 開発者のアニメーション</li><li>3: API アニメーション</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | マップが、マップ上の同じ位置で押されて解放されたときに発生します。 |
| `OnFeatureClick`       | `(List<Feature>)`    | マップが、フィーチャー上の同じ位置で押されて解放されたときに発生します。  |
| `OnLongClick`          | `(double lat, double lon)` | マップが、マップ上の同じ位置で押され、一瞬そのまま保持されてから、解放されたときに発生します。 |
| `OnLongFeatureClick `  | `(List<Feature>)`    | マップが、フィーチャー上の同じ位置で押され、一瞬そのまま保持されてから、解放されたときに発生します。 |
| `OnReady`              | `(AzureMap map)`     | マップが最初に読み込まれたとき、またはアプリの向きが変更されて、必要最小限のマップ リソースが読み込まれ、プログラムを使用してマップを操作できる状態になったときに発生します。 |

次のコードは、`OnClick`、`OnFeatureClick`、および `OnCameraMove` の各イベントをマップに追加する方法を示しています。

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

詳細については、「[マップ内を移動する](how-to-use-android-map-control-library.md#navigating-the-map)」ドキュメントを参照して、マップおよびトリガー イベントの操作方法を確認してください。

## <a name="scope-feature-events-to-layer"></a>フィーチャー イベントのスコープをレイヤーに設定する

`OnFeatureClick` または `OnLongFeatureClick` の各イベントをマップに追加する場合、レイヤー ID を 2 番目のパラメーターとして渡すことができます。 レイヤー ID が渡されると、イベントは、そのレイヤーでイベントが発生した場合にのみ発生します。 スコープがレイヤーに設定されたイベントは、シンボル、バブル、線、および多角形の各レイヤーでサポートされています。

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

## <a name="next-steps"></a>次のステップ

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [マップ内を移動する](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
