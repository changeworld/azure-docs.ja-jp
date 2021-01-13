---
title: Android マップで機能情報を表示する | Microsoft Azure Maps
description: ユーザーがマップ機能を操作するときに情報を表示する方法について説明します。 Azure Maps Android SDK を使用して、トースト メッセージや他の種類のメッセージを表示します。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679998"
---
# <a name="display-feature-information"></a>機能情報を表示する

空間データは、多くの場合、点、線、および多角形を使用して表されます。 このデータには、多くの場合、メタデータ情報が関連付けられています。 たとえば、点でレストランの場所を表すことができ、そのレストランに関するメタデータには、その名前、住所、および提供されている料理の種類が挙げられます。 このメタデータは、GeoJSON `Feature` のプロパティとして追加できます。 次のコードでは、"Hello World!" という値を持つ `title` プロパティを使用して、単純な点機能を作成します。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

マップにデータを作成して追加する方法については、「[データ ソースを作成する](create-data-source-android-sdk.md)」ドキュメントを参照してください。

ユーザーがマップ上の機能と対話するときに、イベントを使用してそれらのアクションに応答することができます。 一般的なシナリオでは、ユーザーが対話した機能のメタデータ プロパティから作成されたメッセージを表示します。 `OnFeatureClick` イベントは、ユーザーがマップ上の機能をタップしたことを検出するために使用される主要なイベントです。 `OnLongFeatureClick` イベントもあります。 マップに `OnFeatureClick` イベントを追加するときに、それを 1 つのレイヤーに制限する場合は、制限するレイヤーの ID を渡します。 レイヤー ID が渡されない場合は、マップ上の任意の機能をタップすると、それがどのレイヤーに含まれているかに関係なく、このイベントが発生します。 次のコードは、マップ上の点データをレンダリングするシンボル レイヤーを作成し、`OnFeatureClick` イベントを追加して、それをこのシンボル レイヤーに制限します。

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>トースト メッセージを表示する

トースト メッセージは、ユーザーに情報を表示する最も簡単な方法の 1 つであり、すべてのバージョンの Android で使用できます。 いかなる種類のユーザー入力もサポートされておらず、短時間のみ表示されます。 タップされた機能についてユーザーにすばやく知らせたい場合は、トースト メッセージを使用することをお勧めします。 次のコードは、`OnFeatureClick` イベントでトースト メッセージを使用する方法を示しています。

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![タップされた機能のアニメーションと表示されているトースト メッセージ](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

トースト メッセージに加えて、機能のメタデータ プロパティを表示するには、次のようなさまざまな方法があります。

- [Snakbar ウィジェット](https://developer.android.com/training/snackbar/showing.html) - `Snackbars` は、操作に関する軽いフィードバックを提供します。 モバイルでは画面の下部、大きいデバイスでは左下に短いメッセージが表示されます。 `Snackbars` は、画面上の他のすべての要素の上に表示され、一度に表示できるのは 1 つだけです。
- [ダイアログ](https://developer.android.com/guide/topics/ui/dialogs) - ダイアログは、決定したり追加情報を入力したりすることをユーザーに求める、小さなウィンドウです。 ダイアログは画面への入力は行わず、通常は、操作を続行する前にユーザーがアクションを実行する必要があるモーダル イベントに使用されます。
- 現在のアクティビティに[フラグメント](https://developer.android.com/guide/components/fragments)を追加します。
- 別のアクティビティまたはビューに移動します。

## <a name="next-steps"></a>次の手順

マップにさらにデータを追加するには:

> [!div class="nextstepaction"]
> [マップ イベントに対応する](android-map-events.md)

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
