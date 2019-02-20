---
title: Azure Maps で Android マップ コントロールを使用する方法 | Microsoft Docs
description: Azure Maps で Android マップ コントロールを使用します。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e3f7579324e1218cc2e2c3594889db776da6e529
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119143"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Azure Maps Android SDK を使用する方法

Azure Maps Android SDK は、Android 用のベクター マップ ライブラリです。 この記事では、Azure Maps Android SDK のインストール、マップの読み込み、ピンの配置のプロセスについて説明します。

## <a name="prerequisites-to-get-started"></a>作業開始の前提条件

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する 

このガイドの手順を実行する前に、[アカウントとキーの管理](how-to-manage-account-keys.md)に関するページを参照して、S1 価格レベルのアカウント サブスクリプションを作成、管理する必要があります。

### <a name="download-android-studio"></a>Android Studio をダウンロードする

[Android Studio](https://developer.android.com/studio/) は、Google から無料でダウンロードできます。 Azure Maps Android SDK をインストールするには、まず Android Studio をダウンロードし、空のアクティビティを含むプロジェクトを作成する必要があります。

## <a name="create-a-project-in-android-studio"></a>Android Studio でプロジェクトを作成する

空のアクティビティを含む新しいプロジェクトを作成する必要があります。 以下の手順に従って新しい Android Studio プロジェクトを作成してください。

1. *[Choose your project]\(プロジェクトの選択\)* で、アプリケーションが実行されるフォーム ファクターとして [Phone and Tablet]\(電話とタブレット\) をオンにします。
2. フォーム ファクターの *[Empty Activity]\(空のアクティビティ\)* をクリックし、**[Next]\(次へ\)** をクリックします。
3. *[Configure your project]\(プロジェクトの構成\)* で、最小限の SDK として `API 21: Android 5.0.0 (Lollipop)` を選択します。 これは Azure Maps Android SDK でサポートされる最も低いバージョンです。
4. 既定値の `Activity Name` と `Layout Name` のままにして、**[Finish]\(完了\)** をクリックします。

Android Studio のインストールと新しいプロジェクトの作成の詳細については、[Android Studio のドキュメント](https://developer.android.com/studio/intro/)を参照してください。

![新しいプロジェクトを作成する](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>仮想デバイスを設定する

Android Studio を使用すると、コンピューター上で仮想 Android デバイスを設定できます。 これは開発中にアプリケーションをテストするために役立ちます。 仮想デバイスを設定するには、プロジェクト イメージの右上にある Android Virtual Device (AVD) Manager アイコンをクリックします。 次に **[Create Virtual Device]\(仮想デバイスの作成\)** ボタンをクリックします。 また、ツールバーの [Tools]\(ツール\) > [Android] > [AVD Manager] からマネージャーにアクセスすることもできます。 **[Phones]\(電話\)** カテゴリから **[Nexus 5X]** を選択し、**[Next]\(次へ\)** をクリックします。

AVD の設定の詳細については、[Android Studio のドキュメント](https://developer.android.com/studio/run/managing-avds)を参照してください。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Azure Maps Android SDK をインストールする

アプリケーションのビルドに進む前に、次の手順に従って Azure Maps Android SDK をインストールします。 

1. **build.gradle** ファイルのリポジトリ ブロックである **allprojects** に以下を追加します。

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **app/build.gradle** を更新し、以下を追加します。

    1. Android ブロックに以下を追加します。

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. 依存関係ブロックを更新し、以下を追加します。

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. 'AndroidManifest.xml' に以下を追加してアクセス許可を設定します。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. 次のような XML になるように **res > layout > activity_main.xml** を編集します。
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. **MainActivity.java** を編集して、マップ ビュー アクティビティ クラスを作成します。 編集後のクラスは次のようになります。

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }

    }

    ```

## <a name="import-classes"></a>クラスをインポートする

前述の手順を完了すると、コード内の一部のテキストに対して Android Studio から警告を受け取る可能性が高くなります。 これを処理するには、`MainActivity.java` で参照されているクラスのいくつかをインポートする必要があります。

`Alt`+`Enter` (Mac では `Option`+`Return`) を押すと、このようなクラスを自動的にインポートできます。 

**[Run 'App']\('アプリ' の実行\)** ボタン (Mac では `Control`+`R`) をクリックしてアプリケーションをビルドします。

![[実行] をクリックします。](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio でアプリケーションをビルドするには、数秒かかります。 ビルドが完了したら、エミュレートされた Android デバイスでアプリケーションをテストできます。 次のようなマップが表示されます。

![Android のマップ](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>マップにマーカーを追加する

マップにマーカーを追加するには、`MainActivity.java` に `mapView.getMapAsync()` 関数を追加します。 最終的な `MainActivity.java` は次のようになります。

```java
package com.example.myapplication;

import android.app.Activity;
import android.os.Bundle;
import com.mapbox.geojson.Feature;
import com.mapbox.geojson.Point;
import com.microsoft.azure.maps.mapcontrol.AzureMaps;
import com.microsoft.azure.maps.mapcontrol.MapControl;
import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
import com.microsoft.azure.maps.mapcontrol.source.DataSource;
import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
public class MainActivity extends AppCompatActivity {
    
    static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }
}
```

アプリケーションを再実行すると、マップ上に次のようなマーカーが表示されます。

![Android のマップ ピン](./media/how-to-use-android-map-control-library/android-map-pin.png)