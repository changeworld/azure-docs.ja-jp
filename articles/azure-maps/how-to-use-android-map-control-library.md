---
title: Android マップ コントロールの概要 | Microsoft Azure Maps
description: Azure Maps Android SDK について理解を深めます。 Android Studio でプロジェクトを作成する方法、SDK をインストールする方法、および対話型のマップを作成する方法を確認します。
author: stevemunk
ms.author: v-munksteve
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 47af15633803fe0dab299c1f2c4a80c10d32efb0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036202"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK の概要

Azure Maps Android SDK は、Android 用のベクター マップ ライブラリです。 この記事では、Azure Maps Android SDK のインストールおよびマップの読み込みのプロセスを説明します。

## <a name="prerequisites"></a>前提条件

必ず、[Android アプリ作成のクイックスタート](quick-android-map.md)に関する記事の手順を完了してください。

## <a name="localizing-the-map"></a>マップのローカライズ

Azure Maps Android SDK には、マップの言語と地域ビューを設定する方法が 3 つ用意されています。 次のコードは、言語をフランス語 ("fr-FR") に、地域ビューを "Auto" に設定する方法を示しています。

1. 静的な `setLanguage` および `setView` プロパティを使用して、言語と地域ビューの情報を `AzureMaps` クラスに渡します。 これで、アプリに既定の言語と地域ビューのプロパティが設定されます。
    
    ::: zone pivot="programming-language-java-android"
    
    ```java
    static {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
    ```
    
    ::: zone-end
    
    ::: zone pivot="programming-language-kotlin"
    
    ```kotlin
    companion object {
        init {
            //Alternatively use Azure Active Directory authenticate.
            AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
            //Set your Azure Maps Key.
            //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
        
            //Set the language to be used by Azure Maps.
            AzureMaps.setLanguage("fr-FR");
        
            //Set the regional view to be used by Azure Maps.
            AzureMaps.setView("Auto");
        }
    }
    ```
    
    ::: zone-end

1. 言語と地域ビューの情報はマップ コントロール XML に渡すこともできます。

    ```xml
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        />
    ```
1. 言語と地域ビューのプロパティをプログラミングで設定する最後の方法では、マップの `setStyle` メソッドを使用します。 これは、マップの言語と地域ビューを変更するときにいつでも実行できます。

    ::: zone pivot="programming-language-java-android"

    ```java
    mapControl.onReady(map -> {
        map.setStyle(
            language("fr-FR"),
            view("Auto")
        );
    });
    ```

    ::: zone-end

    ::: zone pivot="programming-language-kotlin"

    ```kotlin
    mapControl.onReady(OnReady { map: AzureMap ->
        map.setStyle(
            language("fr-FR"),
            view("Auto")
        )
    })
    ```

    ::: zone-end

言語が "fr-FR" に設定され、地域ビューが "Auto" に設定されている Azure Maps の例を次に示します。

![Azure Maps、ラベルをフランス語で表示するマップ イメージ](media/how-to-use-android-map-control-library/android-localization.png)

サポートされている言語と地域ビューの全一覧については、「[Azure Maps でのローカライズのサポート](supported-languages.md)」を参照してください。

## <a name="navigating-the-map"></a>マップ内を移動する

マップをズーム、パン、回転、およびピッチ調整する方法は複数存在します。 以下では、マップ内を移動するときのさまざまな方法をすべて詳しく説明します。

### <a name="zoom-the-map"></a>マップをズームする

* 2 本の指でマップをタッチし、指でつまむようにすると縮小し、指を広げると拡大します。
* マップをダブルタップし、1 レベル分拡大します。
* 2 本の指でダブル タップし、マップを 1 レベル分縮小します。
* 2 回タップします。2 回目のタップで指をマップに置いたままにして、上にドラッグすると拡大し、下にドラッグすると縮小します。

### <a name="pan-the-map"></a>マップをパンする

* マップをタッチし、任意の方向にドラッグします。

### <a name="rotate-the-map"></a>マップを回転する

* 2 本の指でマップをタッチし、回転します。

### <a name="pitch-the-map"></a>マップのピッチを調整する

* 2 本の指でマップをタッチして、上または下にドラッグします。

## <a name="azure-government-cloud-support"></a>Azure Government クラウドのサポート

Azure Maps Android SDK では、Azure Government クラウドがサポートされています。 Azure Maps Android SDK には、同じ Maven リポジトリからアクセスします。 Azure Maps プラットフォームの Azure Government cloud バージョンに接続するには、次のタスクを実行する必要があります。

Azure Maps 認証の詳細が指定されているのと同じ場所で、次のコード行を追加して、Azure Maps Government クラウド ドメインを使用するようにマップに指示します。

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

マップとサービスを認証するときは、Azure Government クラウド プラットフォームからの Azure Maps 認証の詳細を必ず使用してください。

## <a name="migrating-from-a-preview-version"></a>プレビュー バージョンから移行する

プレビューから一般提供への移行に伴い、Azure Maps Android SDK にいくつかの破壊的変更が導入されています。 主な内容は次のとおりです。

* Maven 識別子が `"com.microsoft.azure.maps:mapcontrol:0.7"` から `"com.azure.android:azure-maps-control:1.0.0"` に変更されました。 名前空間とメジャーバージョン番号が変更されました。
* インポート名前空間が `com.microsoft.azure.maps.mapcontrol` から `com.azure.android.maps.control` に変更されました。
* XML オプション、色リソース、画像リソースのリソース名のテキストが `mapcontrol_` から `azure_maps_` に置き換えられています。

    **前:**

    ```xml
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
    />
    ```

    **後:**

    ```xml
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        app:azure_maps_centerLat="47.602806"
        app:azure_maps_centerLng="-122.329330"
        app:azure_maps_zoom="12"
    />
    ```

## <a name="next-steps"></a>次のステップ

マップにオーバーレイ データを追加する方法を確認します。

> [!div class="nextstepaction"]
> [Azure Maps での認証の管理](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Android マップでのマップ スタイルの変更](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
