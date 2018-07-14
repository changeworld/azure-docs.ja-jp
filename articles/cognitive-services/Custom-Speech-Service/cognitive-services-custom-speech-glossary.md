---
title: Azure 上の Custom Speech Service の用語集 | Microsoft Docs
description: 用語集では、Custom Speech Service を使用する際に目にする専門用語が定義されます。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 2b6c54f023eccfa813817256bdcc1cbde83d49a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373120"
---
# <a name="glossary"></a>用語集

## <a name="a"></a>A

### <a name="acoustic-model"></a>音響モデル

音響モデルとは、音声の短い断片を特定の言語のいくつかの音素、つまり音声単位に分類する分類器です。 たとえば、“speech” という単語は “s p iy ch” という 4 つの音素で構成されています。 こうした分類は、1 秒あたり 100 回程度行われます。

## <a name="b"></a>b

## <a name="c"></a>C

### <a name="conversational-model"></a>会話モデル

会話形式での発話の認識に適したモデル。 通常、Microsoft Conversational AM は別の人に向けた音声に使用されます。

## <a name="d"></a>D

### <a name="deployment"></a>デプロイ

採用されたカスタム モデルがサービスになり、URI を公開するプロセス

## <a name="e"></a>E

## <a name="f"></a>F

## <a name="g"></a>G

## <a name="h"></a>H

## <a name="i"></a>I

### <a name="inverse-text-normalization"></a>テキストの逆正規化

書式のない "未加工" テキストを書式付きテキスト (大文字や句読点を含む) に戻すプロセスは、テキストの逆正規化 (ITN) と呼ばれます。

## <a name="j"></a>J

## <a name="k"></a>K

## <a name="l"></a>L

### <a name="language-model"></a>言語モデル

言語モデルは、一連の単語における確率分布を表すものです。 言語モデルにより、一連の単語の中から、単語系列自体の確率に基づいて音が似ている単語を選択できます。

## <a name="m"></a>M

## <a name="n"></a>N

### <a name="normalization"></a>正規化

正規化 (テキスト) : 結果のテキスト (文字起こし) を、システムで読み取れる標準の明確な形式に変換すること。

## <a name="o"></a>O

## <a name="p"></a>P

## <a name="q"></a>Q

## <a name="r"></a>R

## <a name="s"></a>S

### <a name="search-and-dictate-model"></a>検索およびディクテーション モデル

コマンドの処理に適した音響モデル。 Microsoft Search and Dictation LM は、コマンドなどアプリケーションやデバイスに向けて発せられる音声に適しています。

### <a name="subscription-key"></a>Subscription key

サブスクリプション キーは、すべての Custom Speech Service モデル を呼び出すために、クエリ文字列パラメーターとして指定する必要がある文字列です。 サブスクリプション キーは、[Azure Portal](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) で取得します。取得した後は、Custom Speech Service ポータルの [個人用サブスクリプション] で確認できます。

## <a name="t"></a>T

### <a name="transcription"></a>文字起こし

文字起こし: 音声 .wav ファイルを処理して生成されるテキスト

## <a name="u"></a>U

## <a name="v"></a>V

## <a name="w"></a>W

## <a name="x"></a>○

## <a name="y"></a>Y

## <a name="z"></a>Z

* [概要](cognitive-services-custom-speech-home.md)
* [作業の開始](cognitive-services-custom-speech-get-started.md)
* [FAQ](cognitive-services-custom-speech-faq.md)
