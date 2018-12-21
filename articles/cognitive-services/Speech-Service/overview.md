---
title: Speech Service とは
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の一部である Speech Service は、以前は個別に使用できた次のような複数の音声サービスを 1 つにまとめたものです(Bing Speech (音声認識とテキスト読み上げを構成)、Custom Speech、Speech Translation)。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: e86adfd4e832e6b9514e4813ddd4a942b07ca624
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336596"
---
# <a name="what-is-speech-services"></a>Speech Service とは

他の Azure 音声サービスと同じように、Speech Service では、Cortana や Microsoft Office などの製品で使用されている音声認識テクノロジが利用されています。

Speech Service では、以前は [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home)、[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/)、[Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home)、および [Custom Voice](http://customvoice.ai/) の各サービスで利用可能であった Azure 音声機能が統合されています。 現在は、1 つのサブスクリプションで、これらすべての機能にアクセスできます。

## <a name="main-speech-services-functions"></a>Speech Service の主要機能

Speech Service の主要機能には、音声テキスト変換 (音声認識また文字起こしとも呼ばれます)、テキスト読み上げ (音声合成)、および音声翻訳があります。

|関数|機能|
|-|-|
|[音声テキスト変換](speech-to-text.md)| <li>連続するリアルタイムの音声をテキストに書き起こします。<li>音声録音から音声を一括で文字起こしできます。 <li>中間結果、音声の終わりの検出、テキストの自動的な書式設定、および不適切な表現のマスキングをサポートします。 <li>[Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) を呼び出して、文字起こしされた音声からユーザーの意図を導き出すことができます。\*|
|[Text-to-Speech](text-to-speech.md)| <li>**新規**:人の音声 (英語) とほとんど区別できないニューラル テキスト読み上げ音声を提供します。 <li>テキストを自然な音声に変換します。 <li>複数の性別やサポートされている多くの言語の方言が用意されています。 <li>プレーンテキストの入力または音声合成マークアップ言語 (SSML) をサポートします。 |
|[音声翻訳](speech-translation.md)| <li>ストリーミング オーディオをほぼリアルタイムで翻訳します。<li> 録音された音声も処理できます。<li>テキストまたは合成された音声として結果を提供します。 |


## <a name="customize-speech-features"></a>音声機能のカスタマイズ

Speech Service の音声テキスト変換機能とテキスト読み上げ機能の基になるモデルを、独自のデータを使用してトレーニングできます。

|機能|モデル|目的|
|-|-|-|
|音声テキスト変換|[音響モデル](how-to-customize-acoustic-models.md)|特定の話者や環境 (社内や工場内など) で文字起こしする際に有用です。|
||[言語モデル](how-to-customize-language-model.md)|フィールド固有の語彙や文法 (医療や IT の専門用語など) で文字起こしする際に有用です。|
||[発音モデル](how-to-customize-pronunciation.md)|略語と頭字語 (たとえば "IOU" を "I owe you" と発音) を文字起こしする際に有用です。 |
|テキスト読み上げ|[音声フォント](how-to-customize-voice-font.md)|人間の音声サンプルでモデルをトレーニングして、アプリに独自の音声を与えます。|

アプリの音声テキスト変換機能またはテキスト読み上げ機能で標準モデルを使用するすべての場所で、カスタム モデルを使用できます。

## <a name="use-the-speech-service"></a>Speech Service の使用

Microsoft では、音声対応アプリケーションの開発を簡素化するために、Speech Service で使用する [Speech SDK](speech-sdk.md) を提供しています。 Speech SDK には、一貫性のあるネイティブな C#、C++、および Java 用の Speech to Text API と Speech Translation API が提供されています。 これらの言語のいずれかで開発する場合、Speech SDK によってネットワークの詳細が処理されるため、開発が容易になります。

Speech Service には、HTTP 要求を実行できるプログラミング言語で動作する [REST API](rest-apis.md) も含まれています。 REST インターフェイスでは、SDK のストリーミングのリアルタイム機能は提供されません。

|<br>方法|音声<br>テキスト変換|テキストから<br>音声|音声<br>翻訳|<br>説明|
|-|-|-|-|-|
|[Speech SDK](speech-sdk.md)|[はい]|いいえ |[はい]|C#、C++、および Java 用のネイティブ API による開発の簡略化。|
|[REST API](rest-apis.md)|[はい]|[はい]|いいえ |アプリケーションに音声を簡単に追加できる HTTP ベースの単純な API。|

### <a name="websockets"></a>WebSocket

Speech Service では、音声テキスト変換と音声翻訳をストリーミングするための Websocket プロトコルもサポートされています。 Speech SDK は、これらのプロトコルを使用して、Speech Service と通信します。 Speech Service との独自の Websocket 通信を実装する代わりに、Speech SDK を使用してください。

Websocket 経由で Bing Speech または Translator Speech を使用するコードが既に存在する場合は、Speech Service を使用するようにコードを更新できます。 WebSocket プロトコルには互換性がありますが、エンドポイントは異なります。

### <a name="speech-devices-sdk"></a>Speech Devices SDK

[Speech Devices SDK](speech-devices-sdk.md) は、音声対応デバイスの開発者向けのハードウェアとソフトウェアの統合プラットフォームです。 Microsoft のハードウェア パートナーによって、参照設計と開発ユニットが提供されています。 Microsoft では、ハードウェアの機能を最大限に活用する、デバイスに最適化された SDK を提供しています。


## <a name="speech-scenarios"></a>音声のシナリオ

Speech Service のユース ケースには、以下が含まれます。

> [!div class="checklist"]
> * 音声によってトリガーされるアプリを作成する
> * コール センターの録音を文字起こしする
> * 音声ボットを実装する

### <a name="voice-user-interface"></a>音声ユーザー インターフェイス

音声入力は、アプリを柔軟にハンズフリーですぐに使えるようにするための優れた方法です。 音声対応アプリでは、ユーザーは質問するだけで必要な情報を得ることができます。

アプリが一般の人を対象にしている場合は、既定の音声認識モデルを使用できます。 一般的な環境でさまざまな話者を認識します。

医療や IT などの特定のドメインでアプリが使用される場合、[言語モデル](how-to-customize-language-model.md)を作成できます。 このモデルを使用して、アプリで使用される特別な用語を Speech Service に教えることができます。

工場などの雑音が多い環境でアプリを使用する場合、カスタムの[音響モデル](how-to-customize-acoustic-models.md)を作成できます。 このモデルは、Speech Service が雑音と音声を区別するのに役立ちます。

### <a name="call-center-transcription"></a>コール センターの文字起こし

多くの場合、コール センターの録音は、通話で問題が発生した場合にのみ確認されます。 Speech Service を使用すると、すべての録音をテキストに簡単に書き起こすことができます。 [全文検索](https://docs.microsoft.com/azure/search/search-what-is-azure-search)用にインデックスを付けることや、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) を適用して感情、言語、キー フレーズを検出することは簡単です。

コール センターの録音に専門用語 (製品名や IT 用語など) が含まれることが多い場合は、[言語モデル](how-to-customize-language-model.md)を作成して Speech Service にそのボキャブラリを学習させることができます。 カスタム[音響モデル](how-to-customize-acoustic-models.md)は、電話接続が最善ではなくても Speech Service が解釈できるようにする上で役立ちます。

このシナリオの詳細については、Speech Service での「[バッチ文字起こし](batch-transcription.md)」を参照してください。

### <a name="voice-bots"></a>音声ボット

[ボット](https://dev.botframework.com/)は、ユーザーを必要な情報に結び付ける方法、また顧客を好みのビジネスに結び付ける方法として普及しています。 会話型ユーザー インターフェイスを Web サイトまたはアプリに追加すると、その機能を簡単に見つけてすばやくアクセスできるようになります。 Speech Service を使用して、発話された質問を同じやり方で応答すれば、会話は新しい次元の雄弁さを獲得します。

音声対応ボットに独自性を追加するために、音声を独自のものにできます。 カスタム音声は 2 段階のプロセスで作成します。 まず、使用する音声を[録音します](record-custom-voice-samples.md)。 次に、Speech Service の[音声カスタマイズ ポータル](https://cris.ai/Home/CustomVoice)に (テキスト トランスクリプトと共に) [これらの録音を送信します](how-to-customize-voice-font.md)。後の処理はポータルで行われます。 カスタム音声を作成した後、アプリでそれを使用する手順は簡単です。

## <a name="next-steps"></a>次の手順

Speech Service のサブスクリプション キーを取得します。

> [!div class="nextstepaction"]
> [Speech Service を無料で試す](get-started.md)
