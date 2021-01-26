---
title: インポート時にデータをクロールするためのインデクサー
titleSuffix: Azure Cognitive Search
description: Azure SQL Database、SQL Managed Instance、Azure Cosmos DB、または Azure ストレージをクロールして検索可能なデータを抽出し、Azure Cognitive Search インデックスを作成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 5861e79054bed0d9d75258dfa9cb39b198f0f93d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216446"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure Cognitive Search のインデクサー

Azure Cognitive Search の "*インデクサー*" は、検索可能なデータとメタデータを Azure 外部データ ソースから抽出し、データ ソースとインデックス間のフィールドのマッピングを検索インデックスに設定するクローラーです。 この方法は、インデックスにデータを追加するコードを記述することなく、サービスがデータをプルするため、「プル モデル」と呼ばれることもあります。

インデクサーは Azure 専用であり、[Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-cosmosdb.md)、[Azure Table Storage](search-howto-indexing-azure-tables.md)、[Blob Storage](search-howto-indexing-azure-blob-storage.md) 用にそれぞれ個別のインデクサーを使用します。 インデクサーを構成するときは、データ ソース (抽出元) とインデックス (ターゲット) を指定します。 Blob Storage など、いくつかのソースには、そのコンテンツの種類に固有の追加の構成プロパティがあります。

インデクサーは、オンデマンドで実行することも、5 分ごとに実行される定期的なデータ更新スケジュールで実行することもできます。 より頻繁に更新するには、Azure Cognitive Search と外部データ ソースの両方のデータを同時に更新するプッシュ モデルが必要です。

## <a name="usage-scenarios"></a>使用シナリオ

データ インジェストの唯一の手段としてインデクサーを使用することも、インデックス内のフィールドの一部だけを読み込み、その後必要に応じてコンテンツを変換またはエンリッチするという操作を含む、手法の組み合わせを使用することもできます。 次の表に主なシナリオをまとめています。

| シナリオ |戦略 |
|----------|---------|
| 単一のソース | このパターンは最も単純です。1 つのデータ ソースが検索インデックス用の唯一のコンテンツ プロバイダーです。 そのソースから、検索インデックスのドキュメント キーとして機能する一意の値を含む 1 つのフィールドを識別します。 その一意の値が識別子として使用されます。 他のすべてのソース フィールドは、インデックス内の対応するフィールドに暗黙的または明示的にマップされます。 </br></br>重要なポイントは、ドキュメント キーの値がソース データから生成されることです。 検索サービスでは、キー値は生成されません。 以降の実行では、新しいキーを持つ着信ドキュメントが追加されます。それに対し、既存のキーを持つ着信ドキュメントは、インデックス フィールドが null であるかデータが設定済みかに応じて、マージまたは上書きされます。 |
| 複数のソース| インデックスでは、複数のソースからコンテンツを受け入れることができます。この場合、実行のたびに、異なるソースから新しいコンテンツが取り込まれます。 </br></br>1 つの結果として、インデックスではインデクサーのそれぞれの実行後にドキュメントが取得され、各ソースからのドキュメントがすべて生成されます。 たとえば、ドキュメント 1-100 は Blob Storage からのものであり、ドキュメント 101-200 は Azure SQL からのものである、というようになります。 このシナリオの課題は、すべての着信データに対して機能するインデックス スキーマと、検索インデックスで一様なドキュメント キーの構造を設計することです。 本来、ドキュメントを一意に識別する値は、BLOB コンテナーでは metadata_storage_path、SQL テーブルでは主キーです。 コンテンツの抽出元に関係なく、共通の形式でキー値を提供するには、一方または両方のソースを修正する必要があると想定できます。 このシナリオでは、データを均一化して 1 つのインデックスにプルできるように、ある程度の前処理を実行することを想定する必要があります。</br></br>考えられる別の結果としては、最初の実行時に検索ドキュメントに部分的にデータが設定され、その後、後続の実行によってさらにデータが設定され、他のソースから値が取り込まれます。 たとえば、フィールド 1-10 は Blob Storage からのものであり、11-20 は Azure SQL からのものである、というようになります。 このパターンの課題は、インデックス作成のそれぞれの実行で同じドキュメントがターゲットになるようにすることです。 フィールドを既存のドキュメントにマージするには、ドキュメント キーが一致している必要があります。 このシナリオのデモについては、[チュートリアル:複数のデータ ソースからのインデックス作成](tutorial-multiple-data-sources.md)に関する記事のソース コード。 |
| コンテンツの変換 | Cognitive Search では、イメージ分析と自然言語処理を追加して新しい検索可能なコンテンツと構造を作成する、オプションの [AI エンリッチメント](cognitive-search-concept-intro.md)の動作がサポートされます。 AI エンリッチメントは、アタッチされている[スキルセット](cognitive-search-working-with-skillsets.md)を使用してインデクサーによって駆動されます。 AI エンリッチメントを実行する場合、インデクサーではやはりインデックスとデータ ソースが必要になりますが、このシナリオではインデクサーの実行にスキルセット処理が追加されます。 |

## <a name="approaches-for-creating-and-managing-indexers"></a>インデクサーの作成と管理の方法

インデクサーの作成と管理は、次の方法で行うことができます。

+ [Portal のデータのインポート ウィザード](search-import-data-portal.md)
+ [Service REST API](/rest/api/searchservice/Indexer-operations)
+ [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

SDK を使用している場合は、インデクサー、データ ソース、スキルセットで機能するように [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) を作成します。 上記のリンクは .NET SDK 用ですが、すべての SDK で、SearchIndexerClient および同様の API が提供されます。

当初、新しいデータ ソースはプレビュー機能として発表され、REST 専用です。 一般提供に向けてレベルが向上すると、ポータルとさまざまな SDK に完全サポートが組み込まれ、それぞれ独自のリリース スケジュールに従います。

## <a name="permissions"></a>アクセス許可

インデクサーに関連するすべての操作 (状態や定義の GET 要求を含む) には、[管理者 API キー](search-security-api-keys.md)が必要です。

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>サポートされるデータ ソース

インデクサーは、Azure 上のデータ ストアをクロールします。

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (プレビュー段階)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Azure Virtual Machines における SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>インデックス作成のステージ

最初の実行時に、インデックスが空の場合、テーブルまたはコンテナーで提供されるすべてのデータがインデクサーによって読み取られます。 その後の実行では、通常、変更されたデータのみがインデクサーによって検出され取得されます。 BLOB データの場合、変更の検出は自動で行われます。 Azure SQL や Cosmos DB などの他のデータ ソースで、変更の検出を有効にする必要があります。

インデクサーは、それによって取り込まれたドキュメントのそれぞれについて、ドキュメントの検索からインデックス作成のための最終的な検索エンジンの "ハンドオフ" にいたるまでの複数のステップを実装または調整します。 必要に応じて、インデクサーは、スキルセットが定義されていると想定して、スキルセットの実行と出力を促進するのにも役立ちます。

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="インデクサーのステージ" border="false":::

### <a name="stage-1-document-cracking"></a>ステージ 1:ドキュメントの解読

ドキュメント解析は、ファイルを開いてコンテンツを抽出するプロセスです。 データ ソースの種類に応じて、インデックス付けが可能なコンテンツを抽出するために、インデクサーによってさまざまな操作の実行が試行されます。  

例 :  

+ ドキュメントが [Azure SQL データ ソース](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)内のレコードの場合、インデクサーによってレコードの各フィールドが抽出されます。
+ ドキュメントが [Azure Blob Storage データ ソース](search-howto-indexing-azure-blob-storage.md)内の PDF ファイルの場合、テキスト、イメージ、メタデータがインデクサーによって抽出されます。
+ ドキュメントが [Cosmos DB データ ソース](search-howto-index-cosmosdb.md)内のレコードの場合、インデクサーによって Cosmos DB ドキュメントからフィールドとサブフィールドが抽出されます。

### <a name="stage-2-field-mappings"></a>ステージ 2:フィールド マッピング 

インデクサーによって、ソース フィールドからテキストが抽出され、インデックスまたはナレッジ ストアの送信先フィールドにそれが送信されます。 フィールド名と型が一致すると、パスは明確になります。 ただし、出力には異なる名前または型が必要な場合があります。その場合は、フィールドをマップする方法をインデクサーに指示する必要があります。 このステップは、ドキュメント解析の後、変換前に、インデクサーがソース ドキュメントから読み取るときに発生します。 [フィールド マッピング](search-indexer-field-mappings.md)を定義するときに、ソース フィールドの値は変更されずにそのまま送信先フィールドに送信されます。 フィールド マッピングは省略可能です。

### <a name="stage-3-skillset-execution"></a>ステージ 3:スキルセットの実行

スキルセットの実行は、組み込みまたはカスタムの AI 処理を呼び出す省略可能なステップです。 これは画像分析の形式で光学式文字認識 (OCR) に必要になる場合や、言語翻訳が必要な場合があります。 変換が何であれ、スキルセットの実行は、エンリッチメントが発生する場所です。 インデクサーがパイプラインの場合、[スキルセット](cognitive-search-defining-skillset.md)を "パイプライン内のパイプライン" として考えることができます。 スキルセットには、スキルと呼ばれる独自のステップのシーケンスがあります。

### <a name="stage-4-output-field-mappings"></a>ステージ 4: 出力フィールド マッピング

スキルセットを含める場合、通常は出力フィールド マッピングを含める必要があります。 スキルセットの出力は、実際にはエンリッチされたドキュメントと呼ばれる情報のツリーです。 出力フィールド マッピングを使用すると、このツリーの部分を選択してインデックス内のフィールドにマップすることができます。 [出力フィールド マッピングを定義する](cognitive-search-output-field-mapping.md)方法について学習します。

フィールド マッピングではデータ ソースからターゲット フィールドに逐語的な値が関連付けられるのに対し、出力フィールド マッピングでは、エンリッチされたドキュメント内の変換された値をインデックス内のターゲット フィールドに関連付ける方法がインデクサーに伝えられます。 省略可能と見なされるフィールド マッピングとは異なり、インデックスに存在する必要がある変換されたコンテンツに対しては、出力フィールド マッピングを常に定義する必要があります。

次の図は、インデクサーのステージ (ドキュメント解析、フィールド マッピング、スキルセットの実行、出力フィールド マッピング) のサンプル インデクサーの[デバッグ セッション](cognitive-search-debug-session.md)表現を示しています。

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="デバッグ セッションのサンプル" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>基本的な構成手順

インデクサーで実行できる機能は、データ ソースごとに異なります。 そのためインデクサーやデータ ソースの構成には、インデクサーの種類ごとに異なる点があります。 しかし基本的な成り立ちと要件は、すべてのインデクサーに共通です。 以降、すべてのインデクサーに共通の手順について取り上げます。

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

インデクサーは、*データ ソース* オブジェクトからデータ ソース接続を取得します。 データ ソース定義では、接続文字列と、場合によっては資格情報が提供されます。 [データ ソースの作成](/rest/api/searchservice/create-data-source) REST API または [SearchIndexerDataSourceConnection クラス](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)を呼び出して、リソースを作成します。

データ ソースは、それを使用するインデクサーとは別に構成および管理されます。これは、1 つのデータ ソースを複数のインデクサーで使用して、一度に複数のインデックスを読み込めることを意味します。

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する

インデクサーは、データ インジェストに関連したいくつかのタスクを自動化しますが通常、そこにはインデックスの作成は含まれていません。 前提条件として、定義済みのインデックスが必要です。加えてそのフィールドは、外部データ ソース内のフィールドと一致している必要があります。 各フィールドでは、名前とデータ型が一致する必要があります。 インデックス構築の詳細については、「[インデックスの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/Create-Index)」または「[SearchIndex クラス](/dotnet/api/azure.search.documents.indexes.models.searchindex)」を参照してください。 フィールドの関連付けについては、[Azure Cognitive Search インデクサーのフィールド マッピング](search-indexer-field-mappings.md)に関する記事を参照してください。

> [!Tip]
> インデクサーで自動的にインデックスを生成することはできませんが、ポータルにある **データのインポート** ウィザードを活用することができます。 ほとんどの場合、ウィザードで、ソース内の既存のメタデータからインデックス スキーマを推測し、仮のインデックス スキーマを得ることができます。ウィザードがアクティブである間は、このスキーマをインライン編集することが可能です。 サービスのインデックスが作成された後にポータルで行う編集は、主に新しいフィールドの追加に限定されます。 ウィザードは、あくまでインデックスの作成にご使用ください。インデックスの修正には適していません。 実践的なスキルを身に付けるには、[ポータルのチュートリアル](search-get-started-portal.md)をご覧ください。

### <a name="step-3-create-and-schedule-the-indexer"></a>手順 3:インデクサーを作成してスケジュールを設定する

インデクサー定義は、データの取り込みに関連するすべての要素をまとめたコンストラクトです。 必要な要素には、データ ソースとインデックスが含まれます。 省略可能な要素には、スケジュールとフィールド マッピングが含まれます。 フィールド マッピングは、ソース フィールドとインデックス フィールドが明らかに対応している場合にのみ、省略可能となります。 インデクサー構築の詳細については、「[インデクサーの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/Create-Indexer)」を参照してください。

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>インデクサーのオンデマンド実行

インデックス作成はスケジュール設定するのが普通ですが、[Run コマンド](/rest/api/searchservice/run-indexer)を使用して、インデクサーをオンデマンドで呼び出すことも可能です。

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> API の実行で成功コードが返された場合、インデクサーの呼び出しはスケジュール済みですが、実際の処理は非同期的に実行されます。 

インデクサーの状態は、ポータルか [Get Indexer Status API](/rest/api/searchservice/get-indexer-status) を使用して監視できます。 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>インデクサーの状態の取得

インデクサーの現在の状態と実行の履歴は、[インデクサー状態の取得](/rest/api/searchservice/get-indexer-status)コマンドを使用して取得できます。

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

応答には、全体的なインデクサーの状態、最後の (または実行中の) インデクサー呼び出し、およびインデクサー呼び出しの最近の履歴が含まれています。

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

実行履歴には、最近完了した 50 件の実行内容が含まれ、時系列の逆の順に並べられます (そのため、最近の実行内容が応答の最初に表示されます)。

## <a name="next-steps"></a>次のステップ

基本的な概念の説明は以上です。次のステップとしてデータ ソースの種類ごとの要件とタスクを確認してください。

+ [Azure SQL Database、SQL Managed Instance、または Azure 仮想マシン上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cognitive Search BLOB インデクサーを使用した CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
+ [Azure Cognitive Search BLOB インデクサーを使用した JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)