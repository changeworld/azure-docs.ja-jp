---
title: チュートリアル:[新しい Android アプリを作成する]
description: このチュートリアルでは、Azure Spatial Anchors を使用して新しい Android アプリを作成する方法について説明します。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e88654f0dc118fa09d6c1d83287519f3cdbe7363
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122229068"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>チュートリアル:Azure Spatial Anchors を使用して新しい Android アプリを作成する手順

このチュートリアルでは、ARCore 機能を Azure Spatial Anchors と統合する新しい Android アプリの作成方法を示します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

- <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 以降</a>がインストールされている Windows または macOS マシン。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">開発者向け</a>の <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 対応</a> Android デバイス。

## <a name="getting-started"></a>作業の開始

Android Studio を起動します。 **[Android Studio へようこそ]** ウィンドウで、 **[新規 Android Studio プロジェクトの開始]** を選択します。 
1. **[ファイル]** -> **[新しいプロジェクト]** の順に選択します。
1. **[新規プロジェクトの作成]** ウィンドウの **[スマホおよびタブレット]** セクションで、 **[空のアクティビティ]** を選択し、 **[次へ]** をクリックします。
1. [New Project - Empty Activity]\(新しいアクティビティ - 空のアクティビティ\) ウィンドウで、次の値を変更します。
   - **[名前]** 、 **[パッケージ名]** 、 **[保存場所]** を、希望する値に変更します
   - **[言語]** を `Java` に設定します
   - **[最小 API レベル]** を `API 26: Android 8.0 (Oreo)` に設定します
   - 他のオプションはそのままにします
   - **[完了]** をクリックします。 
1. **コンポーネント インストーラー** が実行されます。 いくつかの処理の後、Android Studio によって IDE が開かれます。

![Android Studio - [新しいプロジェクト]](../../../includes/media/spatial-anchors-androidstudio/androidstudio-newproject.png)

<!-- maybe snapshot here -->

## <a name="trying-it-out"></a>試してみる

新しいアプリをテストするには、USB ケーブルを使用して開発用マシンに開発者向けのデバイスを接続します。 Android Studio の右上で、接続されているデバイスを選択して **[Run 'app']\(アプリの実行\)** アイコンをクリックします。 Android Studio によって、接続されているデバイスにアプリがインストールされて起動されます。 "Hello World!" が、 お使いのデバイスで実行されているアプリに表示されます。 **[実行]** -> **[Stop 'app']\('app' を停止\)** をクリックします。
![Android Studio - 実行](../../../includes/media/spatial-anchors-androidstudio/androidstudio-run.png)


## <a name="integrating-_arcore_"></a>_ARCore_ との統合

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> は、Augmented Reality エクスペリエンスを構築するための Google のプラットフォームであり、お使いのデバイスが移動するときにその位置を追跡し、現実世界の独自の認識を構築できるようにします。

ルート `<manifest>` ノード内に次のエントリを含むように `app\manifests\AndroidManifest.xml` を変更します。 このコード スニペットではいくつかのことを行います。

- アプリにデバイスのカメラへのアクセスを許可します。
- Google Play ストア内で、ARCore をサポートするデバイスに対してのみアプリが表示されるようにもします。
- アプリのインストール時に ARCore がまだインストールされていない場合は ARCore をダウンロードしてインストールするように Google Play ストアを構成します。

```xml
<manifest ...>

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-feature android:name="android.hardware.camera.ar" />

    <application>
        ...
        <meta-data android:name="com.google.ar.core" android:value="required" />
        ...
    </application>

</manifest>
```

次のエントリを含むように `Gradle Scripts\build.gradle (Module: app)` を変更します。 このコードにより、ご自分のアプリは確実に ARCore バージョン 1.25 を対象とするようになります。 この変更の後に、Gradle から同期を求める通知を受け取ることがあります。 **[Sync now]\(今すぐ同期\)** をクリックします。

```gradle
dependencies {
    ...
    implementation 'com.google.ar:core:1.25.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>_Sceneform_ の統合

[_Sceneform_](https://developers.google.com/sceneform/develop/) により、OpenGL を習得しなくても、Augmented Reality アプリ内でリアルな 3D シーンを簡単にレンダリングできます。

次のエントリを含むように `Gradle Scripts\build.gradle (Module: app)` を変更します。 このコードにより、アプリは `Sceneform` に必要な Java 8 の言語コンストラクトの使用を許可されます。 また、アプリが `Sceneform` バージョン 1.15 をターゲットとします。 この変更の後に、Gradle から同期を求める通知を受け取ることがあります。 **[Sync now]\(今すぐ同期\)** をクリックします。

```gradle
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.15.0'
    ...
}
```

`app\res\layout\activity_main.xml` を開き、既存の Hello World `<TextView ... />` 要素を次の ArFragment に置き換えます。 このコードにより、カメラ フィードが画面に表示され、お使いのデバイスが移動するときにその位置を ARCore で追跡できます。


```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

> [!NOTE]
> メイン アクティビティの Raw xml を表示するためには、Android Studio の右上にある [コード] または [分割] のボタンをクリックします。

お使いのデバイスにアプリを[再デプロイ](#trying-it-out)して、もう一度検証します。 今回は、カメラのアクセス許可を求められます。 承認されると、画面上にカメラ フィードのレンダリングが表示されます。

## <a name="place-an-object-in-the-real-world"></a>現実世界でのオブジェクトの配置

アプリを使用してオブジェクトを作成し、配置しましょう。 まず、以下の import を `app\java\<PackageName>\MainActivity` に追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=21-23,27-33,17-18)]

続いて、以下のメンバー変数を `MainActivity` クラスに追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=47-52)]

次に、以下のコードを `app\java\<PackageName>\MainActivity` `onCreate()` メソッドに追加します。 このコードでは、ユーザーがデバイスの画面をタップしたときにそれを検出する `handleTap()` というリスナーをフックします。 ARCore の追跡によって既に認識されている現実世界のサーフェス上でタップが行われると、リスナーが実行されます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=63-69,80&highlight=6-7)]

最後に、すべてをまとめる次の `handleTap()` メソッド追加します。 これにより球体が作成されて、タップされた位置に配置されます。 `this.recommendedSessionProgress` は現在 0 に設定されているので、最初は球体が黒くなります。 この値は後で調整されます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-180,183-192,209)]

お使いのデバイスにアプリを[再デプロイ](#trying-it-out)して、もう一度検証します。 今回は、ARCore が環境の認識を開始するようにお使いのデバイスを移動できます。 次に、画面をタップして黒い球体を作成し、任意のサーフェス上に配置します。

## <a name="attach-a-local-azure-spatial-anchor"></a>ローカル Azure Spatial Anchor のアタッチ

次のエントリを含むように `Gradle Scripts\build.gradle (Module: app)` を変更します。 このサンプル コード スニペットは、Azure Spatial Anchors SDK バージョン 2.10.2 を対象としています。 現在、SDK バージョン 2.7.0 がサポートされる最小バージョンであり、それより新しいバージョンの Azure Spatial Anchors を参照している場合も同様に機能するはずです。 最新バージョンの Azure Spatial Anchors SDK を使用することをお勧めします。 SDK のリリース ノートは[こちら](https://github.com/Azure/azure-spatial-anchors-samples/releases)で確認できます。

```gradle
dependencies {
    ...
    implementation 'com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.10.2]'
    implementation 'com.microsoft.azure.spatialanchors:spatialanchors_java:[2.10.2]'
    ...
}
```

Azure Spatial Anchors SDK 2.10.0 以降を対象としている場合は、プロジェクトの `settings.gradle` ファイルの repositories セクションに次のエントリを追加します。 これには、SDK 2.10.0 以降用の Azure Spatial Anchors Android パッケージがホストされている Maven パッケージ フィードの URL が含まれます。 

```gradle
dependencyResolutionManagement {
    ...
    repositories {
        ...
        maven {
            url 'https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Maven-packages/maven/v1'
        }
        ...
    }
}
```

`app\java\<PackageName>`-> **[新規]** -> **[Java Class]\(Java クラス\)** を右クリックします。 **[名前]** を _MyFirstApp_ に設定し、 **[クラス]** を選択します。 `MyFirstApp.java` というファイルが作成されます。 そこに次の import を追加します。

```java
import com.microsoft.CloudServices;
```

`android.app.Application` をスーパークラスとして定義します。
```java
public class MyFirstApp extends android.app.Application {...
```

次に、新しい `MyFirstApp` クラス内に次のコードを追加します。このコードにより、Azure Spatial Anchors がアプリケーションのコンテキストで初期化されます。

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

ここで、ルート `<application>` ノード内に次のエントリを含むように `app\manifests\AndroidManifest.xml` を変更します。 このコードでは、作成したアプリケーション クラスがアプリにフックされます。

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

`app\java\<PackageName>\MainActivity` に戻り、そこに以下の import を追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=17,16,18,24,26,35,36,38&highlight=2-8)]

続いて、以下のメンバー変数を `MainActivity` クラスに追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-56&highlight=3-5)]

次に、以下の `initializeSession()` メソッドを `mainActivity` クラス内に追加しましょう。 これが呼び出されると、Azure Spatial Anchors セッションが作成され、アプリの起動時に適切に初期化されます。 このコードにより、`cloudSession.setSession` 呼び出しを介して ASA セッションに渡された sceneView セッションは、早期リターンが設定されているため、null ではありません。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=92-107,155)]

sceneView セッションがまだ設定されていない場合 (つまり、`sceneView.getSession()` が null の場合) に `initializeSession()` が早期リターンを行えるため、onUpdate 呼び出しを追加して、sceneView セッションが作成されたら ASA セッションが初期化されるようにします。
[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=scene_OnUpdate)]

次に、`initializeSession()` および `scene_OnUpdate(...)` メソッドを `onCreate()` メソッドにフックしましょう。 また、カメラ フィードからのフレームが処理のために Azure Spatial Anchors SDK に送信されるようにします。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=63-80&highlight=9-17)]

最後に、以下のコードを `handleTap()` メソッドに追加します。 これにより、現実世界に配置している黒い球体にローカルの Azure 空間アンカーがアタッチされます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-192,209&highlight=12-13)]

アプリをもう一度[再デプロイ](#trying-it-out)します。 お使いのデバイスを移動し、画面をタップして黒い球体を配置します。 ただし今回は、コードによりローカルの Azure 空間アンカーが作成されて球体にアタッチされます。

先に進む前に、アカウント識別子、キー、ドメインがまだない場合は、Azure Spatial Anchors アカウントを作成してこれらを取得する必要があります。 次のセクションに従ってこれらを取得します。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>クラウドへのローカル アンカーのアップロード

自分の Azure Spatial Anchors アカウント識別子、キー、ドメインを作成したら、`app\java\<PackageName>\MainActivity` に戻り、そこに以下の import を追加できます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=38-43&highlight=3-6)]

続いて、以下のメンバー変数を `MainActivity` クラスに追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=56-61&highlight=3-6)]

次に、以下のコードを `initializeSession()` メソッドに追加します。 まず、このコードにより、Azure Spatial Anchors SDK によってカメラ フィードからフレームが収集される際の進行状況をアプリで監視できます。 その際、球体の色が当初の黒から灰色に変化し始めます。 さらに、アンカーをクラウドに送信するための十分なフレームが収集されると、白に変わります。 次に、このコードでは、クラウド バックエンドとの通信に必要な資格情報が提供されます。 ここでは、アカウント識別子、キー、ドメインを使用するようにアプリを構成します。 これらの情報は、[Spatial Anchors リソースを設定](#create-a-spatial-anchors-resource)するときにテキスト エディターにコピーしました。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=92-130,151-155&highlight=17-43)]

次に、以下の `uploadCloudAnchorAsync()` メソッドを `mainActivity` クラスに追加します。 呼び出されると、このメソッドは、お使いのデバイスから十分なフレームが収集されるまで非同期的に待機します。 この状況が発生するとすぐに、球体の色が黄色に切り替わってから、クラウドへのローカル Azure 空間アンカーのアップロードが開始されます。 アップロードが完了すると、コードによってアンカー識別子が返されます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

最後に、すべてをつなげましょう。 `handleTap()` メソッドに次のコードを追加します。 これにより、球体が作成されるとすぐに `uploadCloudAnchorAsync()` メソッドが呼び出されます。 メソッドから戻ると、次のコードによって球体への最終的な更新が実行され、色が青色に変わります。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-209&highlight=26-39)]

アプリをもう一度[再デプロイ](#trying-it-out)します。 お使いのデバイスを移動し、画面をタップして球体を配置します。 ただし今回は、カメラ フレームの収集時に球体の色が黒から白に変化します。 十分なフレーム数を取得すると、球体が黄色に変わり、クラウドのアップロードが開始されます。 電話がインターネットに接続されていることを確認します。 アップロードの完了後に、球体は青色になります。 必要に応じて、Android Studio で `Logcat` ウィンドウを監視して、アプリによって送信されているログ メッセージを見ることもできます。 ログに記録されるメッセージの例として、フレーム キャプチャ中のセッションの進行状況や、アップロード完了後にクラウドが返すアンカー識別子などがあります。

> [!NOTE]
> `recommendedSessionProgress` の値 (デバッグ ログで `Session progress` と呼ばれている) が表示されない場合は、自分が配置した球体の周りで電話を **動かして回転** していることを確認します。


## <a name="locate-your-cloud-spatial-anchor"></a>クラウド空間アンカーの配置

アンカーがクラウドにアップロードされると、それを再度配置する準備ができています。 まず、以下の import をコードに追加しましょう。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=43,44,34,37&highlight=3-4)]

次に、以下のコードを `handleTap()` メソッドに追加しましょう。 このコードでは、次のことが行われます。

- 既存の青い球体を画面から削除します。
- Azure Spatial Anchors セッションをもう一度初期化します。 このアクションにより、配置しようとしているアンカーは、作成したローカル アンカーではなくクラウドから取得されます。
- クラウドにアップロードしたアンカーに対するクエリを発行します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

ここで、クエリの実行対象のアンカーが配置されたときに呼び出されるコードをフックしましょう。 `initializeSession()` メソッド内に次のコードを追加します。 このスニペットでは、クラウド空間アンカーが配置された後に緑色の球体が作成され、配置されます。 これにより、画面をもう一度タップできるようになるので、シナリオ全体をもう一度繰り返すことができます。別のローカル アンカーを作成し、アップロードして、もう一度配置します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=40-59)]

これで完了です。 最後にアプリを[再デプロイ](#trying-it-out)し、シナリオ全体をエンド ツー エンドで試します。 お使いのデバイスを移動し、黒い球体を配置します。 次に、球体が黄色に変わるまでデバイスを移動し続けてカメラ フレームをキャプチャします。 ローカル アンカーがアップロードされ、球体が青色に変わります。 最後に、ローカル アンカーが削除されるように画面をもう一度タップしてから、対応するクラウド アンカーに対してクエリを実行します。 クラウド空間アンカーが配置されるまで、デバイスの移動を続けます。 正しい場所に緑色の球体が表示され、シナリオ全体をもう一度繰り返すことができます。

## <a name="putting-everything-together"></a>すべてをまとめる

ここでは、さまざまな要素がすべてまとめられた後に `MainActivity` クラス ファイルがどのように見えるかを示します。 これを参照用に使用してご自身のファイルと比較し、違いが残っているかどうかを特定できます。

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ARCore 機能を Azure Spatial Anchors と統合する新しい Android アプリの作成方法を説明しました。 Azure Spatial Anchors ライブラリの詳細については、アンカーの作成と配置方法に関するガイドを引き続き参照してください。

> [!div class="nextstepaction"]
> [Azure Spatial Anchors を使用してアンカーを作成して配置する](../../../articles/spatial-anchors/create-locate-anchors-overview.md)