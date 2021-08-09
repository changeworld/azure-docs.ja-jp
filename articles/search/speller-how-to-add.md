---
title: スペル チェックを追加する
titleSuffix: Azure Cognitive Search
description: クエリを実行する前に、クエリ パイプラインにスペル修正を追加して、クエリ用語の入力ミスを修正します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 28dc63729a946e7b14b950f5082752d78c5992f4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110452686"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Cognitive Search のクエリにスペル チェックを追加する

> [!IMPORTANT]
> スペル修正はパブリック プレビュー段階にあり、プレビュー REST API を介してのみ利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。 最初のプレビュー期間中は、スペル チェックに対して料金は発生しません。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

個々の検索クエリ用語が検索エンジンに到達する前に修正することで、リコールを改善できます。 **スペル チェック** パラメーターは、[simple](query-simple-syntax.md)、 [full](query-lucene-syntax.md)、および現在パブリック プレビュー中の新しい [semantic](semantic-how-to-query-request.md) オプションのすべての種類のクエリでサポートされています。

## <a name="prerequisites"></a>前提条件

+ [サポートされている言語](#supported-languages)のコンテンツを含む既存の検索インデックス。 [[シノニム]](search-synonyms.md) では、現在、スペル修正は機能しません。 いずれのフィールド定義でも、シノニム マップを指定するインデックスでそれを使用しないでください。

+ クエリを送信するための検索クライアント

  検索クライアントは、クエリ要求でプレビューの REST API をサポートする必要があります。 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md)、または自分で変更したコードを使用して、プレビュー API への REST 呼び出しを行うことができます。

+ スペル修正を呼び出す[クエリ要求](/rest/api/searchservice/preview-api/search-documents)には、"api-version=2020-06-30-Preview"、"speller=lexicon"、および "queryLanguage" が[サポートされている言語](#supported-languages)に設定されている必要があります。

> [!Note]
> スペル チェック パラメーターは、セマンティック検索を提供するのと同じリージョン内のすべての階層で使用できます。 サインアップは必要ですが、料金は発生せず、階層の制限はありません。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

## <a name="spell-correction-with-simple-search"></a>単純な検索でのスペル修正

次の例では、組み込みの hotels-sample index を使用して、単純な自由形式のテキスト クエリのスペル修正を示します。 スペル修正を行わない場合、クエリは 0 件の結果を返します。 修正すると、このクエリでは Johnson 家族向けリゾートについて 1 つの結果が返されます。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>完全な Lucene でのスペル修正

スペル修正は、分析される個々のクエリ用語に対して行われます。一部の Lucene クエリではスペル チェック パラメーターを使用できますが、それ以外では使用できない理由です。

+ テキスト分析をバイパスする互換性のないクエリ形式には、ワイルドカード、正規表現、あいまいがあります
+ 互換性のあるクエリ形式には、フィールド検索、近接、用語ブーストがあります

この例では、完全な Lucene 構文とスペル ミスがあるクエリ用語を使用して、Category フィールドに対して検索を使用します。 スペル チェックを含めると、"Suiite" の入力ミスが修正され、クエリが成功します。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>セマンティック検索でのスペル修正

このクエリでは、1 つを除くすべての用語に入力ミスがあるため、関連する結果を返すためのスペル修正が行われます。 詳細については、 [セマンティック クエリの作成](semantic-how-to-query-request.md)に関するページを参照してください。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="supported-languages"></a>サポートされる言語

queryLanguage のスペル チェックの有効な値については、次の表を参照してください。 この一覧は、[サポートされている言語 (REST API リファレンス)](/rest/api/searchservice/preview-api/search-documents#queryLanguage) のサブセットです。 スペル チェックなしでセマンティック キャプションと回答を使用している場合は、言語とバリアントのより大きな一覧から選択できます。

| Language | queryLanguage |
|----------|---------------|
| 英語 [EN] | EN、EN-US (既定値) |
| スペイン語 [ES] | ES、ES-ES (既定値)|
| フランス語 [FR] | FR、FR-FR (既定値) |
| ドイツ語 [DE] | DE、DE-DE (既定値) |

## <a name="language-considerations"></a>言語に関する注意点

スペル チェックに必要な queryLanguage パラメーターは、インデックスのフィールド定義に割り当てられている[言語アナライザー](index-add-language-analyzers.md)と一致している必要があります。 たとえば、フィールドのコンテンツに "fr.microsoft" 言語アナライザーを使用してインデックスが作成された場合、クエリ、スペル チェック、セマンティック キャプション、セマンティック回答はすべて、何らかの形式のフランス語の言語ライブラリを使用する必要があります。

Cognitive Search で言語ライブラリを使用する方法を要約するには

+ 言語アナライザーは、インデックス作成とクエリの実行中に呼び出すことができ、完全な Lucene ("de.lucene" など) または Microsoft ("de.microsoft") のいずれかになります。

+ スペル チェック中に呼び出される言語辞書は、上の表のいずれかの言語コードを使用して指定されます。

クエリ要求では、queryLanguage がスペル チェック、[回答](semantic-answers.md)、キャプションにも同様に適用されます。 セマンティック応答の個々の部分に対するオーバーライドはありません。 

> [!NOTE]
> 言語アナライザーを使用している場合は、さまざまなプロパティ値間の言語の一貫性だけが問題になります。 言語に依存しないアナライザー (keyword、simple、standard、stop、whitespace、`standardasciifolding.lucene` など) を使用している場合は、queryLanguage 値は任意の値にすることができます。

検索インデックスのコンテンツは複数の言語で構成できますが、通常、クエリ入力は 1 つの言語で行われます。 検索エンジンでは、queryLanguage、言語アナライザー、およびコンテンツが構成されている言語の互換性が確認されません。そのため、誤った結果が生成されないように、適切にクエリのスコープを設定してください。

## <a name="next-steps"></a>次のステップ

+ [セマンティック ランク付けとキャプションの呼び出し](semantic-how-to-query-request.md)
+ [基本的なクエリの作成](search-query-create.md)
+ [完全な Lucene クエリ構文の使用](query-Lucene-syntax.md)
+ [単純なクエリ構文の使用](query-simple-syntax.md)
+ [セマンティック検索の概要](semantic-search-overview.md)