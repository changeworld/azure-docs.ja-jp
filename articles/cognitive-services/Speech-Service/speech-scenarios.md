---
title: Azure Cognitive Services の音声のシナリオ
description: シナリオ
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: 3ec5fede8b4e196a2f7573b4c2f202d589a55f32
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282670"
---
# <a name="speech-scenarios"></a>音声のシナリオ

音声認識テクノロジを使用して強化できる多くのシナリオがあります。 ドキュメントでは、最も一般的な機能のいくつかを分析し、関連する機能について説明しています。 ほとんどのコンテンツで、[SDK](speech-sdk.md) はこれらのシナリオを実現するための中心です。

ページでは以下の手順について説明します。
> [!div class="checklist"]
> * 音声によってトリガーされるアプリを作成する
> * コール センターの音声通話を文字起こしする
> * 音声ボット

## <a name="voice-triggered-apps"></a>音声トリガー アプリ

多くのユーザーは、アプリケーションで音声入力を有効にすることを望みます。 音声入力は、アプリを柔軟にして、(たとえば車内で) ハンズフリーで使用できるようにしたり、道案内、ニュース、気象情報を聞くといった色々なタスクを高速化したりするための優れた方法です。 

### <a name="voice-triggered-apps-with-baseline-models"></a>ベースライン モデルによる音声トリガー アプリ

周囲の騒音が過度でない環境で一般の人々がアプリを使用する場合、これを行うための最も簡単ですばやい方法は、[Speech SDK](speech-sdk.md) をダウンロードし、関連[サンプル](quickstart-csharp-dotnet-windows.md)に従うことです。 開発者は SDK と [Azure サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を使用して、Cortana や Skype で使用されている ベースライン音声認識モデルにオーディオをアップロードできます。 モデルは最先端のものであり、前述の製品で使用されています。 数分で稼働状態にできます。

### <a name="voice-triggered-apps-with-custom-models"></a>カスタム モデルによる音声トリガー アプリ

(科学、生物学、特殊なダイエットのニーズなど) 特定の領域をアプリで扱う場合、[言語モデル](how-to-customize-language-model.md)の適応を検討することができます。 言語モデルの適応では、アプリで最もよく使われるフレーズや単語をデコーダーに学習させます。 デコーダーは、ベースライン モデルではなく、特定領域用のカスタム言語モデルを使用して、音声入力をより正確に文字起こしできるようになります。 同様に、アプリが使用される環境で周囲の雑音が顕著な場合、音響モデルを適応させることができます。 [言語の適応](how-to-customize-language-model.md)および[音響適応](how-to-customize-acoustic-models.md)を検討する価値があるその他のケースについては、ドキュメントを参照してください。モデルの作成を開始するには、[適応ポータル](https://customspeech.ai)にアクセスしてください。 ベースライン モデルと同様、カスタム モデルは [Speech SDK](speech-sdk.md) 経由で、関連[サンプル](quickstart-csharp-dotnet-windows.md)に従って呼び出されます。

## <a name="transcribe-call-center-audio-calls"></a>コール センターの音声通話を文字起こしする

コール センターでは、大量のオーディオを蓄積します。 文字起こしによって、それらのオーディオ ファイルの中に隠れている価値を取り出すことができます。 通話の文字起こしを取得することによって、通話時間、感情、顧客満足度、通話が発信者に提供した価値全般を洗い出すことができます。

最も良い開始点は、[Batch transcription API](batch-transcription.md) と関連[サンプル](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)です。

まず [Azure サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を入手した後、[ドキュメント]([Batch transcription API](batch-transcription.md))を参照する必要があります。

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>ベースライン モデルを使用してコール センターのオーディオ通話を文字起こしする

内部ベースライン モデルを使用して文字起こしを実行するか、それとも、言語モデルと音響モデルのどちらかまたは両方を適応させるかを決定する必要があります。 ベースライン モデルを使用する場合、API に必要なのは API キーだけです。 内部的には、API は対象データ向けの最適なモデルを呼び出し、適応させます。

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>カスタム モデルを使用してコール センターのオーディオ通話を文字起こしする

カスタム モデルを使用する予定の場合、API キーに加えてそのモデルの ID も必要になります。 モデル ID は[適応ポータル](https://customspeech.ai)から取得されます。 これは [エンドポイントの詳細] ビューに表示されるエンドポイント ID ではなく、そのモデルの [詳細] をクリックして取得できるモデル ID です。

## <a name="voice-bots"></a>音声ボット

開発者は音声出力によってアプリケーションを強化できます。 Speech Service はさまざまな[言語](supported-languages.md)の音声を合成することができ、その能力にアクセス、またその能力をアプリに追加するための[エンドポイント](rest-apis.md)を提供します。

さらに、個性やユニークさをボットに加えたいユーザーのために、Speech Service では開発者がユニークな音声フォントをカスタマイズできます。 音声認識モデルのカスタマイズと同様、音声フォントにはユーザー データが必要です。 開発者はそのデータを[音声適応ポータル](https://customspeech.ai)にアップロードし、ボット用音声のユニークなブランドの構築を開始します。 詳細については、[こちら](how-to-text-to-speech.md)および [FAQ](faq-text-to-speech.md) ページで説明しています。 

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [Speech SDK の基本](speech-sdk.md)
