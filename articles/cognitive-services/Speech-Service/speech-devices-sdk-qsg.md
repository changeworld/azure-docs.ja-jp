---
title: Speech Devices SDK の使用 - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK の前提条件と手順です。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 46f7762a815a7fa4aa4663d9ac6e7c6001ea345c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097184"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Speech Devices SDK を使ってみる

この記事では、Speech Devices SDK を使用して、音声対応デバイスを開発するための開発 PC と音声デバイス開発キットの構成方法について説明します。 その後、サンプル アプリケーションをビルドしてデバイスに配置します。

サンプル アプリケーションのソース コードは Speech Devices SDK に付属しています。 [GitHub で入手する](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)こともできます。

## <a name="prerequisites"></a>前提条件

Speech Devices SDK を使用した開発を開始する前に、必要な情報とソフトウェアを集めてください。

* [ROOBO から開発キット](http://ddk.roobo.com/)を取得します。 直線または円形のマイク配列構成のキットを使用できます。 ニーズに適したキットを選択してください。

    |開発キットの構成|スピーカーの位置|
    |-----------------------------|------------|
    |円形|デバイスの任意の方向|
    |Linear|デバイスの正面|

* [Speech Devices SDK のダウンロード サイト](https://shares.datatransfer.microsoft.com/)から、Android サンプル アプリを含む最新バージョンの Speech Devices SDK を取得します。 .zip ファイルをローカル フォルダーに抽出します (C:\SDSDK など)。

* [Android Studio](https://developer.android.com/studio/) と [Vysor](http://vysor.io/download/) を PC にインストールします。

* [音声サービスのサブスクリプション キー](get-started.md)を取得します。 30 日間の無料試用版を入手するか、Azure ダッシュボードからキーを取得できます。

* Speech Service の意図認識を使用する場合は、[Language Understanding Intelligent Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)(LUIS) をサブスクライブし、[サブスクリプション キーを取得](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription)します。

    [LUIS の単純なモデルを作成](https://docs.microsoft.com/azure/cognitive-services/luis/)またはサンプルの LUIS モデルである LUIS-example.json. を使用できます。 サンプルの LUIS モデルは、[Speech Devices SDK のウンロード サイト](https://shares.datatransfer.microsoft.com/)から入手できます。 モデルの JSON ファイルを [LUIS ポータル](https://www.luis.ai/home)にアップロードするには、**[新しいアプリのインポート]** を選択し、その JSON ファイルを選択します。

## <a name="set-up-the-development-kit"></a>開発キットをセットアップする

1. ミニ USB ケーブルを使用して開発キットを PC または電源アダプターに接続します。 キットが接続されると、上部のボードの下で緑色の電源インジケーターが点灯します。

1. 2 本目のミニ USB ケーブルを使用して開発キットをコンピューターに接続します。

    ![開発キットの接続](media/speech-devices-sdk/qsg-1.png)

1. 直線または円形のいずれかの構成になるように開発キットの方向を指定します。

    |開発キットの構成|方向|
    |-----------------------------|------------|
    |円形|縦。マイクが天井に向くように置く|
    |Linear|横。マイクが自分を向くように置く (下のイメージを参照)|

    ![直線開発キットの向き](media/speech-devices-sdk/qsg-2.png)

1. 証明書とウェイク ワード (キーワード) テーブル ファイルをインストールし、サウンド デバイスのアクセス許可を設定します。 コマンド プロンプト ウィンドウで次のコマンドを入力します。

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > これらのコマンドでは、Android Studio のインストールの一部である Android Debug Bridge `adb.exe` を使用します。 このツールは C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools にあります。 このディレクトリをパスに追加して、`adb` を呼び出しやすくすることができます。 それ以外の場合は、`adb` を呼び出すすべてのコマンドで adb.exe の完全なインストール パスを指定する必要があります。

    > [!TIP]
    > PC のマイクとスピーカーをミュートし、開発キットのマイクを操作していることがわかるようにします。 こうすることによって、PC からの音声によってデバイスが誤ってトリガされなくなります。

1.  コンピューターで Vysor を起動します。

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  お使いのデバイスが **[Choose a device]** \(デバイスを選択する\) の下に表示されます。 デバイスの横にある **[View]\(ビュー\)** ボタンを選択します。

1.  フォルダー アイコンを選択した後、**[Settings]\(設定\)** > **[WLAN]** の順に選択して、ワイヤレス ネットワークに接続します。

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Wi-Fi システムへのデバイスの接続に関するポリシーが会社にある場合は、MAC アドレスを取得し、会社の Wi-Fi への接続方法を IT 部門に問い合わせる必要があります。
    >
    > 開発キットの MAC アドレスを検索するには、開発キットのデスクトップ上のファイル フォルダー アイコンを選択します。
    >
    >  ![Vysor ファイル フォルダー](media/speech-devices-sdk/qsg-10.png)
    >
    > **[設定]** を選択します。 "mac address" を検索し、**[Mac アドレス]** > **[Advanced WLAN]**(高度な WLAN) の順に選択します。 ダイアログ ボックスの下部近くに表示される MAC アドレスを書き留めます。
    >
    > ![Vysor の MAC アドレス](media/speech-devices-sdk/qsg-11.png)
    >
    > 会社によっては、デバイスを Wi-Fi システムに接続しておくことができる時間が制限されている場合があります。 一定の日数が経過した後、Wi-Fi システムへの開発キットの登録を延長することが必要な場合があります。
    >
    > スピーカーを開発キットに接続する場合は、オーディオのライン出力に接続できます。高品質の 3.5 mm スピーカーを選択する必要があります。
    >
    > ![Vysor オーディオ](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>サンプル アプリケーションを実行する

ROOBO テストを実行して開発キットのセットアップを検証するには、サンプル アプリケーションをビルドしてインストールします。

1.  Android Studio を起動します。

1.  **[Open an existing Android Studio project]\(既存の Android Studio プロジェクトを開く\)** を選択します。

    ![Android Studio - 既存のプロジェクトを開く](media/speech-devices-sdk/qsg-5.png)

1.  C:\SDSDK\Android-Sample-Release\example に移動します。 **[OK]** を選択してサンプル プロジェクトを開きます。

1.  ソース コードに Speech サブスクリプション キーを追加します。 意図認識を試す場合は、[Language Understanding Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) のサブスクリプション キーとアプリケーション ID も追加します。

    キーとアプリケーションの情報は、ソース ファイル MainActivity.java の次の行に配置されます。

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app ID]"
    ```

1. 既定のウェイク ワード (キーワード) は "Computer" です。 用意されている別のウェイク ワード ("Machine"、"Assistant" など) を試すこともできます。 これらの代替ウェイク ワード用のリソース ファイルは、Speech Devices SDK の keyword フォルダーにあります。 たとえば、C:\SDSDK\Android-Sample-Release\keyword\Computer には、ウェイク ワード "Computer" に使用されるファイルが含まれています。

    [カスタム ウェイク ワードを作成する](speech-devices-sdk-create-kws.md)こともできます。

    使用するウェイク ワードをインストールする方法

    * コマンド プロンプト ウィンドウで次のコマンドを実行して、デバイスの data フォルダー内に keyword フォルダーを作成します。

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * ファイル kws.table, kws_k.fst と words_kw.txt をデバイスの \data\keyword フォルダーにコピーします。 コマンド プロンプト ウィンドウで次のコマンドを実行します。 [カスタム ウェイク ワード](speech-devices-sdk-create-kws.md)を作成した場合、Web から生成された kws.table ファイルは、kws.table、kws_k.fst、および words_kw.txt の各ファイルと同じディレクトリに格納されます。 カスタム ウェイク ワードでは、`adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword` コマンドを使用して kws.table ファイルを開発キットにプッシュします。

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```

    * サンプル アプリケーションでこれらのファイルを参照します。 MainActivity.java で、次の行を見つけます。 使用するキーワードが指定されていること、パスがデバイスにプッシュした `kws.table` ファイルを指していることを確認します。

        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > 独自のコード内で、次のように kws.table ファイルを使用して、キーワード モデル インスタンスを作成して認識を開始できます、
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
    次の表に、使用可能な値を示します。

    |可変|意味|使用できる値|
    |--------|-------|----------------|
    |`DeviceGeometry`|物理的なマイクの構成|円形開発キットでは `Circular6+1` |
    |||直線開発キットでは `Linear4`|
    |`SelectedGeometry`|ソフトウェアのマイクの構成|すべてのマイクを使用する円形開発キットでは `Circular6+1`|
    |||4 つのマイクを使用する円形開発キットでは `Circular3+1`|
    |||すべてのマイクを使用する線形開発キットでは `Linear4`|
    |||2 つのマイクを使用する線形開発キットでは `Linear2`|


1.  アプリケーションをビルドするには、**[実行]** メニューで **[アプリの実行]** を選択します。 **[Select Deployment Target]** \(配置ターゲットの選択\) ダイアログ ボックスが表示されます。

1. デバイスを選択し、**[OK]** を選択してアプリケーションをデバイスに配置します。

    ![[Select Deployment Target]\(配置ターゲットの選択\) ダイアログ ボックス](media/speech-devices-sdk/qsg-7.png)

1.  Speech Devices SDK のサンプル アプリケーションが起動し、次のオプションが表示されます。

    ![Speech Devices SDK のサンプル アプリケーションとオプション](media/speech-devices-sdk/qsg-8.png)

1. 実験

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="certificate-failures"></a>証明書エラー

Speech Service を使用する際に証明書エラーが発生した場合は、デバイスの日付と時刻が正しいことを確認します。

1. **[設定]** に移動します。 **[システム]** で、**[日付と時刻]** を選択します。

    ![[設定] で、[日付と時刻] を選択する](media/speech-devices-sdk/qsg-12.png)

1. **[Automatic date & time]**(自動の日付と時刻) オプションは選択したままにします。 **[タイム ゾーンの選択]** で、現在のタイム ゾーンを選択します。

    ![日付とタイム ゾーンのオプションを選択する](media/speech-devices-sdk/qsg-13.png)

    開発キットの時刻が PC の時刻と一致している場合、開発キットはインターネットに接続されています。

    開発の詳細については、「[ROOBO の開発ガイド](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)」を参照してください。

### <a name="audio"></a>オーディオ

ROOBO には、すべてのオーディオをキャプチャし、メモリをフラッシュするツールが用意されています。 オーディオに関する問題のトラブルシューティングに役立ちます。 ツールのバージョンは、開発キットの構成ごとに提供されています。 [ROOBO サイト](http://ddk.roobo.com/)でデバイスを選択し、ページの下部にある **[ROOBO Tools]** リンクを選択します。
