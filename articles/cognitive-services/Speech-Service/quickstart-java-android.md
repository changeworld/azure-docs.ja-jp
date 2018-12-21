---
title: 'クイック スタート: 音声を認識する (Java (Android)) - 音声サービス'
titleSuffix: Azure Cognitive Services
description: Android で Java と Speech Service SDK を使用して音声を認識する方法について説明します。
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: afe0cfe61779e95fc9a65a1f4928ddae4b7af267
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090110"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>クイック スタート: Android で Speech SDK を使用して Java で音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services Speech SDK を使用して音声をテキストに変換する Android 向け Java アプリケーションを作成する方法について説明します。
このアプリケーションは、Microsoft Cognitive Services Speech SDK Maven パッケージ (バージョン 1.1.0) と Android Studio 3.1 が基になっています。
現在 Speech SDK は、32/64 ビットの ARM プロセッサを搭載した Android デバイスおよび Intel x86/x64 互換のプロセッサを搭載した Android デバイスと互換性があります。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](speech-devices-sdk.md) を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech サービス サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、[Sppech サービスを無料で試す](get-started.md)ための記事を参照してください。

## <a name="create-and-configure-a-project"></a>プロジェクトの作成と構成

1. Android Studio を起動し、ウェルカム ウィンドウの **[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。

    ![Android Studio のウェルカム ウィンドウのスクリーンショット](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. **[新しいプロジェクトの作成]** ウィザードが表示されます。 **[Create Android Project]\(Android プロジェクトの作成\)** 画面で、**アプリケーション名**として「**Quickstart**」、**会社のドメイン**として「**samples.speech.cognitiveservices.microsoft.com**」と入力し、プロジェクトのディレクトリを選択します。 C++ および Kotlin のチェック ボックスをオフのままにし、**[次へ]** を選択します。

   ![[新しいプロジェクトの作成] ウィザードのスクリーンショット](media/sdk/qs-java-android-02-create-android-project.png)

1. **[ターゲットの Android デバイス]** 画面で、**[Phone and Tablet]\(電話およびタブレット\)** のみ選択します。 その下のドロップダウン リストから **[API 23: Android 6.0 (Marshmallow)]** を選択し、**[次へ]** を選択します。

   ![[新しいプロジェクトの作成] ウィザードのスクリーンショット](media/sdk/qs-java-android-03-target-android-devices.png)

1. **[Add an Activity to Mobile]\(モバイルにアクティビティを追加\)** 画面で、**[Empty Activity]\(空のアクティビティ\)** を選択して **[次へ]** をクリックします。

   ![[新しいプロジェクトの作成] ウィザードのスクリーンショット](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. **[Configure Activity]\(アクティビティの構成\)** 画面で、アクティビティ名として **MainActivity** を、レイアウト名として **activity\_main** を使用します。 両方のチェック ボックスを選択し、**[完了]** を選択します。

   ![[新しいプロジェクトの作成] ウィザードのスクリーンショット](media/sdk/qs-java-android-05-configure-activity.png)

Android Studio が新しい Android プロジェクトを準備するまでしばらく時間がかかります。 次に、Speech SDK への理解を深め、Java 8 を使用するためのプロジェクトを構成します。

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK の現在のバージョンは `1.1.0` です。

Android 用 Speech SDK は、必要なライブラリと、それを使用するために必要な Android アクセス許可を含む [AAR (Android ライブラリ)](https://developer.android.com/studio/projects/android-library) としてパッケージ化されます。
ホストされている Maven リポジトリの場所は https://csspeechstorage.blob.core.windows.net/maven/ です。

Speech SDK を使用するためにプロジェクトを設定します。 Android Studio のメニュー バーから **[ファイル]** > **[Project Structure]** (プロジェクトの構造) を選択してプロジェクトの構造のウィンドウ開きます。 [Project Structure] (プロジェクトの構造) ウィンドウで、次の変更を行います。

1. ウィンドウの左側にあるリストから、**[プロジェクト]** を選択します。 **[Default Library Repository]\(既定のライブラリ リポジトリ\)** 設定を編集して、コンマと、一重引用符で囲んだ Maven リポジトリの URL を追加します。 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![[Project Structure] (プロジェクトの構造) ウィンドウのスクリーンショット](media/sdk/qs-java-android-06-add-maven-repository.png)

1. 同じ画面の左側で、**[app]\(アプリ\)** を選択します。 次に、ウィンドウの上部にある **[Dependencies]\(依存関係\)** タブを選択します。 緑色のプラス記号 (+) を選択して、ドロップダウン メニューから **[Library dependency]\(ライブラリ依存関係\)** を選択します。

   ![[Project Structure] (プロジェクトの構造) ウィンドウのスクリーンショット](media/sdk/qs-java-android-07-add-module-dependency.png)

1. 表示されたウィンドウで、Android 用 Speech SDK の名前とバージョン (`com.microsoft.cognitiveservices.speech:client-sdk:1.1.0`) を入力します。 **[OK]** をクリックします。
   次に示すように、依存関係のリストに Speech SDK が追加されます。

   ![[Project Structure] (プロジェクトの構造) ウィンドウのスクリーンショット](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. **[プロパティ]** タブを選択します。**[Source Compability]\(ソース互換性\)** と **[Target Compatibility]\(ターゲット互換性\)** はどちらも **[1.8]** を選択します。

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. **[OK]** を選択してプロジェクトの構造 ウィンドウを閉じ、プロジェクトに変更を適用します。

## <a name="create-user-interface"></a>ユーザー インターフェイスを作成する

アプリケーション用の基本的なユーザー インターフェイスを作成します。 メイン アクティビティのレイアウト `activity_main.xml` を編集します。 最初に、レイアウトにはアプリケーションの名前およびテキスト "Hello World!" を含む TextView のタイトル バーが含まれています。

* TextView 要素をクリックします。 右上隅にあるその ID 属性を `hello` に変更します。

* `activity_main.xml` ウィンドウの左上のパレットから、テキストの上の何もないスペースにボタンをドラッグします。

* 右側のボタンの属性で、`onClick` 属性の値に「`onSpeechButtonClicked`」と入力します。 ボタン イベントを処理するこの名前を持つメソッドを記述します。  右上隅にあるその ID 属性を `button` に変更します。

* レイアウトの制約を推測したい場合、デザイナーの上部にある魔法の杖のアイコンを使用します。

  ![魔法の杖アイコンのスクリーンショット](media/sdk/qs-java-android-10-infer-layout-constraints.png)

この時点で、UI のテキストおよびグラフィカル表現は次のようになります。

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル `MainActivity.java` を開きます。 このファイル内のすべてのコードを次の内容に置き換えます。

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` メソッドには、マイクとインターネットのアクセス許可を要求し、ネイティブ プラットフォーム バインディングを初期化するコードが含まれています。 ネイティブ プラットフォーム バインディングの構成は 1 回だけ必要です。 アプリケーションの初期化中に早期に行う必要があります。

   * メソッド `onSpeechButtonClicked` は上述の通りボタン クリック ハンドラーです。 ボタンを押下すると、音声テキスト変換がトリガーされます。

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. Android デバイスを開発用 PC に接続します。 デバイスで[開発モードと USB デバッグ](https://developer.android.com/studio/debug/dev-options)が有効なことを確認します。

1. アプリケーションをビルドするには、Ctrl + F9 キーを押すか、メニュー バーから **[ビルド]** > **[Make Project]\(プロジェクトの作成\)** を選択します。

1. アプリケーションを起動するには、Shift + F10 キーを押すか、**[実行]** > **[Run 'app']\(アプリの実行\)** を選択します。

1. 表示された配置ターゲット ウィンドウで、Android デバイスを選択します。

   ![配置ターゲットの選択ウィンドウのスクリーンショット](media/sdk/qs-java-android-12-deploy.png)

アプリケーションのボタンを押して音声認識に関するセクションを開始します。 次の 15 秒間の英語スピーチが Speech サービスに送信されて変換されます。 結果は Android アプリケーションと Android Studio の logcat ウィンドウに表示されます。

![Android アプリケーションのスクリーンショット](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/java-android` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech SDK for Java を使用して音声から意図を認識する](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>関連項目

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
