---
title: Azure Maps Android SDK を使用したマップ スタイルの設定
description: マップのスタイルを設定する 2 つの方法について説明します。 スタイルを調整するため、レイアウト ファイルまたはアクティビティ クラスで Microsoft Azure Maps Android SDK を使用する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532485"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Azure Maps Android SDK を使用したマップ スタイルの設定

この記事では、Azure Maps Android SDK を使用してマップ スタイルを設定する方法を示します。 Azure Maps は、6 つの異なるマップ スタイルから選択できます。 サポートされているマップ スタイルの詳細については、「[Azure Maps でのサポートされているマップ スタイル](./supported-map-styles.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。
3. [Azure Maps Android SDK](./how-to-use-android-map-control-library.md) をダウンロードしてインストールします。


## <a name="set-map-style-in-the-layout"></a>レイアウトでのマップ スタイルの設定

マップ スタイルは、アクティビティ クラスのレイアウト ファイルで設定できます。 `res > layout > activity_main.xml` を編集すると、次のようになります。

```XML
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`上記の属性はマップ スタイルを **grayscale_dark** に設定します。

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps でスタイルを grayscale_dark として表示しているマップ イメージ":::

## <a name="set-map-style-in-the-mainactivity-class"></a>MainActivity クラスでのマップ スタイルの設定

マップ スタイルは、MainActivity クラスで設定することもできます。 `java > com.example.myapplication > MainActivity.java` ファイルを開き、次のコード スニペットを **onCreate()** メソッドにコピーします。 このコードによりマップ スタイルは **satellite_road_labels** に設定されます。

>[!WARNING]
>Android Studio に必要なクラスがインポートされていない可能性があります。  その場合、コードにいくつかの未解決の参照が存在することになります。 必要なクラスをインポートするには、未解決の各参照にマウス ポインターを置き、`Alt + Enter` (Mac では Option + Return) を押します。

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps でスタイルを satellite_road_labels として表示しているマップ イメージ":::