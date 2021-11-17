---
title: インデックスの概要
titleSuffix: Azure Cognitive Search
description: スキーマ定義や物理データ構造など、Azure Cognitive Search のインデックス作成の概念とツールについて説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: ab1106ef927829589934485c2022d353339d5089
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062816"
---
# <a name="search-indexes-in-azure-cognitive-search"></a>Azure Cognitive Search の検索インデックス

Azure Cognitive Search では、フル テキスト クエリおよびフィルター適用済みクエリに使用される検索可能なコンテンツが "*検索インデックス*" に格納されます。 インデックスは、スキーマによって定義され、サービスに保存されます。2 番目の手順としてデータのインポートが続きます。 

この記事では、検索インデックスの概要について説明します。 すぐに開始したいですか? [検索インデックスの作成](search-how-to-create-search-index.md)に関する記事を参照してください。

## <a name="whats-a-search-index"></a>検索インデックスとは

Cognitive Search のインデックスには "*検索ドキュメント*" が格納されます。 概念的に、ドキュメントはインデックス内で検索可能なデータの 1 つの単位です。 たとえば、小売業者であれば製品ごとにドキュメントがあり、報道機関であれば記事ごとにドキュメントがあります。 これらの概念をなじみのあるデータベースの同等のものに対応させるなら、*検索インデックス* は *テーブル* と同じで、*ドキュメント* はテーブルにおける *行* とほぼ同じです。

インデックスの物理的な構造は、スキーマによって決まります。 "フィールド" コレクションは通常、インデックスの最大の部分であり、各フィールドには、名前、[データ型](/rest/api/searchservice/Supported-data-types)の割り当て、および使用方法を決定する許容される動作を示す属性が設定されます。

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

その他の要素は簡潔にするために折りたたまれていますが、[suggester](index-add-suggesters.md)、[スコアリング プロファイル](index-add-scoring-profiles.md)、[アナライザー](search-analyzers.md)の各リンクから詳細を得ることができます。これらは、アナライザーによってサポートされる言語規則やその他の特性と、[クロス オリジン リモート スクリプティング (CORS)](#corsoptions) 設定に従って文字列をトークンに処理するために使用されます。

## <a name="field-definitions"></a>フィールド定義

検索ドキュメントは、`fields` コレクションによって定義されます。 ドキュメントの識別のためのフィールド (キー)、検索可能なテキストの格納、フィルター、ファセット、並べ替えをサポートするためのフィールドが必要になります。 ユーザーに表示しないデータのフィールドが必要になる場合もあります。 たとえば、検索順位の変更に使用できる利益率やマーケティング プロモーションのフィールドが必要な場合があります。

Edm.String 型の 1 つのフィールドをドキュメント キーとして指定する必要があります。 これは各検索ドキュメントを一意に識別するために使用され、大文字と小文字が区別されます。 キーを使用してドキュメントを取得し、詳細ページを設定できます。

受信データ自体が階層化されている場合、入れ子構造を表すために、スキーマには[複合型](search-howto-complex-data-types.md)データ型を割り当てます。 あらかじめ登録されているサンプル データ セットである Hotels (ホテル) は、各ホテルとの一対一のリレーションシップを持つ Address (複数のサブフィールドを含む) と、各ホテルに複数の部屋が関連付けられている複合型コレクションの Rooms を使用した複合型を示しています。 

インデックスを作成する前に、アナライザーを文字列フィールドに割り当てます。 特定のフィールドでオートコンプリートを有効にする場合は、サジェスターに対して同じ操作を行います。

<a name="index-attributes"></a>

### <a name="attributes"></a>属性

フィールド属性は、フィールドがどのように使用されるか (フルテキスト検索、ファセット ナビゲーション、並べ替えなどの操作で使用されるかどうか) を決定します。 

文字列フィールドは多くの場合、"検索可能" および "取得可能" としてマークされます。 検索結果を絞り込むために使用されるフィールドには、"並べ替え可能"、"フィルター可能"、および "ファセット可能" が含まれます。

|属性|説明|  
|---------------|-----------------|  
|"検索可能" |フルテキスト検索可能であり、インデックス作成中の単語分割などの字句解析に従います。 検索可能フィールドを "sunny day" などの値に設定した場合、その値は内部的に個別のトークン "sunny" と "day" に分割されます。 詳細については、「[フルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。|  
|"フィルター可能" |$filter クエリで参照されます。 型 `Edm.String` または `Collection(Edm.String)` のフィルター可能フィールドは単語分割されないため、比較は完全に一致するかどうかだけになります。 たとえば、このようなフィールドを "sunny day" に設定した場合、`$filter=f eq 'sunny'` では一致が見つかりませんが、`$filter=f eq 'sunny day'` では見つかります。 |  
|"並べ替え可能" |既定では、システムは結果をスコアで並べ替えますが、ドキュメント内のフィールドに基づいて並べ替えを構成できます。 型 `Collection(Edm.String)` のフィールドを "並べ替え可能" にすることはできません。 |  
|"ファセット可能" |通常、カテゴリ (たとえば、特定の市にあるホテル) ごとのヒット カウントを含む検索結果のプレゼンテーションで使用されます。 このオプションは、 `Edm.GeographyPoint`型のフィールドでは使用できません。 "フィルター可能"、"並べ替え可能"、または "ファセット可能" である型 `Edm.String` のフィールドの長さは、最大 32 キロバイトです。 詳細については、「[Create Index (REST API) (インデックスの作成 (REST API))](/rest/api/searchservice/create-index)」を参照してください。|  
|"キー" |インデックス内のドキュメントの一意識別子。 キー フィールドとして正確に 1 つのフィールドを選択する必要があり、それは型 `Edm.String` である必要があります。|  
|"取得可能" |検索結果でこのフィールドを返すことができるかどうかを決定します。 これは、あるフィールド (*利幅* など) をフィルター、並べ替え、またはスコア付けのメカニズムとして使用するが、このフィールドをエンド ユーザーには表示したくない場合に役立ちます。 `true` for `key` である必要があります。|  

いつでも新しいフィールドを追加できますが、既存のフィールド定義はインデックスの有効期間の間ロックされます。 このため、開発者は通常、単純なインデックスを作成したり、アイデアをテストしたり、ポータル ページを使用して設定を検索したりするためのポータルを使用します。 インデックスを容易に再構築できるようにコード ベースのアプローチに従う場合は、インデックス設計を頻繁に反復する方がより効率的です。

> [!NOTE]
> インデックスの作成に使用する API には、さまざまな既定の動作があります。 [REST API](/rest/api/searchservice/Create-Index) の場合、ほとんどの属性は既定で有効であり (たとえば、文字列フィールドの "searchable" および "retrievable" は true です)、無効にする場合は、単にそれらを設定するだけです。 .NET SDK の場合は、逆のことが言えます。 明示的に設定していないプロパティの場合、既定では、特に有効にしない限り、対応する検索動作は無効にされています。

<a name="index-size"></a>

## <a name="storage-implications-of-field-attributes"></a>フィールド属性のストレージへの影響

インデックスのサイズは、アップロードするドキュメントのサイズと、suggester を含めるかどうかや、個々のフィールドに属性を設定する方法など、インデックス構成によって決定されます。 

次のスクリーンショットは、属性のさまざまな組み合わせの結果であるインデックス格納パターンを示しています。 インデックスは **不動産サンプルインデックス** に基づいています。これは、データのインポート ウィザードを使用して簡単に作成できます。 インデックスのスキーマは表示されませんが、インデックス名に基づいて属性を推測できます。 たとえば、*realestate-searchable* インデックスでは "searchable" 属性が選択されていて他には何もなく、*realestate-retrievable* インデックスでは "retrievable" 属性が選択されていて他には何もなく、以下同様です。

![属性の選択に基づいたインデックス サイズ](./media/search-what-is-an-index/realestate-index-size.png "属性の選択に基づいたインデックス サイズ")

これらのインデックスのバリエーションは人為的なものですが、属性がストレージに与える影響の広範な比較のために参照できます。 設定 "retrievable" はインデックスのサイズを増加させますか? いいえ。 **suggester** にフィールドを追加するとインデックスのサイズが増加しますか? はい。 

フィルター処理されたフィールドと並べ替え済みのフィールドは、文字シーケンスを逐語的に照合できるようにトークン化されないため、フィールドをフィルター設定または並べ替え可能にすると、ストレージ消費量も増大します。

また、上記の表に反映されていない事柄に、[アナライザー](search-analyzers.md)の影響があります。 edgeNgram トークナイザーを使用して逐語的な文字シーケンス (a、ab、abc、abcd) を格納した場合、インデックスのサイズは、標準アナライザーを使用した場合よりも大きくなります。

> [!Note]
> ストレージ アーキテクチャは Azure Cognitive Search の実装の詳細と考えられており、予告なく変更されることがあります。 現在の動作が将来も変わらないという保証はありません。

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>`corsOptions` のバージョン情報

インデックス スキーマには、`corsOptions` を設定するためのセクションが含まれます。 ブラウザーではすべてのクロスオリジン要求が禁止されるので、既定ではクライアント側 JavaScript で API を呼び出すことはできません。 インデックスに対するクロスオリジン クエリを許可するには、**corsOptions** 属性を設定することによって、CORS (クロスオリジン リソース共有) を有効にします。 セキュリティ上の理由から、CORS がサポートされているのはクエリ API だけです。 

CORS に対しては以下のオプションを設定できます。

+ **allowedOrigins** (必須):インデックスへのアクセスが許可されるオリジンのリストです。 つまり、これらのオリジンから提供されるすべての JavaScript コードは、インデックスのクエリを許可されます (正しい api-key を提供する場合)。 各オリジンの標準的な形式は `protocol://<fully-qualified-domain-name>:<port>` ですが、多くの場合 `<port>` は省略されます。 詳しくは、「[Cross-origin resource sharing (クロスオリジン リソース共有)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)」(Wikipedia) をご覧ください。

  すべてのオリジンにアクセスを許可する場合は、**allowedOrigins** 配列の唯一の要素として `*` を指定します。 "*運用環境の検索サービスに対してはこれは推奨されません*" が、開発およびデバッグでは役に立つことがよくあります。

+ **maxAgeInSeconds** (省略可能):ブラウザーでは、この値を使用して、CORS プレフライト応答をキャッシュする期間 (秒) が決定されます。 負ではない整数を指定する必要があります。 この値が大きいほどパフォーマンスはよくなりますが、CORS ポリシーの変更が有効になるまでの時間は長くなります。 これを設定しないと、既定の期間として 5 分が使用されます。

## <a name="next-steps"></a>次のステップ

Azure Cognitive Search のインデックスの作成には、ほぼどのサンプルまたはチュートリアルを使用しても、実践の参考にできます。 まず、目次から任意のクイックスタートを選択できます。

ただし、データを使用してインデックスを読み込む方法についても理解しておく必要があります。 インデックスの定義とデータのインポートの方法は、連携して定義されます。 次の記事では、インデックスの作成および読み込みの詳細について説明します。

+ [検索インデックスの作成](search-how-to-create-search-index.md)

+ [データ インポートの概要](search-what-is-data-import.md)

+ [ドキュメントの追加、更新、削除 (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 