---
title: 'クイック スタート: Android で Cognitive Services Speech SDK を使用して Java で音声を認識する'
titleSuffix: Microsoft Cognitive Services
description: Android で Cognitive Services Speech SDK を使用して Java で音声を認識する方法について説明します
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: ad1bf15f198529487dbed92d8f8639704b0c5fc7
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325127"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>クイック スタート: Android で Speech SDK を使用して Java で音声を認識する

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services Speech SDK を使用して音声をテキストに変換する Android 向け Java アプリケーションを作成する方法について説明します。
このアプリケーションは、Microsoft Cognitive Services Speech SDK Maven パッケージ (バージョン 0.5.0) と Android Studio 3.1 が基になっています。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](speech-devices-sdk.md) のページを参照してください。

## <a name="prerequisites"></a>前提条件

* Speech サービスのサブスクリプション キー。 [Speech サービスを無料で試す](get-started.md)ための記事を参照してください。
* Android Studio を実行できる PC (Windows、Linux、Mac)。
* バージョン 3.1 の [Android Studio](https://developer.android.com/studio/)。
* [開発が有効化](https://developer.android.com/studio/debug/dev-options)されており、マイクが動作する ARM ベースの Android デバイス (API 23: Android 6.0 Marshmallow 以上)。

## <a name="create-an-android-studio-project"></a>Android Studio プロジェクトの作成

Android Studio を起動し、**[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

表示される **[新しいプロジェクトの作成]** ウィザードで、次のように選択します。

1. **[Create Android Project]\(Android プロジェクトの作成\)** 画面で、**アプリケーション名**として「**Quickstart**」、**会社のドメイン**として「**samples.speech.cognitiveservices.microsoft.com**」と入力し、プロジェクトの場所を選択します。 チェック ボックスはオフのままにして、**[次へ]** をクリックします。

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. **[Target Android Devices]\(ターゲットの Android デバイス\)** 画面で、ただ 1 つのオプションとして **[Phone and Tablet]\(携帯電話とタブレット\)** チェック ボックスをオンにし、その下のドロップダウン リストから **[API 23: Android 6.0 (Marshmallow)]** を選択して、**[次へ]** をクリックします。

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. **[Add an Activity to Mobile]\(モバイルにアクティビティを追加\)** 画面で、**[Empty Activity]\(空のアクティビティ\)** を選択して **[次へ]** をクリックします。

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. **[Configure Activity]\(アクティビティの構成\)** 画面で、アクティビティ名として **MainActivity** を、レイアウト名として **activity\_main** を使用します。 両方のチェック ボックスをオンにして **[完了]** をクリックします。

   ![](media/sdk/qs-java-android-05-configure-activity.png)

しばらく実行した後、新しく作成された Android Studio プロジェクトが表示されます。

## <a name="configure-your-project-for-the-speech-sdk"></a>プロジェクトを Speech SDK 用に構成する

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK の現在のバージョンは `0.5.0` です。

Android 用 Speech SDK は、必要なライブラリと、それを使用するために必要な Android アクセス許可を含む [AAR (Android ライブラリ)](https://developer.android.com/studio/projects/android-library) としてパッケージ化されます。
ホストされている Maven リポジトリの場所は https://csspeechstorage.blob.core.windows.net/maven/ です。

以下、Speech SDK を使用するためにプロジェクトを設定する方法について説明します。

**[ファイル]** \> **[プロジェクト構造]** 下でプロジェクト構造ウィンドウを開きます。
表示されたウィンドウで次の変更を行います (すべての手順を完了するまで **[OK]** をクリックしないでください)。

1. **[プロジェクト]** を選択し、**[Default Library Repository]\(既定のライブラリ リポジトリ\)** 設定を編集して、コンマと、一重引用符で囲んだ Maven リポジトリの URL `'https://csspeechstorage.blob.core.windows.net/maven/'` を追加します。

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. 同じ画面の左側で、**[アプリ]** モジュールを選択し、上部の **[依存関係]** タブを選択します。右上隅の緑色のプラス記号をクリックし、**[Library dependency]\(ライブラリ依存関係\)** を選択します。

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. 表示されたウィンドウで、Android 用 Speech SDK の名前とバージョン (`com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`) を入力して **[OK]** をクリックします。
   次に示すように、依存関係のリストに Speech SDK が追加されます。

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. 上部の **[プロパティ]** タブを選択します。**[Source Compability]\(ソース互換性\)** と **[Target Compatibility]\(ターゲット互換性\)** はどちらも **[1.8]** を選択します。

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. 最後に、**[OK]** をクリックして **[プロジェクトの構造]** ウィンドウを閉じ、すべての更新を適用します。

## <a name="create-a-minimal-ui"></a>最小限の UI の作成

メイン アクティビティのレイアウト `activity_main.xml` を編集します。
既定では、タイトル バーにアプリケーションの名前、TextView に「Hello World!」が表示されます。

* TextView をクリックします。 右上隅にあるその ID 属性を `hello` に変更します。

* `activity_main.xml` ウィンドウの左上のパレットから、テキストの上の何もないスペースにボタンをドラッグします。

* 右側のボタンの属性で、`onClick` 属性の値に「`onSpeechButtonClicked`」と入力します。これはボタン ハンドラーの名前になります。
  右上隅にあるその ID 属性を `button` に変更します。

* レイアウトの制約を推測したい場合、デザイナーの上部にある魔法の杖のアイコンを使用します。

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

この時点で、UI のテキストおよびグラフィカル バージョンは次のようになります。

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

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. `MainActivity.java` ソース ファイルを編集して、そのコードを次の内容に置き換えます (package ステートメントに続く部分)。

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` メソッドには、マイクとインターネットのアクセス許可を要求し、ネイティブ プラットフォーム バインディングを初期化するコードが含まれています。 ネイティブ プラットフォーム バインディングの構成は、アプリケーション初期化中の早い時期に一度だけ行う必要があります。
   
   * メソッド `onSpeechButtonClicked` は、以前はボタン クリック ハンドラーとして結び付けられていました。 ボタン押下は実際の音声認識をトリガーします。

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

* ビルドするには、Ctrl + F9 キーを押すか、または **[ビルド]** \> **[Make Project]\(プロジェクトのメイク\)** を選択します。

* Android デバイスを開発用 PC に接続します。 [開発モードと USB デバッグが有効](https://developer.android.com/studio/debug/dev-options)なことを確認します。

* アプリを起動するには、Shift + F10 キーを押すか、または **[実行]** \> **[Run 'app']\(’アプリ’ を実行\)** を選択します。

* 表示された配置ターゲット ウィンドウで、Android デバイスを選択します。

  ![アプリを起動してデバッグする](media/sdk/qs-java-android-12-deploy.png)

* デバイス上でアプリが起動するはずです。
  ボタンを押すと、そこから 15 秒間が認識され、UI に表示されます (Android Studio の logcat ウィンドウでも応答が確認できるはずです)。

  ![認識が成功した後の UI](media/sdk/qs-java-android-13-gui-on-device.png)

このスクリーンショットで Android のクイック スタートは終わりです。 プロジェクトの完全なサンプル コードはサンプル リポジトリからダウンロードできます。

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
このサンプルは、`quickstart/java-android` フォルダーで探してください。

## <a name="next-steps"></a>次の手順

* [サンプルを入手する](speech-sdk.md#get-the-samples)
