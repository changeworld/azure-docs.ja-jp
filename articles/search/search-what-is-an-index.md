---
title: インデックスの概要
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の検索インデックスについて説明し、コンテンツ、構築、物理的な式、およびインデックス スキーマについて説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 51b075dbce189370cf502bce6d46471da6c58348
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490537"
---
# <a name="indexes-in-azure-cognitive-search"></a>Azure Cognitive Search のインデックス

Azure Cognitive Search では、"*検索インデックス*" は検索可能なコンテンツであり、インデックス作成、フル テキスト検索、フィルター処理されたクエリの検索エンジンで利用できます。 インデックスは、スキーマによって定義され、検索サービスに保存されます。2 番目のステップとしてデータのインポートが続きます。 このコンテンツは検索サービス内に存在します。これは、最新のアプリケーションで想定されるミリ秒単位の応答時間に必要な、プライマリ データ ストアとは別のものです。 特定のインデックス作成シナリオを除き、検索サービスはローカル データに接続したりクエリを実行したりすることはありません。

検索インデックスを作成および管理している場合は、この記事で次のことを理解できます。

+ コンテンツ (ドキュメントおよびスキーマ)
+ 物理的な表現
+ 基本操作

今すぐに使いたいですか? 代わりに、[検索インデックスの作成](search-how-to-create-search-index.md)に関する記事を参照してください。

## <a name="content-of-a-search-index"></a>検索インデックスのコンテンツ

Cognitive Search のインデックスには "*検索ドキュメント*" が格納されます。 概念的に、ドキュメントはインデックス内で検索可能なデータの 1 つの単位です。 たとえば、小売業者に製品ごとのドキュメントがあり、ニュース組織に記事ごとのドキュメントがある場合、旅行サイトにはホテルと目的地ごとのドキュメントがある場合があります。 これらの概念をなじみのあるデータベースの同等のものに対応させるなら、*検索インデックス* は *テーブル* と同じで、*ドキュメント* はテーブルにおける *行* とほぼ同じです。

ドキュメントの構造は、次に示すように、インデックス スキーマによって決定されます。 "フィールド" コレクションは通常、インデックスの最大の部分であり、各フィールドには、名前、[データ型](/rest/api/searchservice/Supported-data-types)の割り当て、および使用方法を決定する許容される動作を示す属性が設定されます。

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

その他の要素は簡略化のため折りたたまれていますが、次のリンクから詳細を得ることができます。 

+ [suggester](index-add-suggesters.md) は、オートコンプリートのような先行入力クエリをサポートします
+ 関連性のチューニングには[スコアリング プロファイル](index-add-scoring-profiles.md)が使用されます
+ [アナライザー](search-analyzers.md)は、言語ルールまたはアナライザーによってサポートされるその他の特性に基づいて、文字列をトークンに処理するために使用されます
+ [クロスオリジン リモート スクリプティング (CORS)](search-how-to-create-search-index.md#corsoptions) は、異なるドメインからの要求を発行するアプリに使用されます
+ [暗号化キー](search-security-manage-encryption-keys.md)は、インデックス内の機密性の高いコンテンツをダブル暗号化するために使用されます。

### <a name="field-definitions"></a>フィールド定義

検索ドキュメントは、"フィールド" コレクションによって定義されます。 ドキュメントの識別のためのフィールド (キー)、検索可能なテキストの格納、フィルター、ファセット、並べ替えをサポートするためのフィールドが必要になります。 ユーザーに表示しないデータのフィールドが必要になる場合もあります。 たとえば、検索順位の変更に使用できる利益率やマーケティング プロモーションのフィールドが必要な場合があります。

受信データが階層化された性質を持つ場合は、入れ子構造を表すために使用される[複合型](search-howto-complex-data-types.md)として、インデックス内でそれを表すことができます。 あらかじめ登録されているサンプル データ セットである Hotels (ホテル) は、各ホテルとの一対一のリレーションシップを持つ Address (複数のサブフィールドを含む) と、各ホテルに複数の部屋が関連付けられている複合型コレクションの Rooms を使用した複合型を示しています。 

<a name="index-attributes"></a>

### <a name="field-attributes"></a>フィールド属性

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

## <a name="physical-representation"></a>物理的な表現

Azure Cognitive Search におけるインデックスの物理的な構造は、主に内部実装です。 そのスキーマにアクセスし、そのコンテンツにクエリを実行し、そのサイズを監視し、容量を管理することができますが、クラスター自体 (インデックス、シャード、その他のファイルとフォルダー) は、Microsoft がお客様に代わって制限を受けることなく、内部で管理します。

インデックスのサイズは、次の条件によって決まります。

+ ドキュメントの数量と構成
+ インデックスの構成 (具体的には、suggester を含めるかどうか)
+ 個々のフィールドの属性

インデックス サイズを監視するには、Azure portal の [インデックス] タブを使用するか、検索サービスに対して [GET INDEX 要求](/rest/api/searchservice/get-index)を発行します。

### <a name="factors-influencing-index-size"></a>インデックス サイズに影響する要因

ドキュメントの構成と数量は、インポートに選択した内容によって決まります。 検索インデックスには検索可能なコンテンツのみを含める必要があります。 ソース ドキュメントにバイナリ フィールドが含まれている場合は、通常、これらのフィールドをインデックス スキーマから省略します (AI エンリッチメントを使用してコンテンツを解読して分析し、テキスト検索可能な情報を作成する場合を除く)。

インデックス構成には、suggester、顧客アナライザー、スコアリング プロファイル、CORS 設定、暗号化キー情報など、ドキュメント以外の他のコンポーネントを含めることができます。 上記の一覧で、インデックス サイズに影響する可能性のあるコンポーネントは suggester だけです。 [**suggester**](index-add-suggesters.md) は、先行入力またはオートコンプリートのクエリをサポートするコンストラクトです。 そのため、suggester を含めると、インデックス作成プロセスによって、逐語的な文字の照合に必要なデータ構造が作成されます。 suggester はフィールド レベルで実装されるため、先行入力にふさわしいフィールドのみを選択してください。

フィールド属性は、インデックス サイズの 3 番目の考慮事項です。 属性によって動作が決定されます。 これらの動作をサポートするために、インデックス作成プロセスによってサポート データ構造が作成されます。 たとえば、"検索可能" によって、トークン化された用語の転置インデックスをスキャンする[フル テキスト検索](search-lucene-query-architecture.md)が呼び出されます。 これに対して、"フィルター可能" または "並べ替え可能" の属性では、未変更の文字列に対する反復処理がサポートされます。

### <a name="example-demonstrating-the-storage-implications-of-attributes-and-suggesters"></a>属性と suggester がストレージに与える影響を示す例

次のスクリーンショットは、属性のさまざまな組み合わせの結果であるインデックス格納パターンを示しています。 このインデックスは **不動産サンプル インデックス** に基づいています。これは、データのインポート ウィザードと組み込みのサンプル データを使用して簡単に作成できます。 インデックスのスキーマは表示されませんが、インデックス名に基づいて属性を推測できます。 たとえば、*realestate-searchable* インデックスでは "searchable" 属性が選択されていて他には何もなく、*realestate-retrievable* インデックスでは "retrievable" 属性が選択されていて他には何もなく、以下同様です。

![属性の選択に基づいたインデックス サイズ](./media/search-what-is-an-index/realestate-index-size.png "属性の選択に基づいたインデックス サイズ")

これらのインデックスのバリエーションはやや人為的なものですが、属性がストレージに与える影響の広範な比較のために参照できます。 設定 "retrievable" はインデックスのサイズを増加させますか? いいえ。 **suggester** にフィールドを追加するとインデックスのサイズが増加しますか? はい。 

フィルター処理されたフィールドと並べ替え済みのフィールドは、文字シーケンスを逐語的に照合できるようにトークン化されないため、フィールドをフィルター設定または並べ替え可能にすると、ストレージ消費量も増大します。

また、上記の表に反映されていない事柄に、[アナライザー](search-analyzers.md)の影響があります。 edgeNgram トークナイザーを使用して逐語的な文字シーケンス (a、ab、abc、abcd) を格納した場合、インデックスのサイズは、標準アナライザーを使用した場合よりも大きくなります。

## <a name="basic-operations"></a>基本操作

インデックスの概要を理解したので、このセクションでは、1 つのインデックスに接続してセキュリティを保護するなどの、インデックスの実行時操作について説明します。

### <a name="index-isolation"></a>インデックスの分離性
  
Cognitive Search で操作の対象となるインデックスは一度に 1 つです。インデックスに関連したすべての操作は、単一のインデックスが対象となります。 関連するインデックスや、インデックス作成またはクエリのための独立したインデックスの結合の概念はありません。 

インデックスを管理する際、インデックスの移動やコピーに関して、ポータルや API のサポートはないことに注意してください。 代わりに、ユーザーは通常、アプリケーション デプロイ ソリューションを別の検索サービスでポイントする (同じインデックス名を使用している場合) か、名前を変更して現在の検索サービスにコピーを作成してからビルドします。

### <a name="continuously-available"></a>継続的に使用可能

インデックスは継続的に使用可能であり、一時停止したり、オフラインにしたりすることはできません。 継続的な操作のために設計されているので、コンテンツの更新やインデックス自体への追加はリアルタイムで行われます。 その結果、要求がドキュメントの更新と一致する場合、クエリは一時的に不完全な結果を返す可能性があります。

ドキュメント操作 (更新または削除) や、現在のインデックスの既存の構造と整合性に影響しない変更 (新しいフィールドの追加など) に対しては、クエリの継続性が存在します。 構造上の更新 (既存のフィールドの変更) を行う必要がある場合は、通常、開発環境での削除と再構築のワークフローを使用して、または運用サービスでインデックスの新しいバージョンを作成することによってそれらが管理されます。

再構築を避けるため、小規模な変更を行っている一部のユーザーは、以前のバージョンと共存する新しいものを作成することによって、フィールドの "バージョン管理" を選択しています。 これは、時間の経過と共に、特にレプリケートに負荷のかかる運用環境のインデックスで、古いフィールドまたは古いカスタム アナライザー定義の形式の、孤立したコンテンツになります。 インデックス ライフサイクル管理の一部として、インデックスの計画更新に関する問題に対処することができます。

### <a name="endpoint-connection-and-security"></a>エンドポイントの接続とセキュリティ

すべてのインデックス作成とクエリの要求は、インデックスを対象とします。 エンドポイントは、通常、次のいずれかになります。

| エンドポイント | 接続とアクセスの制御 |
|----------|-------------------------------|
| `<your-service>.search.windows.net/indexes` | インデックスのコレクションを対象とします。 インデックスを作成、一覧表示、または削除するときに使用します。 これらの操作には管理者権限が必要です。管理者 API キーまたは Search 共同作成者ロールを通じて使用できます。 |
| `<your-service>.search.windows.net/indexes/<your-index>/docs` | 1 つのインデックスのドキュメント コレクションを対象とします。 インデックスにクエリを実行するときに使用します。 読み取り権限で十分であり、クエリ API キーまたはデータ閲覧者ロールを通じて使用できます。 |

## <a name="next-steps"></a>次のステップ

Azure Cognitive Search のインデックスの作成には、ほぼどのサンプルまたはチュートリアルを使用しても、実践の参考にできます。 まず、目次から任意のクイックスタートを選択できます。

ただし、データを使用してインデックスを読み込む方法についても理解しておく必要があります。 インデックスの定義とデータのインポートの方法は、連携して定義されます。 次の記事では、インデックスの作成および読み込みの詳細について説明します。

+ [検索インデックスの作成](search-how-to-create-search-index.md)

+ [データ インポートの概要](search-what-is-data-import.md)

+ [ドキュメントの追加、更新、削除 (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 