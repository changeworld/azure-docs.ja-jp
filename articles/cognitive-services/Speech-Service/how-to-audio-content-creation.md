---
title: Audio Content Creation - Speech サービス
titleSuffix: Azure Cognitive Services
description: Audio Content Creation は、Microsoft のテキスト読み上げ出力をご使用のアプリや製品に合わせてカスタマイズしたり微調整したりするためのオンライン ツールです。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: b65a39489f0823a411a9dddf79ec3abd1e5ee20d
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806207"
---
# <a name="audio-content-creation"></a>Audio Content Creation

[Audio Content Creation](https://aka.ms/audiocontentcreation) は、Microsoft のテキスト読み上げ出力をご使用のアプリやと製品に合わせてカスタマイズしたり微調整したりするためのオンライン ツールです。 このツールを使用すると、パブリックの音声やカスタムの音声をより正確で自然な表現にするために微調整したり、クラウドでの出力を管理したりすることができます。

Audio Content Creation ツールは、[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) に基づいています。 カスタマイズとチューニングを簡単にするために、Audio Content Creation を使用して、テキスト読み上げの出力をリアルタイムで視覚的に検査することができます。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

以下の図は、カスタマイズされた音声テキスト変換の出力をチューニングおよびエクスポートするために必要な手順を示しています。 それぞれのステップの詳細については、下のリンクを使用してください。

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. 最初の手順は、[Azure アカウントの作成、Speech リソースの登録、サブスクリプション キーの取得](#create-a-speech-resource)です。 サブスクリプション キーを取得したら、それを使用して Speech サービスを呼び出したり、[Audio Content Creation](https://aka.ms/audiocontentcreation) にアクセスしたりすることができます。
2. プレーンテキストまたは SSML を使用して[音声チューニング ファイルを作成](#create-an-audio-tuning-file)します。
3. チューニングする音声と言語を選択します。 Audio Content Creation には、すべての [Microsoft テキスト読み上げ音声](language-support.md#text-to-speech)が含まれています。 標準、ニューラル、または独自のカスタム音声を使用できます。
   >[!NOTE]
   > カスタム ニューラル音声には、ゲート アクセスを使用できます。これにより、自然な音声と同じような高品位の音声を作成できます。 詳細については、[ゲート プロセス](https://aka.ms/ignite2019/speech/ethics)に関するページを参照してください。

4. 既定の結果を確認します。 次に、チューニング ツールを使用して、発音、ピッチ、レート、イントネーション、音声スタイルなどを調整します。 オプションの完全な一覧については、「[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md)」を参照してください。
5. [チューニングした音声を保存してエクスポートします](#export-tuned-audio)。 チューニング トラックをシステムに保存すると、作業を続行して出力を反復処理することができます。 出力に問題がなければ、エクスポート機能を使用して音声作成タスクを作成できます。 エクスポート タスクの状態を監視し、ご使用のアプリや製品で使用するための出力をダウンロードすることができます。
6. 最後の手順では、ご使用のアプリや製品でカスタムのチューニングされた音声を使用します。

## <a name="create-a-speech-resource"></a>Speech リソースを作成する

Speech リソースを作成して Speech Studio に接続するには、次の手順に従います。

1. 次の手順に従って、[Azure アカウントにサインアップ](get-started.md#try-the-speech-service-using-a-new-azure-account)し、[Speech リソースを作成](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)します。 価格レベルが **S0** に設定されていることを確認します。 ニューラル音声のいずれかを使用している場合は、必ず[サポートされているリージョン](regions.md#standard-and-neural-voices)でリソースを作成してください。
2. [Audio Content Creation](https://aka.ms/audiocontentcreation) にサインインします。
3. 既存のプロジェクトを選択するか、 **[新規作成]** をクリックします。
4. 上部のナビゲーションにある **[設定]** オプションを使用すると、いつでもサブスクリプションを変更できます。

## <a name="create-an-audio-tuning-file"></a>音声チューニング ファイルを作成する

Audio Content Creation ツールにコンテンツを取り込むには、2 つの方法があります。

**オプション 1:**

1. [Audio Content Creation](https://aka.ms/audiocontentcreation) にサインインした後、 **[Audio Tuning]\(音声チューニング\)** をクリックして、新しい音声チューニング ファイルを作成します。
2. 編集ウィンドウが表示されたら、最大 10,000 文字まで入力できます。
3. 忘れずに保存してください。

**オプション 2:**

1. [Audio Content Creation](https://aka.ms/audiocontentcreation) にサインインした後、 **[アップロード]** をクリックして、1 つまたは複数のテキスト ファイルをインポートします。 プレーンテキストと SSML の両方がサポートされています。
2. テキスト ファイルをアップロードするときは、コンテンツが以下の要件を満たしていることを確認してください。

   | プロパティ | 値/備考 |
   |----------|---------------|
   | ファイル形式 | プレーン テキスト (.txt)<br/> SSML テキスト (.txt)<br/> ZIP ファイルはサポートされていません |
   | エンコード形式 | UTF-8 |
   | ファイル名 | 各ファイルには一意の名前が必要です。 重複はサポートされていません。 |
   | テキストの長さ | テキスト ファイルは 10,000 文字を超えないようにしてください。 |
   | SSML の制限 | 各 SSML ファイルには、単一の SSML のみを含めることができます。 |

### <a name="plain-text-example"></a>プレーン テキストの例

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML テキストの例

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>チューニングした音声をエクスポートする

音声出力を確認し、チューニングと調整に問題がなければ、音声をエクスポートできます。

1. 音声作成タスクを作成するには、[Audio Content Creation](https://aka.ms/audiocontentcreation) ツールで **[エクスポート]** をクリックします。
2. チューニングした音声の出力形式を選択します。 サポートされている形式とサンプル レートの一覧については、以下を参照してください。
3. タスクの状態は **[Export task]\(エクスポート タスク\)** タブで確認できます。タスクが失敗した場合は、詳細情報のページで詳細なレポートを確認してください。
4. タスクが完了すると、 **[Audio Library]\(音声ライブラリ\)** タブから音声をダウンロードできるようになります。
5. **[Download]** をクリックします。 これで、カスタムのチューニングされた音声をご使用のアプリや製品で使用する準備ができました。

### <a name="supported-audio-formats"></a>サポートされている音声形式

| 形式 | 16 kHz サンプル レート | 24 kHz サンプル レート |
|--------|--------------------|--------------------|
| wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>関連項目

* [Long Audio API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
