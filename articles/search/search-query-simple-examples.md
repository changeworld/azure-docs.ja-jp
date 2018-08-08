---
title: Azure Search の単純なクエリの例 | Microsoft Docs
description: フルテキスト検索、フィルター検索、地理検索、ファセット検索、および Azure Search インデックスのクエリに使用されるその他のクエリ文字列の単純なクエリの例。
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368600"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Azure Search でクエリを作成するための単純構文クエリの例

[単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)では、既定のクエリ パーサーを呼び出して、Azure Search インデックスに対してフルテキスト検索クエリを実行します。 単純クエリ アナライザーは高速で、フルテキスト検索、フィルター検索、ファセット検索、地理検索などの Azure Search で一般的なシナリオに対応します。 この記事では、単純な構文を使用するときに利用できるクエリ操作をデモンストレーションする例について詳しく説明します。

代替のクエリ構文には[完全な Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 構文があります。この構文は、あいまい検索、ワイルドカード検索などのより複雑なクエリ構造をサポートし、処理に時間がかかります。 完全な構文の詳細と例については、[Lucene 構文のクエリの例](search-query-lucene-examples.md)に関するページを参照してください。

## <a name="formulate-requests-in-postman"></a>Postman で要求を作成する

次の例では、[City of New York OpenData](https://nycopendata.socrata.com/) イニシアティブが提供するデータセットに基づいて利用可能なジョブで構成される NYC ジョブ検索インデックスを活用します。 このデータが最新のものであるとか、完全であるとはお考えにならないでください。 インデックスは、Microsoft が提供するサンドボックス サービス上にあります。つまり、これらのクエリを試すのに Azure サブスクリプションまたは Azure Search は必要ありません。

必要になるのは、GET で HTTP 要求を発行するための Postman または同等のツールです。 詳細については、[REST クライアントを使用したテスト](search-fiddler.md)に関するページを参照してください。

### <a name="set-the-request-header"></a>要求ヘッダーを設定する

1. 要求ヘッダーで、**Content-Type** を `application/json` に設定します。

2. **api-key** を追加して文字列 `252044BE3886FE4A8E3BAA4F595114BB` に設定します。 これは、NYC ジョブ インデックスをホストするサンドボックス検索サービスのクエリ キーです。

要求ヘッダーを指定した後は、**search=** 文字列のみを入れ替えることで、この記事のすべてのクエリに対して要求ヘッダーを再利用できます。 

  ![Postman の要求ヘッダー](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>要求 URL を設定する

要求は、Azure Search エンドポイントと検索文字列を含む URL と GET コマンドを組み合わせたものです。

  ![Postman の要求ヘッダー](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL は、次の要素から構成されます。

+ **`https://azs-playground.search.windows.net/`** は、Azure Search 開発チームによって管理されているサンドボックス検索サービスです。 
+ **`indexes/nycjobs/`** は、そのサービスのインデックス コレクション内の NYC ジョブ インデックスです。 要求にはサービス名とインデックスの両方が必要です。
+ **`docs`** は、検索可能なすべてのコンテンツを含むドキュメント コレクションです。 要求ヘッダーに指定されたクエリ api-key は、ドキュメント コレクションを対象とする読み取り操作に対してのみ機能します。
+ **`api-version=2017-11-11`** は、すべての要求に必須のパラメーターである api-version を設定します。
+ **`search=*`** はクエリ文字列です。最初のクエリでは null で、最初の 50 件の結果が返されます (既定値)。

## <a name="send-your-first-query"></a>初めてクエリを送信する

確認手順として、次の要求を GET に貼り付け、**[送信]** をクリックします。 結果は冗長な JSON ドキュメントとして返されます。 下の最初の例のこの URL をコピーして貼り付けることができます。

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

クエリ文字列 **`search=*`** は、null または空の検索に相当する未指定の検索です。 これは特に有用ではありませんが、実行できる最も簡単な検索です。

オプションで、URL に **`$count=true`** を追加して、検索基準に一致するドキュメントの数を返すことができます。 空の検索文字列では、これはインデックス内のすべてのドキュメントです (NYC ジョブの場合は 2802)。

## <a name="how-to-invoke-simple-query-parsing"></a>単純なクエリの解析を呼び出す方法

対話型クエリでは何も指定する必要はありません。既定値は simple です。 コードでは、以前に完全なクエリ構文用に **queryType=full** を呼び出している場合は、**queryType=simple** を使用して既定値にリセットすることができます。

## <a name="example-1-field-scoped-query"></a>例 1: フィールド スコープ クエリ

最初のクエリは構文に固有ではありません (このクエリは単純な構文と完全な構文の両方で機能します) が、この例を使用して、合理的に読みやすい JSON 応答を生成するベースライン クエリの概念を紹介します。 簡潔にするため、このクエリでは *business_title* フィールドのみを対象として、肩書きのみが返されるよう指定しています。 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

**searchFields** パラメーターは、検索範囲をビジネス タイトル フィールドだけに制限します。 **select** パラメーターは、結果セットに含まれるフィールドを決定します。

このクエリの応答は、次のスクリーンショットのようになります。

  ![Postman の応答のサンプル](media/search-query-lucene-examples/postman-sample-results.png)

お気付きかもしれませんが、検索スコアが指定されていなくても、すべてのドキュメントの検索スコアも返されます。 これは、検索スコアが結果のランク順を示す値を含むメタデータであるためです。 検索がフルテキスト検索でなかった、または適用する基準がないという理由でランクがない場合は、1 の均一のスコアが発生します。 null 検索の場合、基準はなく、行は任意の順序で返されます。 検索条件に含まれる定義が増えるのに応じて、検索スコアが意味のある値に変化します。

## <a name="example-2-look-up-by-id"></a>例 2: ID による参照

この例は少し特殊ですが、検索動作を評価する際、検索結果から除外されている理由や検索結果に含まれている理由を理解するために、ドキュメントの内容全体を調べることが必要になる場合があります。 ドキュメント全体を返すには、[参照操作](https://docs.microsoft.com/rest/api/searchservice/lookup-document)を使用してドキュメント ID を渡します。

すべてのドキュメントは一意の識別子を持ちます。 参照クエリの構文を試す場合、使用する ID を見つけるために、最初にドキュメント ID の一覧を返します。 NYC ジョブの場合、識別子は `id` フィールドに格納されます。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

次の例は、前の応答で最初に出現した `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" に基づいて特定のドキュメントを返す検索クエリです。 次のクエリでは、選択したフィールドだけでなく、ドキュメント全体が返されます。 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>例 3: 検索の精度

用語のクエリは、単一の用語を対象とし、おそらくその多くは個別に評価されます。 語句のクエリは引用符で囲まれ、逐語的な文字列として評価されます。 一致の精度は、演算子と searchMode によって制御されます。

例 1: **`&search=fire`** は、150 件の結果を返します。すべての一致に、ドキュメントのどこかに出現する単語 fire が含まれています。

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

例 2: **`&search=fire department`** は、2,002 件の結果を返します。 fire または department を含むドキュメントについて一致が返されます。

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

例 3: **`&search="fire department"`** は、82 件の結果を返します。 文字列を二重引用符で囲むと両方の用語に対して逐語的検索を実行することを表します。一致は、結合された用語で構成されるインデックス内のトークン化された用語で検出されます。 これが、**`search=+fire +department`** のような検索と同等でない理由です。 どちらの用語も必須ですが、これらは個別にスキャンされます。 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>例 4: searchMode によるブール値

単純な構文では、文字形式のブール演算子 (`+, -, |`) がサポートされています。 searchMode パラメーターは、精度と再現率のトレードオフを示します。`searchMode=any` は再現率を優先し (任意の条件で一致すると結果セットとしてドキュメントが適格になります)、`searchMode=all` は精度を優先します (すべての条件が一致する必要があります)。 既定値は `searchMode=any` です。これは、クエリに複数の演算子を含めて、絞り込んだ結果ではなくより広範な結果を取得した場合に、混乱を招く可能性があります。 これは特に NOT に当てはまります。NOT では、特定の用語を "含まない" すべてのドキュメントが結果に含まれます。

既定の searchMode (any) を使用した場合、2,800 件のドキュメントが返されます。これは、複合語 "fire department" を含むドキュメントと、用語 "Metrotech Center" を含まないすべてのドキュメントの合計です。

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
searchMode を `all` に変更すると、条件に累積的な効果が適用され、より小さな結果セット (21 個のドキュメント) が返されます。これは、"fire department" という語句全体を含むドキュメントから、Metrotech Center のアドレスのジョブを除いた結果です。

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>例 5: 結果の構造化

いくつかのパラメーターは、検索結果に含まれるフィールド、各バッチで返されるドキュメントの数、および並べ替え順を制御します。 この例では、前の例のいくつかを再利用し、**$select** ステートメントと逐語検索基準を使用して結果を特定のフィールドに限定し、82 件の一致を返します 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
前の例に付加して、タイトルで並べ替えることができます。 この並べ替えは、インデックス内で civil_service_title が *sortable* であるために機能します。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

結果のページングは、**$top** パラメーターを使用して実装できます。このケースでは、上位 5 つのドキュメントが返されます。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

次の 5 件を取得するには、最初のバッチをスキップします。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>次の手順
コードでクエリを指定してみてください。 次のリンクでは、既定の単純な構文を使用して .NET と REST API の両方の検索クエリを設定する方法について説明しています。

* [.NET SDK を使用した Azure Search インデックスの照会](search-query-dotnet.md)
* [REST API を使用した Azure Search インデックスの照会](search-query-rest-api.md)

追加の構文リファレンス、クエリ アーキテクチャ、およびサンプルについては、次のリンク先を参照してください。

+ [高度なクエリを作成するための Lucene 構文のクエリの例](search-query-lucene-examples.md)
+ [Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [完全な Lucene クエリ](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
