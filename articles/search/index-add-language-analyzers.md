---
title: 言語アナライザーを文字列フィールドに追加する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search での英語以外のクエリおよびインデックスのための多言語字句テキスト解析について説明します。
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539401"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Azure Cognitive Search インデックスの文字列フィールドに言語アナライザーを追加する

"*言語アナライザー*" は、[テキスト アナライザー](search-analyzers.md)の固有の種類であり、対象言語の言語規則を使用して字句解析を実行します。 すべての検索可能フィールドには、**analyzer** プロパティがあります。 インデックスに翻訳された文字列が含まれる場合 (英語と中国語のテキストが別のフィールドになっている場合など)、各フィールドで言語アナライザーを指定して、これらのアナライザーの豊富な言語機能にアクセスできます。  

Azure Cognitive Search では、Lucene によって提供される 35 個のアナライザーと、Office および Bing で使用されるマイクロソフト独自の自然言語処理技術によって提供される 50 個のアナライザーがサポートされています。

## <a name="comparing-analyzers"></a>アナライザーの比較

開発者によっては、使い慣れた簡単なオープン ソース ソリューションである Lucene を好む場合があります。 Lucene の言語アナライザーは高速です。しかし、マイクロソフトのアナライザーには高度な機能があります。たとえば、レンマ化、単語複混合化 (ドイツ語、デンマーク語、オランダ語、スウェーデン語、ノルウェー語、エストニア語、フィンランド語、ハンガリー語、スロバキア語などの言語で)、エンティティ認識 (URL、電子メール、日付、数値) などです。 可能であれば、マイクロソフトと Lucene の両方のアナライザーを比較し、より適したものを選んでください。 

言語によっては、マイクロソフトのアナライザーでのインデックス作成には、平均して、Lucene の同等機能の 2 ～ 3 倍の時間がかかります。 平均的なサイズのクエリの場合、検索のパフォーマンスはさほど影響はありません。 

### <a name="english-analyzers"></a>英語のアナライザー

既定のアナライザーは標準 Lucene であり、英語でも問題なく動作しますが、おそらく Lucene の英語アナライザーや Microsoft の英語アナライザーほどではありません。 
 
+ Lucene の英語アナライザーでは、標準アナライザーが拡張されています。 単語から所有格 (末尾の 's) が除去され、Porter Stemming アルゴリズムに従ってステミングが適用され、英語のストップワードが除去されます。  

+ Microsoft の英語アナライザーでは、ステミングではなくレンマ化が実行されます。 つまり、語形変化および変則的な語形がより適切に処理され、関連性の高い検索結果が得られます 

## <a name="configuring-analyzers"></a>アナライザーの構成

言語アナライザーはそのままで使用されます。 インデックス定義の各フィールドについて、言語と言語スタック (Microsoft または Lucene) を指定するアナライザー名を **analyzer** プロパティに設定できます。 そのフィールドに対してインデックスの作成および検索を行う場合は、同じアナライザーが適用されます。 たとえば、英語、フランス語、スペイン語によるホテルの説明を含む個別のフィールドを同じインデックスに同時に作成できます。

> [!NOTE]
> インデックス作成時とフィールドのクエリ時とで異なる言語アナライザーを使用することはできません。 この機能は、[カスタムアナライザー](index-add-custom-analyzers.md) 用に予約されています。 このため、**searchAnalyzer** プロパティまたは **indexAnalyzer** プロパティを言語アナライザーの名前に設定しようとすると、REST API によってエラー応答が返されます。 代わりに、**アナライザー** プロパティを使用する必要があります。

**searchFields** クエリ パラメーターを使用して、クエリ内で検索対象とする言語固有のフィールドを指定します。 アナライザー プロパティを含むクエリの例は、「[ドキュメントの検索](https://docs.microsoft.com/rest/api/searchservice/search-documents)」で確認できます。 

インデックス プロパティについて詳しくは、「[インデックスの作成 &#40;Azure Cognitive Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)」をご覧ください。 Azure Cognitive Search での解析について詳しくは、[Azure Cognitive Search でのアナライザー](https://docs.microsoft.com/azure/search/search-analyzers)に関する記事をご覧ください。

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>言語アナライザー一覧 
 サポートされている言語と、Lucene およびマイクロソフトのアナライザーの名前を以下に一覧します。  

|Language|Microsoft のアナライザーの名前|Lucene のアナライザーの名前|  
|--------------|-----------------------------|--------------------------|  
|アラビア語|ar.microsoft|ar.lucene|  
|アルメニア語||hy.lucene|  
|ベンガル語|bn.microsoft||  
|バスク語||eu.lucene|  
|Bulgarian|bg.microsoft|bg.lucene|  
|カタロニア語|ca.microsoft|ca.lucene|  
|簡体中国語|zh-Hans.microsoft|zh-Hans.lucene|  
|中国語 (繁体字)|zh-Hant.microsoft|zh-Hant.lucene|  
|Croatian|hr.microsoft||  
|Czech|cs.microsoft|cs.lucene|  
|Danish|da.microsoft|da.lucene|  
|Dutch|nl.microsoft|nl.lucene|  
|English|en.microsoft|en.lucene|  
|Estonian|et.microsoft||  
|Finnish|fi.microsoft|fi.lucene|  
|French|fr.microsoft|fr.lucene|  
|ガリシア語||gl.lucene|  
|German|de.microsoft|de.lucene|  
|Greek|el.microsoft|el.lucene|  
|グジャラート語|gu.microsoft||  
|ヘブライ語|he.microsoft||  
|ヒンディー語|hi.microsoft|hi.lucene|  
|Hungarian|hu.microsoft|hu.lucene|  
|アイスランド語|is.microsoft||  
|インドネシア語|id.microsoft|id.lucene|  
|アイルランド語||ga.lucene|  
|Italian|it.microsoft|it.lucene|  
|Japanese|ja.microsoft|ja.lucene|  
|カンナダ語|kn.microsoft||  
|Korean|ko.microsoft|ko.lucene|  
|Latvian|lv.microsoft|lv.lucene|  
|Lithuanian|lt.microsoft||  
|マラヤーラム語|ml.microsoft||  
|マレー語 (ラテン)|ms.microsoft||  
|マラーティー語|mr.microsoft||  
|ノルウェー語|nb.microsoft|no.lucene|  
|ペルシャ語||fa.lucene|  
|Polish|pl.microsoft|pl.lucene|  
|ポルトガル語 (ブラジル)|pt-Br.microsoft|pt-Br.lucene|  
|ポルトガル語 (ポルトガル)|pt-Pt.microsoft|pt-Pt.lucene|  
|パンジャーブ語|pa.microsoft||  
|Romanian|ro.microsoft|ro.lucene|  
|Russian|ru.microsoft|ru.lucene|  
|セルビア語 (キリル)|sr-cyrillic.microsoft||  
|セルビア語 (ラテン)|sr-latin.microsoft||  
|Slovak|sk.microsoft||  
|Slovenian|sl.microsoft||  
|Spanish|es.microsoft|es.lucene|  
|Swedish|sv.microsoft|sv.lucene|  
|タミル語|ta.microsoft||  
|テルグ語|te.microsoft||  
|Thai|th.microsoft|th.lucene|  
|Turkish|tr.microsoft|tr.lucene|  
|ウクライナ語|uk.microsoft||  
|ウルドゥ語|ur.microsoft||  
|ベトナム語|vi.microsoft||  

 名前に **Lucene** が含まれるすべてのアナライザーでは、[Apache Lucene の言語アナライザー](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )が利用されています。

## <a name="see-also"></a>関連項目  

+ [インデックスの作成 &#40;Azure コグニティブ検索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

