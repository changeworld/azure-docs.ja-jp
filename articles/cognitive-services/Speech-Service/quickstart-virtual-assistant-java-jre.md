---
title: クイック スタート:カスタムの音声優先仮想アシスタント (プレビュー)、Java (Windows、Linux) - Speech Services
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Cognitive Services Speech ソフトウェア開発キット (SDK) を Java コンソール アプリケーションで使用する方法を学習します。 クライアント アプリケーションを、Direct Line Speech チャネルを使用するように構成されている作成済みの Bot Framework ボットに接続して、音声優先仮想アシスタント エクスペリエンスを有効にする方法を学習します。
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025367"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>クイック スタート:Speech SDK (Java) を使用して音声優先仮想アシスタントを作成する

この記事では、[Cognitive Services Speech SDK](speech-sdk.md) を使用して、Java コンソール アプリケーションを作成します。 アプリケーションは、Direct Line Speech チャネルを使用するように構成されている作成済みのボットに接続し、音声要求を送信し、音声応答アクティビティ (構成されている場合) を返します。 アプリケーションは、Speech SDK Maven パッケージと、Windows、Ubuntu Linux、または macOS 上の Eclipse Java IDE を使用して構築されます。 これは、64 ビットの Java 8 のランタイム環境 (JRE) で実行されます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* オペレーティング システム:Windows (64 ビット)、Ubuntu Linux 16.04/18.04 (64 ビット)、または macOS 10.13 以降
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Speech Service 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* Bot Framework バージョン 4.2 以上を使用して作成された事前構成済みのボット。 ボットは、音声入力を受信するために新しい "Direct Line Speech" チャネルに登録する必要があります。

    > [!NOTE]
    > プレビューでは、Direct Line Speech チャネルは、現在 **westus2** リージョンのみをサポートしています。

    > [!NOTE]
    > 「[Speech Services を無料で試す](get-started.md)」に記載されている Standard 価格レベルの 30 日間の無料試用版は **westus** (**westus2** ではありません) に制限されているため、Direct Line Speech との互換性はありません。 Free および Standard レベルの **westus2** サブスクリプションには互換性があります。

Ubuntu 16.04 または 18.04 を実行している場合は、Eclipse を起動する前に、これらの依存関係がインストールされていることを確認してください。

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Windows (64 ビット版) を実行している場合は、お使いのプラットフォーム用の Microsoft Visual C++ 再頒布可能パッケージがインストールされていることを確認してください。
* [Visual Studio 2017 の Microsoft Visual C++ 再頒布可能パッケージをダウンロードする](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>省略可能:すぐに開始

このクイック スタートでは、音声対応のボットに接続する単純なクライアント アプリケーションを作成する方法を順を追って説明します。 このクイック スタートに使用されている、すぐにコンパイルできる完全なソース コードは、[Speech SDK のサンプル](https://aka.ms/csspeech/samples)のページの `quickstart` フォルダーに含まれています。

## <a name="create-and-configure-project"></a>プロジェクトの作成と構成

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

さらに、ログ記録を有効にするには、**pom.xml** ファイルを更新して次の依存関係を含めます。

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>サンプル コードを追加する

1. 新しい空のクラスを Java プロジェクトに追加するために、**[File]**  >  **[New]**  >  **[Class]** の順に選択します。

1. **[New Java Class]** ウィンドウで、**[Package]** フィールドに **speechsdk.quickstart** と入力し、**[Name]** フィールドに **Main** と入力します。

   ![[New Java Class] ウィンドウのスクリーンショット](media/sdk/qs-java-jre-06-create-main-java.png)

1. 新しく作成された **Main** クラスを開き、`Main.java` ファイルの内容を次の開始コードに置き換えます。

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. **main** メソッドでは、最初に `BotConnectorConfig` を構成し、それを使用して `SpeechBotConnector` インスタンスを作成します。 これにより、ボットと対話するために Direct Line Speech チャネルに接続されます。 `AudioConfig` インスタンスは、音声入力のソースを指定するためにも使用されます。 この例では、`AudioConfig.fromDefaultMicrophoneInput()` により既定のマイクが使用されます。

    * 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。サブスクリプション キーは、[こちら](get-started.md)から入手できます。
    * 文字列 `YourServiceRegion` を、自分のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。
    * 文字列 `YourChannelSecret` を、Direct Line Speech チャネルのシークレットに置き換えます。

    > [!NOTE]
    > プレビューでは、Direct Line Speech チャネルは、現在 **westus2** リージョンのみをサポートしています。

    > [!NOTE]
    > 「[Speech Services を無料で試す](get-started.md)」に記載されている Standard 価格レベルの 30 日間の無料試用版は **westus** (**westus2** ではありません) に制限されているため、Direct Line Speech との互換性はありません。 Free および Standard レベルの **westus2** サブスクリプションには互換性があります。

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` は、ボットのアクティビティ、音声認識の結果、およびその他の情報を伝達するために、いくつかのイベントに依存しています。 次に、これらのイベント リスナーを追加します。

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. `connectAsync()` メソッドを呼び出して、`SpeechBotConnector` を Direct Line Speech に接続します。 ボットをテストするには、`listenOnceAsync` メソッドを呼び出してマイクから音声入力を送信します。 さらに、`sendActivityAsync` メソッドを使用して、カスタム アクティビティをシリアル化された文字列として送信することもできます。 これらのカスタム アクティビティを使用すると、ボットが会話に使用する追加データを提供できます。

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. 変更を `Main` ファイルに保存します。

1. 応答の再生をサポートするためには、音声をサポートするユーティリティ メソッドが含まれる追加のクラスを追加します。 音声を有効にするには、新しい別の空のクラスを Java プロジェクトに追加します。そのためには、**[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Class]\(クラス\)** の順に選択します。

1. **[New Java Class]\(新しい Java クラス\)** ウィンドウで、**[Package]\(パッケージ\)** フィールドに「**speechsdk.quickstart**」と入力し、**[Name]\(名前\)** フィールドに「**AudioPlayer**」と入力します。

   ![[New Java Class] ウィンドウのスクリーンショット](media/sdk/qs-java-jre-06-create-main-java.png)

1. 新しく作成された **AudioPlayer** クラスを開き、次のコードに置き換えます。

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. 変更を `AudioPlayer` ファイルに保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

F11 キーを押すか、**[Run]**  >  **[Debug]** の順に選択します。
コンソールに "Say something" というメッセージが表示されます。この時点で、ボットが理解できる英語の語句や文を話しかけることができます。 音声は Direct Line Speech チャネルを介してボットに送信されます。ボットによって音声が認識および処理された後、その応答がアクティビティとして返されます。 ボットが応答として音声を返す場合、音声は `AudioPlayer` クラスを使用して再生されます。

![認識が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルから音声を読み取る方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で Java のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [クイック スタート:音声を翻訳する、Java (Windows、Linux)](quickstart-translate-speech-java-jre.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
