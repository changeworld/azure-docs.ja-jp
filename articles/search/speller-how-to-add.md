---
title: スペル チェックを追加する
titleSuffix: Azure Cognitive Search
description: クエリを実行する前に、クエリ パイプラインにスペル修正を追加して、クエリ用語の入力ミスを修正します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: references_regions
ms.openlocfilehash: 98aec7fc2a3857ac50125e7e49c5f9ab105a49d7
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535930"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Cognitive Search のクエリにスペル チェックを追加する

> [!IMPORTANT]
> スペル修正はパブリック プレビュー段階にあり、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下で提供されます。 Azure portal、プレビュー REST API からのみ利用できます。

個々の検索クエリ用語が検索エンジンに到達する前に修正することで、リコールを改善できます。 **スペル チェック** パラメーターは、[simple](query-simple-syntax.md)、 [full](query-lucene-syntax.md)、および現在パブリック プレビュー中の新しい [semantic](semantic-how-to-query-request.md) オプションのすべての種類のクエリでサポートされています。

スペル チェックは、[セマンティック検索プレビュー](semantic-search-overview.md)と一緒にリリースされ、queryLanguage パラメーターを共有しますが、それ以外においては、独自の前提条件を持つ独立した機能です。 この機能を使用するためにサインアップを行う必要はなく、追加料金もかかりません。

## <a name="prerequisites"></a>前提条件

スペル チェックを使用するには、次が必要です。

+ Basic レベル以上の検索サービス (リージョンは問わず)。

+ [サポートされている言語](#supported-languages)のコンテンツを含む既存の検索インデックス。

+ "speller=lexicon"、および "queryLanguage" が[サポートされている言語](#supported-languages)に設定されている[クエリ要求](/rest/api/searchservice/preview-api/search-documents)。 スペル チェックは、"search" パラメーターで渡された文字列に対して機能します。 フィルターに対してはサポートされていません。

クエリ要求でプレビュー API をサポートする検索クライアントを使用します。 REST の場合、[Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md)、または自分で変更したコードを使用して、プレビュー API への REST 呼び出しを行うことができます。 また、Azure SDK のベータ版リリースを使用することもできます。

| クライアント ライブラリ | バージョン |
|----------|----------|
| REST API | [2021-04-30-Preview](/rest/api/searchservice/index-preview) または 2020-06-30-Preview |
| Azure SDK for .NET | [バージョン 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) | 
| Azure SDK for Java |  [バージョン 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar) |
| Azure SDK for JavaScript | [バージョン 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2) |
| Azure SDK for Python | [バージョン 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

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

スペル修正は、テキスト分析が行われる個々のクエリ用語に対して行われます。そのため、一部の Lucene クエリではスペル チェック パラメーターを使用できますが、それ以外では使用できません。

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

queryLanguage の有効な値は、[サポートされる言語のリスト (REST API リファレンス)](/rest/api/searchservice/preview-api/search-documents#queryLanguage) からコピーされた以下の表に記載されています。

| Language | queryLanguage |
|----------|---------------|
| 英語 [EN] | EN、EN-US (既定値) |
| スペイン語 [ES] | ES、ES-ES (既定値)|
| フランス語 [FR] | FR、FR-FR (既定値) |
| ドイツ語 [DE] | DE、DE-DE (既定値) |
| オランダ語 [NL] | NL、NL-BE、NL-NL (既定値) |

### <a name="querylanguage-considerations"></a>queryLanguage の考慮事項

他の場所で述べた通り、クエリ要求の queryLanguage パラメーターは 1 つしかありませんが、そのパラメーターは複数の機能で共有されており、それぞれが異なる言語コーホートをサポートしています。 スペル チェックのみを使用している場合は、上の表にあるサポートされている言語のリストが完全なリストです。 

### <a name="language-analyzer-considerations"></a>言語アナライザーに関する考慮事項

英語以外のコンテンツを含むインデックスでは、英語以外のフィールドに対して[言語アナライザー](index-add-language-analyzers.md)を使用して、ネイティブ言語の言語規則を適用することがよくあります。

言語分析を行ったコンテンツにスペル チェックを追加する場合は、インデックス作成やクエリ処理のすべての手順で同じ言語を使用すると、より良い結果が得られます。 たとえば、フィールドのコンテンツに "fr.microsoft" 言語アナライザーを使用してインデックスが作成された場合、クエリ、スペル チェック、セマンティック キャプション、セマンティック回答はすべて、何らかの形式のフランス語の語彙や言語ライブラリを使用する必要があります。

Cognitive Search で言語ライブラリを使用する方法を要約するには

+ 言語アナライザーは、インデックス作成とクエリの実行中に呼び出すことができ、Apache Lucene ("de.lucene" など) または Microsoft ("de.microsoft") のいずれかになります。

+ スペル チェック中に呼び出される言語辞書は、上の表のいずれかの言語コードを使用して指定されます。

クエリ要求では、queryLanguage に割り当てられた値がスペル チェック、[回答](semantic-answers.md)、キャプションにも同様に適用されます。 

> [!NOTE]
> 言語アナライザーを使用している場合は、さまざまなプロパティ値間の言語の一貫性だけが問題になります。 言語に依存しないアナライザー (keyword、simple、standard、stop、whitespace、`standardasciifolding.lucene` など) を使用している場合は、queryLanguage 値は任意の値にすることができます。

検索インデックスのコンテンツは複数の言語で構成できますが、通常、クエリ入力は 1 つの言語で行われます。 検索エンジンでは、queryLanguage、言語アナライザー、およびコンテンツが構成されている言語の互換性が確認されません。そのため、誤った結果が生成されないように、適切にクエリのスコープを設定してください。

## <a name="next-steps"></a>次のステップ

+ [セマンティック ランク付けとキャプションの呼び出し](semantic-how-to-query-request.md)
+ [基本的なクエリの作成](search-query-create.md)
+ [完全な Lucene クエリ構文の使用](query-Lucene-syntax.md)
+ [単純なクエリ構文の使用](query-simple-syntax.md)
+ [セマンティック検索の概要](semantic-search-overview.md)