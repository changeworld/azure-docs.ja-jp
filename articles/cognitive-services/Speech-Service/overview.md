---
title: Speech Service (プレビュー) とは
description: Microsoft の Cognitive Services の一部である Speech Service では、これまで別々に提供されていた複数の Azure 音声サービス (Bing Speech (音声認識とテキスト読み上げで構成)、Custom Speech、Speech Translation) が統合されています。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "41937534"
---
# <a name="what-is-the-speech-service-preview"></a>Speech Service (プレビュー) とは

1 つのサブスクリプションで利用できる Speech Service により、開発者はアプリケーションに強力な音声認識機能を簡単に追加できます。 アプリでは、音声コマンド、文字起こし、ディクテーション、音声合成、音声翻訳の各機能を備えることができるようになります。

Speech Service は、Cortana や Microsoft Office などの他の Microsoft 製品で使用されているテクノロジを備えています。

> [!NOTE]
> 現在、Speech Service はパブリック プレビュー段階にあります。 このページを定期的に参照して、ドキュメント、追加コード サンプルなどの最新情報を確認してください。

## <a name="speech-service-features"></a>Speech Service の機能

|関数|説明|
|-|-|
|[音声テキスト変換](speech-to-text.md)| アプリケーションが入力として受け取れるテキストにオーディオ ストリームが変換されます。 また、[Language Understanding Service](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) と統合すると、発話からユーザーの意図を導き出すことができます。|
|[テキスト読み上げ](text-to-speech.md)| プレーン テキストが自然な音声に変換され、オーディオ ファイルでアプリケーションに提供されます。 サポートされている多数の言語で、性別やアクセントが異なる複数の音声を利用できます。 |
|[音声翻訳](speech-translation.md)| ストリーミング オーディオをほぼリアルタイムで翻訳したり、録音された音声を処理したりできます。 |
|カスタム音声テキスト変換|独自の[音響](how-to-customize-acoustic-models.md)モデルと[言語](how-to-customize-language-model.md)モデルを作成し、カスタムの[発音](how-to-customize-pronunciation.md)ルールを指定することで、音声テキスト変換をカスタマイズできます。 |
|[カスタム テキスト読み上げ](how-to-customize-voice-font.md)|テキスト読み上げ用の独自の音声を作成できます。|
|[Speech Devices SDK](speech-devices-sdk.md)| 統合 Speech Service の導入に伴い、Microsoft とパートナーは、音声認識デバイスの開発に最適化された統合ハードウェア/ソフトウェア プラットフォームを提供しています。 |

## <a name="access-to-the-speech-service"></a>Speech Service へのアクセス

Speech Service は 2 つの方法で利用できます。 [SDK](speech-sdk.md) は、サポートされているプラットフォームで簡単に開発できるように、ネットワーク プロトコルの詳細を抽象化します。 [REST API](rest-apis.md) は、どのプログラミング言語でも動作しますが、SDK が提供するすべての機能を提供するわけではありません。

|<br>方法|音声<br>テキスト変換|テキストから<br>音声|音声<br>翻訳|<br>説明|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|[はい]|いいえ |[はい]|特定のプログラミング言語用のライブラリは、開発を簡略化する Websocket ベースのプロトコルを利用します。|
|[REST](rest-apis.md)|[はい]|[はい]|いいえ |アプリケーションに音声を簡単に追加できる HTTP ベースの単純な API。|

## <a name="speech-scenarios"></a>音声のシナリオ

音声テクノロジのいくつかの一般的な使用法について、以下で簡単に説明します。 [Speech SDK](speech-sdk.md) は、これらのシナリオのほとんどで中心的な役割を果たします。

> [!div class="checklist"]
> * 音声によってトリガーされるアプリを作成する
> * コール センターの録音を文字起こしする
> * 音声ボットを実装する

### <a name="voice-triggered-apps"></a>音声によってトリガーされるアプリ

音声入力は、アプリを柔軟にハンズフリーですぐに使えるようにするための優れた方法です。 音声対応アプリでは、ユーザーは質問するだけで必要な情報を得られます。クリックやタップで情報に移動する必要はありません。

アプリが一般の人を対象にしている場合、Speech Service によって提供されるベースライン音声認識モデルを使用できます。 これは、一般的な環境でさまざまな話者を認識するのに適します。

アプリを特定の領域 (たとえば、医薬または IT) で使用する場合、[言語モデル](how-to-customize-language-model.md)を作成して、アプリで使用する特別な用語について Speech Service に学習させることができます。

アプリが工場のようなノイズの多い環境で使用される場合、カスタム[音響モデル](how-to-customize-acoustic-models.md)を作成して、Speech Service が音声とノイズをより区別しやすくすることができます。

始めるのは容易で、[Speech SDK ](speech-sdk.md) をダウンロードし、関連する[クイック スタート](quickstart-csharp-dotnet-windows.md)の記事に従うだけです。

### <a name="transcribe-call-center-recordings"></a>コール センターの録音を文字起こしする

多くの場合、コール センターの録音は、通話で問題が発生した場合にのみ確認されます。 Speech Service を使用すると、すべての録音をテキストに簡単に書き起こすことができます。 テキストに書き起こせば、[全文検索](https://docs.microsoft.com/azure/search/search-what-is-azure-search)用にインデックスを付けることや、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) を適用して感情、言語、キー フレーズを検出することは簡単です。

コール センターの録音に専門用語 (製品名や IT 専門用語など) が含まれることが多い場合は、[言語モデル](how-to-customize-language-model.md)を作成して Speech Service にそのボキャブラリを学習させることができます。 カスタム[音響モデル](how-to-customize-acoustic-models.md)は、電話接続が最善ではなくても Speech Service が解釈できるようにする上で役立ちます。

このシナリオの詳細については、Speech Service での「[バッチ文字起こし](batch-transcription.md)」を参照してください。

### <a name="voice-bots"></a>音声ボット

[ボット](https://dev.botframework.com/)は、ユーザーを必要な情報に結び付ける方法、また顧客を好みのビジネスに結び付ける方法として、ますます普及しています。 会話型ユーザー インターフェイスを Web サイトまたはアプリに追加すると、その機能を簡単に見つけてすばやくアクセスできるようになります。 Speech Service を使用して、発話された質問に対して音声合成で実際に応答すれば、この会話は新たな様相を帯びます。

音声対応ボットに独自性を追加する (およびブランドを強化する) ために、音声を独自のものにできます。 カスタム音声は 2 段階のプロセスで作成します。 まず、使用する音声を[録音します](record-custom-voice-samples.md)。 次に、Speech Service の[音声カスタマイズ ポータル](https://cris.ai/Home/CustomVoice)に (テキスト トランスクリプトと共に) [これらの録音を送信します](how-to-customize-voice-font.md)。後の処理はポータルで行われます。 カスタム音声を作成したら、アプリで簡単に使用できます。

## <a name="next-steps"></a>次の手順

Speech Service のサブスクリプション キーを取得します。

> [!div class="nextstepaction"]
> [Speech Service を無料で試す](get-started.md)
