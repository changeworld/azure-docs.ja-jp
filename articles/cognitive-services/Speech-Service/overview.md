---
title: Speech Service (プレビュー) とは | Microsoft Docs
description: Microsoft の Cognitive Services の一部である Speech Service では、これまで別々に提供されていた複数の Azure 音声サービス (Bing Speech (音声認識とテキスト読み上げで構成)、Custom Speech、Speech Translation) が統合されています。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: e7c09eee1634c52e78a523a7cc65641ea99f23e6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "35378871"
---
# <a name="what-is-the-speech-service-preview"></a>Speech Service (プレビュー) とは

Microsoft の Cognitive Services の一部である Speech Service では、これまで別々に提供されていた複数の Azure 音声サービス (Bing Speech (音声認識とテキスト読み上げで構成)、Custom Speech、Speech Translation) が統合されています。 前身サービスと同様に、Speech Service は、Cortana や Microsoft Office などの他の Microsoft 製品で使用されているテクノロジを備えています。

> [!NOTE]
> 現在、Speech Service はパブリック プレビュー段階にあります。 このページを定期的に参照して、ドキュメント、追加コード サンプルなどの最新情報を確認してください。

1 つのサブスクリプションで利用できる統合 Speech Service により、開発者はアプリケーションに強力な音声認識機能を簡単に提供できます。 アプリでは、音声コマンド、文字起こし、ディクテーション、音声合成、翻訳の各機能を備えることができるようになります。

|関数|説明|
|-|-|
|音声テキスト変換|人間の連続音声を、アプリケーションへの入力として使用できるテキストに変換します。 [Language Understanding Service](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) と統合すると、発話からユーザーの意図を導き出すことができます。|
|テキストから音声へ|テキストを自然な合成音声のオーディオ ファイルに変換します。|
|音声&nbsp;翻訳|音声を他の言語に翻訳し、テキストまたは音声で出力します。|

## <a name="using-the-speech-service"></a>Speech Service の使用

Speech Service は 2 つの方法で利用できます。 [SDK](speech-sdk.md) は、ネットワーク プロトコルの詳細を抽象化します。 [REST API](rest-apis.md) は、どのプログラミング言語でも動作しますが、SDK が提供するすべての機能を提供するわけではありません。

|<br>方法|音声<br>テキスト変換|テキストから<br>音声|音声<br>翻訳|<br>説明|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|[はい]|いいえ |[はい]|開発を簡素化する、特定のプログラミング言語用のライブラリ。|
|[REST](rest-apis.md)|[はい]|[はい]|いいえ |アプリケーションに音声を簡単に追加できる HTTP ベースの単純な API。|

## <a name="speech-to-text"></a>音声テキスト変換

[Speech to Text](speech-to-text.md) (STT) (音声認識) API により、オーディオ ストリームが書き起こされ、アプリケーションが入力として受け取ることができるテキストに変換されます。 アプリケーションでは、そのテキストをドキュメントに入力したり、コマンドとして認識したりできます。

音声テキスト変換は、対話、会話、ディクテーションの各シナリオ用に個別に最適化されています。 Speech to Text API の一般的なユース ケースを次に示します。 

* 中間結果なしで短い音声 (コマンドなど) を認識する
* 以前に録音された長い音声 (ボイスメール メッセージなど) を書き起こす
* ディクテーションを目的として、部分的な結果と共にストリーミング音声をリアルタイムで書き起こす
* 発声された自然言語の要求に基づいて、ユーザーが実行したいことを判断する

Speech to Text API では、リアルタイムの連続認識と中間結果で対話型の音声文字起こしがサポートされています。 また、発話の終了の検出、オプションの自動大文字化および句読点挿入、不適切な言葉のマスク、テキストの正規化もサポートされています。

音声テキスト変換の音響モデルと言語モデルをカスタマイズして、特殊なボキャブラリ、騒がしい環境、さまざまな話し方に対応できます。

## <a name="text-to-speech"></a>テキストから音声へ

[Text to Speech](text-to-speech.md) (TTS) (音声合成) API により、プレーン テキストが自然な音声に変換され、オーディオ ファイルでアプリケーションに提供されます。 サポートされている多数の言語で、性別やアクセントが異なる複数の音声を利用できます。

この API では、音声合成マークアップ言語 (SSML) タグがサポートされているので、単語が難しい場合も正確な発音を指定できます。 SSML では、テキストで音声の特徴 (強勢、速度、音量、性別、ピッチなど) を指示することもできます。

Text to Speech API の一般的なユース ケースを次に示します。

* 視覚に障碍があるユーザー向けの代替画面出力としての音声出力
* ナビゲーションなどの車内アプリケーションの音声指示
* Speech to Text API と連携した会話型ユーザー インターフェイス

サポートされていない言語が必要な場合や、独自の音声をアプリケーションで使用する場合、Text to Speech API ではカスタム音声モデルがサポートされています。

## <a name="speech-translation"></a>音声翻訳

[Speech Translation](speech-translation.md) API を使用して、ストリーミング オーディオのほぼリアルタイムでの翻訳または録音済みの音声の処理を行うことができます。 ストリーミング翻訳では、翻訳の進行状況を示すためにユーザーに表示できる中間結果が返されます。 結果はテキストと音声のいずれかで返されます。

音声翻訳のユース ケースを次に示します。

* 旅行者向けの "会話" 翻訳モバイル アプリまたはデバイスを実装する 
* 音声録音およびビデオ録画の字幕作成の自動翻訳を提供する

## <a name="speech-devices-sdk"></a>Speech Devices SDK

統合 Speech Service の導入に伴い、Microsoft とパートナーは、音声認識デバイスの開発に最適化された統合ハードウェア/ソフトウェア プラットフォームである、[Speech Devices SDK](speech-devices-sdk.md) を提供しています。 この SDK は、あらゆる種類のアプリケーションに対応するスマート音声デバイスの開発に適しています。

Speech Devices SDK では、ウェイク ワードをカスタマイズして独自のアンビエント デバイスを構築できるため、オーディオ キャプチャをトリガーするキューがブランド固有のものになります。 さらに、ノイズの抑制、遠距離の音声、ビームフォーミングなど、マルチチャンネル ソースに対して高度なオーディオ処理が行われ、より正確な音声認識が実現されます。

## <a name="next-steps"></a>次の手順

Speech Service の無料試用版サブスクリプション キーを取得します。

> [!div class="nextstepaction"]
> [Speech Service を無料で試す](get-started.md)
