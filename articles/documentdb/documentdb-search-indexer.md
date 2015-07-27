<properties 
    pageTitle="インデクサーを使用した DocumentDB と Azure Search の接続 | Azure" 
    description="この記事では、Azure Search インデクサーとデータ ソースとして DocumentDB を使用する方法を説明します。"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.tgt_pltfrm="NA" 
    ms.workload="data-services" 
    ms.date="06/16/2015" 
    ms.author="andrl"/>

#インデクサーを使用した DocumentDB と Azure Search の接続

DocumentDB データの優れた検索機能を実装する場合は、DocumentDB の Azure Search インデクサーをご使用ください。 この記事では、インデックス作成のインフラストラクチャを保守するコードを記述することなく Azure DocumentDB と Azure Search を統合する方法を説明します。

このセットアップを行うには、[Azure Search アカウントをセットアップ](../search-get-started.md#start-with-the-free-service)し (標準の検索のアップグレードは不要です)、[Azure Search REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) を呼び出して DocumentDB **データ ソース**とそのデータ ソースの**インデクサー**を作成する必要があります。

##<a id="Concepts"></a>Azure Search インデクサーの概念

Azure Search では、データ ソース (DocumentDB を含む) とそのデータ ソースに対して動作するインデクサーの作成および管理をサポートしています。

**データ ソース**により、インデックスを作成する必要があるデータ、データにアクセスするための資格情報、および Azure Search でのデータの変更 (コレクション内のドキュメントの変更や削除など) を効率的に識別するためのポリシーを指定します。データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

**インデクサー**は、データ ソースからターゲットの検索インデックスにデータが流れるしくみを説明します。ターゲット インデックスとデータ ソースの組み合わせごとにインデクサーを 1 つ作成するように設計する必要があります。複数のインデクサーから同じインデックスへ書き込むことができますが、1 つのインデクサーから書き込むことができるのは 1 つのインデックスのみです。インデクサーは次の目的で使用します。

- 1 回限りのデータのコピーを実行して、インデックスを作成する。
- スケジュールに従ってデータ ソースの変更とインデックスを同期する。スケジュールは、インデクサーの定義の一部です。
- 必要に応じてインデックスのオンデマンド更新を呼び出す。 

##<a id="CreateDataSource"></a>手順 1: データ ソースを作成する

HTTP POST 要求を発行して、次の要求ヘッダーを含む新しいデータ ソースを Azure Search サービスに作成します。
    
    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

`api-version` は必須です。有効な値には `2015-02-28` 以降のバージョンが含まれます。

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

- **name**: データ ソースの名前。

- **type**: `documentdb` を使用。

- **credentials**:

    - **connectionString**: 必須。次の形式で Azure DocumentDB データベースへの接続情報を指定します。`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **container**:

    - **name**: 必須。インデックスを作成する DocumentDB コレクションを指定します。 

    - **query**: 省略可能。任意の JSON ドキュメントを、Azure Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。

- **dataChangeDetectionPolicy**: 省略可能。以下の[データ変更の検出ポリシーに関するセクション](#DataChangeDetectionPolicy)を参照してください。

- **dataDeletionDetectionPolicy**: 省略可能。以下の[データ削除の検出ポリシーに関するセクション](#DataDeletionDetectionPolicy)を参照してください。

###<a id="DataChangeDetectionPolicy"></a>ドキュメントの変更のキャプチャ

データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。現在、唯一サポートされているポリシーは、次のような、DocumentDB によって指定される `_ts` 最終更新タイムスタンプ プロパティを使用した `High Water Mark` ポリシーです。

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts" 
    } 

さらに、クエリのプロジェクションと `WHERE` 句にも `_ts` を追加する必要があります。次に例を示します。

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>ドキュメントの削除のキャプチャ

ソース テーブルから行が削除されると、検索インデックスからも同様に行を削除する必要があります。データ削除の検出ポリシーの目的は、削除されたデータ項目を効率的に識別することです。現在、唯一サポートされているポリシーは、`Soft Delete` ポリシー (削除されると何らかのフラグでマークされる) のみです。このポリシーは、次のように指定します。

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a document as deleted" 
    }

> [AZURE.NOTE]カスタムのプロジェクションを使用している場合は、SELECT 句にプロパティを含める必要があります。

###<a id="CreateDataSourceExample"></a>要求本文の例

次の例では、カスタム クエリとポリシーのヒントと一緒にデータ ソースを作成します。

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark" 
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###Response

データ ソースが正常に作成された場合は、HTTP 201 Created 応答を受け取ります。

##<a id="CreateIndex"></a>手順 2: インデックスを作成する

ターゲットの Azure Search インデックスがまだない場合は、インデックスを作成します。これは、[Azure ポータル UI](../search-get-started.md#test-service-operations) から、または[インデックス作成 API](https://msdn.microsoft.com/library/azure/dn798941.aspx) を使用して行うことができます。

	POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
	Content-Type: application/json
	api-key: [Search service admin key]


ターゲット インデックスのスキーマがソース JSON ドキュメントのスキーマまたはカスタムのクエリ プロジェクションの出力と互換性があることを確認します。

###図 A: JSON データ型と Azure Search データ型間のマッピング

<table style="font-size:12">
    <tr>
        <th>JSON データ型</th>
        <th>互換性のあるターゲット インデックス フィールドの型</th>
    </tr>
    <tr>
        <td>ブール値</td>
        <td>Edm.Boolean、Edm.String</td>
    </tr>
    <tr>
        <td>整数などの数値</td>
        <td>Edm.Int32、Edm.Int64、Edm.String</td>
    </tr>
    <tr>
        <td>浮動小数点などの数値</td>
        <td>Edm.Double、Edm.String</td>
    </tr>
    <tr>
        <td>String</td>
        <td>Edm.String</td>
    </tr>
    <tr>
        <td>
            プリミティブ型の配列<br/>
            例: [ "a", "b", "c" ]
        </td>
        <td>Collection(Edm.String)</td>
    </tr>
    <tr>
        <td>日付などの文字列</td>
        <td>Edm.DateTimeOffset、Edm.String</td>
    </tr>
    <tr>
        <td>
            GeoJSON オブジェクト<br/>
            例: { "type": "Point", "coordinates": [ long, lat ] }
        </td>
        <td>Edm.GeographyPoint</td>
    </tr>
    <tr>
        <td>その他の JSON オブジェクト</td>
        <td>該当なし</td>
    </tr>
</table>

###<a id="CreateIndexExample"></a>要求本文の例

次の例では、ID と説明のフィールドを持つインデックスを作成します。

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###Response

インデックスが正常に作成された場合、HTTP 201 Created 応答を受け取ります。

##<a id="CreateIndexer"></a>手順 3: インデクサーを作成する

HTTP POST 要求を次のヘッダーと共に使用して、Azure Search サービス内に新しいインデクサーを作成できます。
    
    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

要求の本文には、次のフィールドを含むインデクサーの定義が含まれている必要があります。

- **name**: 必須。インデクサーの名前。

- **dataSourceName**: 必須。既存のデータ ソースの名前。

- **targetIndexName**: 必須。既存のインデックスの名前。

- **schedule**: 省略可能。以下の[インデックス作成のスケジュールに関するセクション](#IndexingSchedule)を参照してください。

###<a id="IndexingSchedule"></a>スケジュールに従ったインデクサーの実行

インデクサーには、必要に応じてスケジュールを指定できます。スケジュールが存在する場合、インデクサーはスケジュールに従って定期的に実行されます。スケジュールには次の属性があります。

- **interval**: 必須。インデクサーが実行される間隔または期間を指定する時間の値。許可される最短の間隔は 5 分です。最長は 1 日です。XSD "dayTimeDuration" 値 ([ISO 8601 期間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。使用されるパターンは、`P(nD)(T(nH)(nM))` です。たとえば、15 分ごとの場合は `PT15M`、2 時間ごとの場合は `PT2H` です。 

- **startTime**: 必須。インデクサーの実行を開始する時刻を指定する UTC 日時。

###<a id="CreateIndexerExample"></a>要求本文の例

次の例では、UTC 2015 年 1 月 1 日から 1 時間ごとに実行されるスケジュールで `myDocDbDataSource` データ ソースによって参照されるコレクションのデータを `mySearchIndex` インデックスにコピーするインデクサーを作成します。

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###Response

インデクサーが正常に作成された場合、HTTP 201 Created 応答を受け取ります。

##<a id="RunIndexer"></a>手順 4: インデクサーを実行する

スケジュールに従って定期的に実行されるだけでなく、次の HTTP POST 要求を発行して、オンデマンドでインデクサーを呼び出すこともできます。

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###Response

インデクサーが正常に呼び出された場合、HTTP 202 Accepted 応答を受け取ります。

##<a name="GetIndexerStatus"></a>手順 5: インデクサーの状態を取得する

HTTP GET 要求を発行して、インデクサーの現在の状態と実行の履歴を取得することができます。

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###Response

インデクサーの全体的な正常性状態、インデクサーの最後の呼び出し、および (存在する場合は) インデクサー呼び出しの最近の履歴に関する情報が含まれる応答の本文と共に、HTTP 200 OK 応答が表示されます。

応答は、次のようになります。

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

実行履歴には、最近完了した 50 件の実行内容が含まれ、時系列の逆の順に並べられます (そのため、最近の実行内容が応答の最初に表示されます)。

##<a name="NextSteps"></a>次のステップ

ご利用ありがとうございます。 DocumentDB のインデクサーを使用して、Azure DocumentDB を Azure Search と統合する方法についての説明は以上で終了です。

 - Azure DocumentDB の詳細については、[ここ](/services/documentdb/)をクリックしてください。

 - Azure Search の詳細については、[ここ](/services/search/)をクリックしてください。
 

<!---HONumber=July15_HO3-->