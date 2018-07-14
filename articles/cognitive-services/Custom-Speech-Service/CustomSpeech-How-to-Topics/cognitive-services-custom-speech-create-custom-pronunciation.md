---
title: Azure 上の Custom Speech Service でカスタムの発音を使用する | Microsoft Docs
description: Cognitive Services の Custom Speech Service を使用して言語モデルを作成する方法について説明します。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: panosper
ms.openlocfilehash: a74b69b84cc80809a25f18b580a18abb5721b8b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373141"
---
# <a name="enable-custom-pronunciation"></a>カスタムの発音を有効にする
カスタムの発音を使用すると、発音形式と単語または用語の表示を定義することができます。 製品名や頭字語などのカスタマイズされた用語を処理する場合に便利です。 必要なのは、発音ファイル (単純な .txt ファイル) のみです。

しくみは次のとおりです。 1 つの .txt ファイルにカスタムの発音エントリを複数入力できます。 構造は次のとおりです。

```
Display form <Tab> Spoken form <Newline>
```

*例*:

| 表示フォーム | 音声フォーム |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>音声フォームの要件
音声フォームは小文字にする必要があります (これはインポート時に強制することができます)。 また、データ インポーターでチェックを提供する必要があります。 音声フォームまたは表示フォームにタブを使用することはできません。 ただし、表示フォームでは、他にも禁止文字が存在する可能性があります (たとえば、? や ^)。

各 .txt ファイルには複数のエントリを含めることができます。 たとえば、次のスクリーンショットを見てください。

![頭字語の発音のエントリがいくつか記載されたメモ帳のスクリーンショット](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

音声フォームは、表示フォームの音声シーケンスです。 文字、単語、または音節で構成されています。 現時点では、音声フォームの編成に役立つその他のガイダンスや一連の標準はありません。 

## <a name="supported-pronunciation-characters"></a>サポートされている発音文字
現在、カスタムの発音は、英語 (en-US) とドイツ語 (de-de) でサポートされています。 (カスタムの発音ファイル内の) 用語の音声フォームを表現するために使用できる文字セットを次の表に示します。 

| 言語 | 文字 |
|---------- |----------|
| 英語 (en-Us) | a、b、c、d、e、f、g、h、i、j、k、l、o、p、q、r、s、t、u、v、w、x、y、z |
| ドイツ語 (de-DE) | ä、ö、ü、ẞ、a、b、c、d、e、f、g、h、i、j、k、l、o、p、q、r、s、t、u、v、w、x、y、z |

>[注] 用語の表示フォーム (発音ファイル内) は、言語適応データ セットと同じ方法で記述する必要があります。

## <a name="requirements-for-the-display-form"></a>表示フォームの要件
表示フォームにすることができるのは、既存の単語を組み合わせたカスタムの単語、用語、頭字語、または複合語のみです。 また、一般的な単語の代替となる発音を入力することもできます。 

>[!NOTE]
この機能を使用して一般的な単語を編成し直したり、音声フォームを変更したりすることはお勧めしません。 一部の通常ではない単語 (略語、専門用語、外来語など) が正しくデコードされていないかどうかを確認するには、デコーダーを実行することをお勧めします。 正しくデコードされている場合は、カスタムの発音ファイルに追加します。 言語モデルでは、常に単語の表示フォームのみを使用する必要があります。 

## <a name="requirements-for-the-file-size"></a>ファイル サイズの要件
発音エントリを含む .txt ファイルのサイズは 1 MB に制限されています。 通常、このファイルを介して大量のデータをアップロードする必要はありません。 多くの場合、カスタムの発音ファイルは数 KB 単位のサイズです。 すべてのロケールの .txt ファイルは、UTF-8 BOM 形式でエンコードされている必要があります。 英語のロケールでは、ANSI も使用できます。

## <a name="next-steps"></a>次の手順
* [カスタムの音響モデル](cognitive-services-custom-speech-create-acoustic-model.md)を作成して、認識精度を向上します。
* [カスタム Speech-to-Text エンドポイントを作成](cognitive-services-custom-speech-create-endpoint.md)します。このエンドポイントは、アプリから使用できます。
