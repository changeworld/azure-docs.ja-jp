---
title: チュートリアル:GeoJSON データを Azure Maps Android SDK に読み込む | Microsoft Azure Maps
description: GeoJSON データ ファイルを Azure Maps Android マップ SDK に読み込む方法についてのチュートリアル。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: b36c82a12b984aa956cc7d9d41cbc13ffb3d083f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425214"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>チュートリアル:GeoJSON データを Azure Maps Android SDK に読み込む

このチュートリアルでは、場所データの GeoJSON ファイルを Azure Maps Android SDK にインポートする手順を説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Maps を Android アプリケーションに追加する。
> * データ ソースを作成してローカル ファイルまたは Web から GeoJSON ファイルを読み込む。
> * マップにデータを表示する。
> * マップ上のデータを操作して、その詳細を表示する。

## <a name="prerequisites"></a>[前提条件]

1. 「[Quickstart:Android アプリの作成](quick-android-map.md)。 このクイックスタートで使用したコードをこのチュートリアルで拡張します。
2. [目的地サンプル](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json)の GeoJSON ファイルをダウンロードします。

### <a name="import-geojson-data-from-web-or-assets-folder"></a>GeoJSON データを Web または assets フォルダーからインポートする

ほとんどの GeoJSON ファイルには、`FeatureCollection` 内のすべてのデータがラップされています。 このシナリオを念頭に置くと、GeoJSON ファイルを文字列としてアプリケーションに読み込む場合、それらを地物コレクションの静的 `fromJson` メソッドに渡すことで、GeoJSON の `FeatureCollection` オブジェクトに文字列を逆シリアル化してマップに追加することができます。

次の手順は、アプリケーションに GeoJSON ファイルをインポートし、それを GeoJSON の `FeatureCollection` オブジェクトとして逆シリアル化する方法を示しています。

1. 「[Quickstart:Android アプリの作成](quick-android-map.md)。以降の手順は、そのアプリケーションを前提として記述されています。
2. Android Studio のプロジェクト パネルで **app** フォルダーを右クリックし、`New > Folder > Assets Folder` に移動します。
3. [目的地サンプル](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json)の GeoJSON ファイルを assets フォルダーにドラッグ アンド ドロップします。

::: zone pivot="programming-language-java-android"

4. **MainActivity.java** ファイルに移動し、`onCreate` メソッド内で、`mapControl.onReady` イベントのコールバック内に以下のコードを追加します。 このコードでは、`importDataFromUrl` メソッドを使用して **SamplePoiDataSet.json** ファイルを assets フォルダーからデータ ソースに読み込んでから、それをマップに追加します。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("asset://SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. **MainActivity.kt** ファイルに移動し、`onCreate` メソッド内で、`mapControl.onReady` イベントのコールバック内に以下のコードを追加します。 このコードでは、`importDataFromUrl` メソッドを使用して **SamplePoiDataSet.json** ファイルを assets フォルダーからデータ ソースに読み込んでから、それをマップに追加します。

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("asset://SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

5. GeoJSON データをデータ ソースとして読み込むコードを使用して、そのデータをどのようにマップ上に表示するかを指定する必要があります。 ポイント データには、いくつかの異なるレンダリング レイヤーが存在します。最もよく使用されるレイヤーは、[バブル レイヤー](map-add-bubble-layer-android.md)、[シンボル レイヤー](how-to-add-symbol-to-android-map.md)、[ヒート マップ レイヤー](map-add-heat-map-layer-android.md)です。 `mapControl.onReady` イベントのコールバックで、データをインポートするためのコードの後に、データをバブル レイヤーにレンダリングする次のコードを追加します。

::: zone pivot="programming-language-java-android"

```java
//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a layer and add it to the map.
val layer = new BubbleLayer(source)
map.layers.add(layer)
```

::: zone-end

6. Android Studio のプロジェクト パネルで、パス `app > res > layout` の下にある **layout** フォルダーを右クリックし、`New > File` に移動します。 **popup_text.xml** という名前の新しいファイルを作成します。
7. ファイル **popup_text.xml** を開きます。 ファイルがデザイナー ビューで開いたら、画面を右クリックし、[XML に移動] を選択します。 次の XML をコピーしてこのファイルに貼り付けます。 この XML では、ポップアップと一緒に使用できる単純なレイアウトを作成し、テキスト ビューを含めます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

::: zone pivot="programming-language-java-android"

8. ファイル **MainActivity.java** に戻り、バブル レイヤーのコードの後に、再利用可能なポップアップを作成するための次のコードを追加します。

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

//Close it initially.
popup.close();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

8. ファイル **MainActivity.kt** に戻り、バブル レイヤーのコードの後に、再利用可能なポップアップを作成するための次のコードを追加します。

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)
    
//Close it initially.
popup.close()
```

::: zone-end

9. クリック イベントをバブル レイヤーにアタッチするために、次のコードを追加します。 バブル レイヤー内のバブルをタップすると、イベントが発生し、それによって、選択した機能のプロパティから詳細が取得され、**popup_text.xml** レイアウト ファイルを使用してビューが作成され、それがコンテンツとしてポップアップに渡され、そしてフィーチャーの位置にポップアップが表示されます。

::: zone pivot="programming-language-java-android"

```java
//Add a click event to the layer.
map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Display the name and entity type information of the feature into the text view of the popup layout.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText("%s\n%s",
        f.getStringProperty("Name"),
        f.getStringProperty("EntityType")
    );

    //Get the position of the clicked feature.
    Position pos = MapMath.getPosition((Point)f.geometry());

    //Set the options on the popup.
    popup.setOptions(
            //Set the popups position.
            position(pos),

            //Set the anchor point of the popup content.
            anchor(AnchorType.BOTTOM),

            //Set the content of the popup.
            content(customView)
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue to bubble up.
    return false;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a click event to the layer.
map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Display the name and entity type information of the feature into the text view of the popup layout.
    val tv = customView.findViewById<TextView>(R.id.message)
    tv.text = String.format(
        "%s\n%s",
        f.getStringProperty("Name"),
        f.getStringProperty("EntityType")
    )

    //Get the position of the clicked feature.
    val pos = MapMath.getPosition(f.geometry() as Point?)

    //Set the options on the popup.
    popup.setOptions( //Set the popups position.
        position(pos),  //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  //Set the content of the popup.
        content(customView)
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue to bubble up.
    false
} as OnFeatureClick, layer)
```

::: zone-end

10. アプリケーションを実行します。 マップが表示され、GeoJSON ファイル内のそれぞれの場所にバブルが重ねて表示されます。 任意のバブルをタップすると、タッチしたフィーチャーの名前とエンティティ型を含むポップアップが表示されます。

    ![GeoJSON ファイルからのデータが表示されたマップ。場所をタップするとポップアップが開く](media/tutorial-load-geojson-file-android/android-import-geojson.gif)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順に従って、このチュートリアルのリソースをクリーンアップします。

1. Android Studio を閉じて、作成したアプリケーションを削除します。
2. 外部のデバイスでアプリケーションをテストした場合は、そのデバイスからアプリケーションをアンインストールします。

## <a name="next-steps"></a>次の手順

他のコード例や対話型のコーディング エクスペリエンスについては、以下を参照してください。

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [フィーチャーの情報を表示する](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
