---
title: Speech Devices SDK を使ってみる
description: Speech Devices SDK の前提条件と手順です。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 266315a731eec8a2c0ab0a880ce9e1db58331184
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283138"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Speech Devices SDK を使ってみる

この記事では、Speech Devices SDK を使用して、音声対応デバイスを開発するための開発 PC と音声デバイス開発キットの構成方法について説明します。 その後、サンプル アプリケーションをビルドしてデバイスに配置します。 

サンプル アプリケーションのソース コードは Speech Devices SDK に付属しています。[GitHub で入手する](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)こともできます。

## <a name="prerequisites"></a>前提条件

Speech Devices SDK を使用した開発を開始する前に、必要な情報とソフトウェアを集めてください。

* [Roobo から](http://ddk.roobo.com/)開発キットを取得します。 直線または円形のマイク配列構成のキットを使用できます。ニーズに適したキットを選択してください。

    |開発キットの構成|スピーカーの位置|
    |-----------------------------|------------|
    |円形|デバイスの任意の方向|
    |Linear|デバイスの正面|

* Speech Devices SDK の[ダウンロード サイト](https://shares.datatransfer.microsoft.com/)から、Android サンプル アプリを含む最新バージョンの Speech Devices SDK を取得します。 ZIP ファイルをローカル フォルダーに抽出します (`C:\SDSDK` など)。

* [Android Studio](https://developer.android.com/studio/) と [Vysor](http://vysor.io/download/) を PC にインストールします。

* Speech Service の[サブスクリプション キー](get-started.md)を取得します。 30 日間の無料試用版を入手するか、Azure ダッシュボードからキーを取得できます。

* Speech Service の意図認識を使用する場合は、[Language Understanding Intelligent Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)(LUIS) をサブスクライブし、[サブスクリプション キーを取得](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription)します。 

    [単純な LUIS モデルを作成する](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)か、Speech Devices SDK [ダウンロード サイト](https://shares.datatransfer.microsoft.com/)から入手できるサンプル LUIS モデル`LUIS-example.json`を使用できます。 **[新しいアプリのインポート]** をクリックし、モデルの JSON ファイルを選択して、その JSON ファイルを [LUIS ポータル](https://www.luis.ai/home)にアップロードします。

## <a name="set-up-the-development-kit"></a>開発キットをセットアップする

1. 開発キットの電源アダプターをコンセントに差し込みます。 トップ ボードの下で緑色の電源インジケーターが点灯します。

1. 開発キットをミニ USB ケーブルを使用してコンピューターに接続します。

    ![開発キットの接続](media/speech-devices-sdk/qsg-1.jpg)

1. 開発キットを適切な方向に向けます。

    |開発キットの構成|方向|
    |-----------------------------|------------|
    |円形|縦。マイクが天井に向くように置く|
    |Linear|横。マイクが自分を向くように置く (下を参照)|

    ![直線開発キットの向き](media/speech-devices-sdk/qsg-2.jpg)

1. 証明書とウェイク ワード (キーワード) テーブル ファイルをインストールし、サウンド デバイスのアクセス許可を設定します。 コマンド ウィンドウで次のコマンドを入力します。

    > [!NOTE]
    > これらのコマンドでは、Android Studio のインストールの一部である Android Debug Bridge `adb.exe` を使用します。 このツールは `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools` にあります。 このディレクトリをパスに追加して、`adb` を呼び出しやすくすることができます。 それ以外の場合は、`adb` を呼び出すすべてのコマンドで `adb.exe` の完全なインストール パスを指定する必要があります。

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > PC のマイクとスピーカーをミュートにします。 これにより、確実に開発キットのマイクで作業でき、PC からの音声によってデバイスを誤ってトリガーすることがなくなります。
    
1.  コンピューターで Vysor を起動します。

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  お使いのデバイスが [Choose a device]\(デバイスを選択する\) の下に表示されます。 その隣の **[View]\(ビュー\)** ボタンをクリックします。 
 
1.  **[Settings] \(設定\)**、**[WLAN]** の順にクリックして、ワイヤレス ネットワークに接続します。

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>サンプル アプリケーションを実行する

Roobo テストを実行して開発キットのセットアップを検証するには、サンプル アプリケーションをビルドしてインストールします。

1.  Android Studio を起動します。

1.  [Open an existing Android Studio project]\(既存の Android Studio プロジェクトを開く\) を選択します。

    ![Android studio - 既存のプロジェクトを開く](media/speech-devices-sdk/qsg-5.png)
 
1.  `C:\SDSDK\Android-Sample-Release\example` を参照し、**[OK]** をクリックしてサンプル プロジェクトを開きます。
 
1.  ソース コードに Speech サブスクリプション キーを追加します。 意図認識を試す場合は、[Language Understanding Intelligent Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) のサブスクリプション キーとアプリケーション ID も追加します。 

    キーとアプリケーションの情報は、ソース ファイル `MainActivity.java` の次の行に配置されます。

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. 既定のウェイク ワード (キーワード) は "Computer" です。  必要に応じて、用意されている別のウェイク ワード ("Machine" と "Assistant") を試すこともできます。 これらの代替ワード用のリソース ファイルは、Speech Devices SDK の "keyword" フォルダーで見つけることができます。 たとえば、`C:\SDSDK\Android-Sample-Release\keyword\Computer` には、"Computer" で使用されるファイルが含まれています。

    [カスタム ウェイク ワードを作成する](speech-devices-sdk-create-kws.md)こともできます。

    目的のウェイク ワードをインストールするには:
 
    * コマンド ウィンドウで次のコマンドを使用して、デバイスの \data\ フォルダー内に `keyword` フォルダーを作成します。

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * `kws.table`、`kws_g.fst`、`kws_k.fst`、および `words_kw.txt` の各ファイルをデバイスの \data\keyword\ フォルダーにコピーします。 コマンド ウィンドウで次のコマンドを実行します。

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * サンプル アプリケーションでこれらのファイルを参照します。 `MainActivity.java` で次の行を探します。 使用するキーワードが指定されていること、パスがデバイスにプッシュした `kws.table` ファイルを指していることを確認します。
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > 独自のコード内で、次のように `kws.table` ファイルを使用して、キーワード モデル インスタンスを作成して認識を開始できます、
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  マイク配列ジオメトリ設定が含まれる次の行を更新します。

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |可変|意味|使用できる値|
    |--------|-------|----------------|
    |`DeviceGeometry`|物理的なマイクの構成|円形開発キットでは `Circular6+1`|
    ||| 直線開発キットでは `Linear4`|
    |`SelectedGeometry`|ソフトウェアのマイクの構成|すべてのマイクを使用する円形開発キットでは `Circular6+1`|
    |||4 つのマイクを使用する開発キットでは `Circular3+1`|
    |||すべてのマイクを使用する直線開発キットでは `Linear4`|
    |||2 つのマイクを使用する直線開発キットでは `Linear2`|


1.  [Run]\(実行\) メニューの **[Run 'app']\('アプリ' の実行\)** を選択して、アプリをビルドします。 [Select Deployment Target]\(配置ターゲットの選択\) ダイアログが表示されます。 デバイスを選択し、**[OK]** をクリックしてアプリケーションをデバイスに配置します。

    ![配置ターゲットの選択](media/speech-devices-sdk/qsg-7.png)
 
1.  Speech Devices SDK のサンプル アプリケーションが起動し、次のオプションが表示されます。

    ![サンプル スピーチ デバイス アプリケーション](media/speech-devices-sdk/qsg-8.png)

1. いろいろ試してください。

## <a name="troubleshooting"></a>トラブルシューティング

Speech Service を使用する際に証明書エラーが発生した場合は、デバイスの日付と時刻が正しいことを確認します。

開発の詳細については、Roobo の[開発ガイド](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf) を参照してください。

Roobo では、すべての音声をフラッシュ メモリにキャプチャするツールを提供しています。音声に関する問題のトラブルシューティングに役立てることができます。 ツールのバージョンは、開発キットの構成ごとに提供されています。 [Roobo サイト](http://ddk.roobo.com/)でデバイスを選択し、ページの下部にある **[ROOBO Tools]** リンクをクリックします。
