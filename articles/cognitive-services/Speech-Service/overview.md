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
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445636"
---
# <a name="what-is-the-speech-service-preview"></a>Speech Service (プレビュー) とは

Speech Service は、Cortana や Microsoft Office などの他の Microsoft 製品で使用されているテクノロジを備えています。  この同じサービスは Cognitive Service としてすべての顧客が利用できます。 

> [!NOTE]
> 現在、Speech Service はパブリック プレビュー段階にあります。 このページを定期的に参照して、ドキュメント、追加コード サンプルなどの最新情報を確認してください。

1 つのサブスクリプションで利用できる Speech Service により、開発者はアプリケーションに強力な音声認識機能を簡単に提供できます。 アプリでは、音声コマンド、文字起こし、ディクテーション、音声合成、翻訳の各機能を備えることができるようになります。

## <a name="speech-service-features"></a>Speech Service の機能

|関数|説明|
|-|-|
|[音声テキスト変換](speech-to-text.md)| アプリケーションが入力として受け取れるテキストにオーディオ ストリームが変換されます。 また、[Language Understanding Service](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) と統合すると、発話からユーザーの意図を導き出すことができます。|
|[テキスト読み上げ](text-to-speech.md)| プレーン テキストが自然な音声に変換され、オーディオ ファイルでアプリケーションに提供されます。 サポートされている多数の言語で、性別やアクセントが異なる複数の音声を利用できます。 |
|[音声翻訳](speech-translation.md)| ストリーミング オーディオをほぼリアルタイムで翻訳したり、録音された音声を処理したりできます。 |
|[Speech Devices SDK](speech-devices-sdk.md)| 統合 Speech Service の導入に伴い、Microsoft とパートナーは、音声認識デバイスの開発に最適化された統合ハードウェア/ソフトウェア プラットフォームを提供しています。 |

## <a name="using-the-speech-service"></a>Speech Service の使用

Speech Service は 2 つの方法で利用できます。 [SDK](speech-sdk.md) は、ネットワーク プロトコルの詳細を抽象化します。 [REST API](rest-apis.md) は、どのプログラミング言語でも動作しますが、SDK が提供するすべての機能を提供するわけではありません。

|<br>方法|音声<br>テキスト変換|テキストから<br>音声|音声<br>翻訳|<br>説明|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|[はい]|いいえ |[はい]|開発を簡素化する、特定のプログラミング言語用のライブラリ。|
|[REST](rest-apis.md)|[はい]|[はい]|いいえ |アプリケーションに音声を簡単に追加できる HTTP ベースの単純な API。|

## <a name="next-steps"></a>次の手順

Speech Service の無料試用版サブスクリプション キーを取得します。

> [!div class="nextstepaction"]
> [Speech Service を無料で試す](get-started.md)
