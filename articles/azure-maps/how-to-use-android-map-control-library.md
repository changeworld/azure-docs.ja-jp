---
title: Azure Maps Android SDK の概要
description: Microsoft Azure Maps Android SDK について理解を深めます。 Android Studio でプロジェクトを作成する方法、SDK をインストールする方法、および対話型のマップを作成する方法を確認します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531263"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK の概要

Azure Maps Android SDK は、Android 用のベクター マップ ライブラリです。 この記事では、Azure Maps Android SDK のインストールおよびマップの読み込みのプロセスを説明します。

## <a name="prerequisites"></a>前提条件

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。
Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」を参照してください。
3. [Google の Android Studio をダウンロードしてインストールします](https://developer.android.com/studio/)。

## <a name="create-a-project-in-android-studio"></a>Android Studio でプロジェクトを作成する

Android Studio プロジェクトを作成するには、次の手順を完了します。

1. Android Studio を起動します。
2. **[+ 新しいプロジェクトの作成]** をクリックします。
3. **[Phone and Tablet]\(電話とタブレット\)** タブで、 **[空のアクティビティ]** をクリックします。 **[次へ]** をクリックします。
4. **[Configure your project]\(プロジェクトの構成\)** で、最小限の SDK として `API 21: Android 5.0.0 (Lollipop)` を選択します。
5. 言語として `Java` を選択します。
6. プロジェクトの既定の `Name` をそのまま使用します。 **[完了]** をクリックします。

Android Studio のインストールと新しいプロジェクトの作成の詳細については、[Android Studio のドキュメント](https://developer.android.com/studio/intro/)を参照してください。

![Android Studio でプロジェクトを作成する ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>デバイスをセットアップする

開発中にアプリケーションをテストするには、Android フォンまたは Android エミュレーターを使用することができます。

AVD (Android 仮想デバイス) の設定の詳細については、[Android Studio のドキュメント](https://developer.android.com/studio/run/managing-avds)を参照してください。

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK をインストールする

アプリケーションのビルドの次の手順は、Azure Maps Android SDK をインストールすることです。

SDK をインストールするには、以下の手順を完了します。

1. [プロジェクト] タブで、 **[Gradle スクリプト]** を展開します。 **build.gradle (プロジェクト: My_Application)** を開き、次のコードを **すべてのプロジェクト** (`repositories` セクション) に追加します。

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **build.gradle (モジュール: My_Application)** を開きます。

3. `defaultConfig` セクションの **minSdkVersion** が API 21 以降であることを確認します。

4. Android セクションに、次のコードを追加します。

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. 次のコードを `dependencies` セクションに追加します。

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. メイン ツール バーの **[ファイル]** をクリックし、 **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** をクリックします。

7. `res > layout > activity_main.xml` を開きます。 右上隅の `Code` ビューをクリックします。 `<androidx.constraintlayout.widget.ConstraintLayout>` 要素内に次の XML を追加します。
    
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
            />
    </FrameLayout>
    ```

8. `java > com.example.myapplication > MainActivity.java` ファイルで、次のことを行う必要があります。

    * Azure Maps SDK の imports を追加する。
    * Azure Maps の認証情報を設定する。
    * **onCreate** メソッドでマップ コントロールのインスタンスを取得する。

    すべてのアプリケーション ビューに認証情報を追加しなくても済むように、`AzureMaps.setSubscriptionKey` を呼び出すことで認証情報をグローバルに設定します。 Azure Active Directory を使用して認証するには、`AzureMaps.setAadProperties` を呼び出すこともできます。

    マップ コントロールは、MainActivity クラスの次のライフサイクル メソッドをオーバーライドします。 以下のメソッドで、Android の OpenGL ライフサイクルの管理を行います。

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    `MainActivity.java` ファイルを次のように編集します。

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {

        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.

            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

>[!WARNING]
>Android Studio に必要なクラスがインポートされていない可能性があります。  その場合、コードにいくつかの未解決の参照が存在することになります。 必要なクラスをインポートするには、未解決の各参照にマウス ポインターを置き、`Alt + Enter` (Mac では Option + Return) を押します。

Android Studio でアプリケーションをビルドするには、数秒かかります。 ビルドが完了した後、エミュレートされた Android デバイスでアプリケーションをテストできます。 次のようなマップが表示されます。

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Android アプリケーションでの Azure Maps":::

## <a name="localizing-the-map"></a>マップのローカライズ

Azure Maps Android SDK には、マップの言語と地域設定を設定するための 3 つの異なる方法が用意されています。

1. AzureMaps クラスで静的メソッドを呼び出して、言語と地域の設定を行います。

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. マップ コントロール XML で言語と地域の設定を定義します。

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. マップ コントロールでメソッドを呼び出して、言語と地域の設定を行います。 このオプションを使用すると、実行時に設定を変更できます。

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

以下に、言語が `fr-FR` に設定されている Azure Maps の例を示します。

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps、ラベルをフランス語で表示するマップ イメージ":::

サポートされている言語と地域ビューの完全な一覧については、[こちら](supported-languages.md)をご覧ください。

## <a name="navigating-the-map"></a>マップ内を移動する

マップをズーム、パン、回転、およびピッチ調整する方法は複数存在します。 以下では、マップ内を移動するときのさまざまな方法をすべて詳しく説明します。

**マップをズームする**

- 2 本の指でマップをタッチし、指でつまむようにすると縮小し、指を広げると拡大します。
- マップをダブルタップし、1 レベル分拡大します。
- 2 本の指でダブル タップし、マップを 1 レベル分縮小します。
- 2 回タップします。2 回目のタップで指をマップに置いたままにして、上にドラッグすると拡大し、下にドラッグすると縮小します。

**マップをパンする**

- マップをタッチし、任意の方向にドラッグします。

**マップを回転する**

- 2 本の指でマップをタッチし、回転します。

**マップのピッチを調整する**

- 2 本の指でマップをタッチして、上または下にドラッグします。

## <a name="next-steps"></a>次のステップ

マップにオーバーレイ データを追加する方法を確認します。

> [!div class="nextstepaction"]
> [Android マップへのシンボル レイヤーの追加](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Android マップへの図形の追加](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Android マップでのマップ スタイルの変更](./set-android-map-styles.md)