---
title: Text Translation REST API リファレンス ガイド
titleSuffix: Azure Cognitive Services
description: Text Translation REST API の一覧とリンクを示します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/10/2021
ms.author: lajanuar
ms.openlocfilehash: f3188f303d7e2945341267c9ea4598682e4d7964
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426249"
---
# <a name="text-translation-rest-api"></a>Text Translation REST API

Text Translation は、Azure Translator サービスのクラウドベースの機能であり、REST API の Azure Cognitive Services ファミリの一部です。 Text Translation API により、すべての[サポートされている言語と方言](../../language-support.md)の言語ペア間でテキストが翻訳されます。 利用可能なメソッドを次の表に示します。

| Request| Method| 説明|
|---------|--------------|---------|
| [**languages**](v3-0-languages.md) | **GET** | **翻訳**、**表記変換**、**辞書** のメソッドによって現在サポートされている言語のセットを返します。 この要求に認証ヘッダーは必要なく、サポートされている言語セットを表示するために Translator リソースは必要ありません。|
|[**translate**](v3-0-translate.md) | **POST**| 指定されたソース言語のテキストを、ターゲット言語のテキストに翻訳します。|
|[**transliterate**](v3-0-transliterate.md) |  **POST** | ソース言語のスクリプトまたはアルファベットを、ターゲット言語のスクリプトまたはアルファベットにマップします。
|[**detect**](v3-0-detect.md) | **POST** | ソース言語を識別します。 |
|[**breakSentence**](v3-0-break-sentence.md) | **POST** | ソース テキスト内の文の長さを表す整数の配列を返します。 |
| [**dictionary/lookup**](v3-0-dictionary-lookup.md) | **POST** | 単一の単語翻訳に対する代替語を返します。 |
| [**dictionary/examples**](v3-0-dictionary-lookup.md) | **POST** | 用語がコンテキストでどのように使用されるかを返します。 |

> [!div class="nextstepaction"]
> [Azure portal で Translator リソースを作成する](../translator-how-to-signup.md)

> [!div class="nextstepaction"]
> [クイックスタート: REST API とプログラミング言語](../quickstart-translator.md)
