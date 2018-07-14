---
title: Azure 上の Custom Speech Service の概要 | Microsoft Docs
description: Custom Speech Service にサブスクライブし、サービス アクティビティを Azure サブスクリプションにリンクしてモデルをトレーニングし、展開を実行します。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fe7a140f5ba2d712014f03663a88d516958d188e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373221"
---
# <a name="get-started-with-custom-speech-service"></a>Custom Speech Service の概要

Custom Speech Service の主な機能を確認し、アプリケーションのニーズに合わせて音響モデルと言語モデルを構築、展開、および使用する方法について説明します。 Custom Speech Service ポータルにサインアップすると、より広範なドキュメントと手順の説明を参照できます。

## <a name="samples"></a>サンプル  
Microsoft が提供しているお勧めの基本的なサンプルについては、[こちら](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)を参照してください。

## <a name="prerequisites"></a>前提条件  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Custom Speech Service にサブスクライブし、サブスクリプション キーを取得する
上記の例を再現する前に、Custom Speech Service にサブスクライブし、サブスクリプション キーを取得する必要があります。[サブスクリプション](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech)に関するページを参照するか、[こちら](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md)の説明に従ってください。 プライマリ キーとセカンダリ キーの両方をこのチュートリアルで利用できます。 必ず、お使いの API を秘密かつ安全にしておくためのベスト プラクティスに従ってください。

### <a name="get-the-client-library-and-example"></a>クライアント ライブラリと例を入手する
[SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk) を介してクライアント ライブラリとサンプルをダウンロードできます。 ダウンロードした ZIP ファイルは、任意のフォルダーに抽出する必要があります、多くのユーザーは Visual Studio 2015 フォルダーを選択します。

## <a name="creating-a-custom-acoustic-model"></a>カスタム音響モデルを作成する
特定のドメインの音響モデルをカスタマイズするには、音声データのコレクションが必要です。 このコレクションは、一連の音声認識データのオーディオ ファイルと、各オーディオ ファイルの文字起こしのテキスト ファイルで構成されます。 オーディオ データは、認識エンジンを使用する典型的なシナリオのものである必要があります。

例: ノイズが多い工場環境での音声認識精度を高めるには、オーディオ ファイルがノイズの多い工場での人の会話で構成されている必要があります。
1 人の話者に対するパフォーマンスを最適化する場合は (たとえば、フランクリン・ルーズベルトの炉辺談話をすべて文字起こしする場合など)、オーディオ ファイルはその話者だけの多くの例で構成される必要があります。

カスタム音響モデルの作成方法の詳細については、[こちら](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md)を参照してください。

## <a name="creating-a-custom-language-model"></a>カスタム言語モデルを作成する
カスタム言語モデルの作成手順は、音声データがなく、テキストのみである点を除き、音響モデルの作成手順と似ています。 テキストは、ユーザーが話すと予想した、またはユーザーがアプリケーションで話した (または入力した) 内容を記録したクエリや発話の多数の例から構成される必要があります。

カスタム言語モデルの作成方法の詳細については、[こちら](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md)を参照してください。

## <a name="creating-a-custom-speech-to-text-endpoint"></a>カスタム Speech-to-Text エンドポイントを作成する
カスタム音響モデルと言語モデルの両方または一方を作成したら、カスタム Speech-to-Text エンドポイントに展開できます。 新しいカスタム エンドポイントを作成するには、ページの上部にある [CRIS] メニューから [Deployments]\(展開\) をクリックします。 現在のカスタム エンドポイントの "展開" というテーブルが表示されます。 まだエンドポイントを作成していない場合、テーブルは空です。 現在のロケールがテーブル タイトルに反映されます。 別の言語の展開を作成する場合は、[Change Locale]\(ロケールの変更\) をクリックします。 サポートされている言語に関する追加情報については、ロケールの変更に関するセクションを参照してください。

カスタム speech-text エンドポイントの作成方法の詳細については、[こちら](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md)を参照してください。

## <a name="using-a-custom-speech-endpoint"></a>Custom Speech エンドポイントを使用する
要求は、既定の Microsoft Cognitive Services 音声エンドポイントとよく似た方法で CRIS Speech-to-Text エンドポイントに送信できます。 これらのエンドポイントは、Speech API の既定のエンドポイントと機能的には同じである点に注意してください。 そのため、Speech API のクライアント ライブラリまたは REST API を介して使用できる同じ機能を、カスタム エンドポイントでも使用できます。

カスタム Speech-to-Text エンドポイントの使用方法の詳細については、[こちら](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md)を参照してください。


CRIS を介して作成されたエンドポイントは、サブスクリプションが関連付けられているレベルによって、処理できる同時要求数が異なる点に注意してください。 上限を超える数の認識が要求されると、エラー コード 429 Too many requests (要求が多すぎます) が返されます。 詳細については、[価格情報](https://www.microsoft.com/cognitive-services/en-us/pricing)を参照してください。 さらに、無料レベルには、要求の毎月のクォータがあります。 無料レベルで毎月のクォータを超えてエンドポイントにアクセスすると、サービスはエラー コード 403 Forbidden (禁止) を返します。

このサービスは、音声がリアルタイムで送信されることを前提としています。 より速く送信された場合、リアルタイムの時間が経過するまで、要求は実行中と見なされます。

* [概要](cognitive-services-custom-speech-home.md)
* [FAQ](cognitive-services-custom-speech-faq.md)
* [用語集](cognitive-services-custom-speech-glossary.md)
