<properties
pageTitle="Azure Blob Storage のインデックスを Azure Search で作成する"
description="Azure Search で Azure Blob Storage のインデックスを作成し、ドキュメントからテキストを抽出する方法について説明します。"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="07/12/2016"
ms.author="eugenesh" />

# Azure Blob Storage 内ドキュメントのインデックスを Azure Search で作成する

この記事では、Azure Search を使用して、Azure Blob Storage に格納されているドキュメント (PDF や Microsoft Office ドキュメント、その他のよく使用されている形式など) のインデックスを作成する方法を説明します。Azure Search の新しい BLOB インデクサーを使用すると、迅速かつシームレスに作成できます。

> [AZURE.IMPORTANT] 現在この機能はプレビュー版です。バージョン **2015-02-28-Preview** を使用した REST API でのみ利用できます。プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。

## BLOB インデックスの設定

Azure Blob Storage のインデクサーの設定と構成は、[こちらの記事](https://msdn.microsoft.com/library/azure/dn946891.aspx)の説明に従い、Azure Search REST API を使用して**インデクサー**と**データ ソース**を作成、管理することによって行います。今後は、Azure Search .NET SDK と Azure ポータルに BLOB インデックスの作成機能が追加される予定です。

データ ソースでは、インデックスを作成するデータ、データにアクセスするために必要な資格情報、および Azure Search がデータの変更 (新しい行、変更された行、削除された行) を効率よく識別できるようにするポリシーを指定します。データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

インデクサーは、データ ソースと検索対象のインデックスをつなげるリソースです。

BLOB インデックス作成を設定するには、次の手順に従います。

1. Azure ストレージ アカウント内のコンテナー (と必要に応じてそのコンテナー内のフォルダー) を参照する `azureblob` タイプのデータ ソースを作成します。
	- ストレージ アカウントの接続文字列を `credentials.connectionString` パラメーターとして渡します。接続文字列は、Azure ポータルから取得できます。目的のストレージ アカウント ブレードに移動して [キー] を選択し、"プライマリ接続文字列" または "セカンダリ接続文字列" の値を使用します。
	- コンテナー名を指定します。必要に応じて `query` パラメーターを使用し、フォルダーを対象にすることもできます。
2. 検索可能な `content` フィールドを持つ検索インデックスを作成します。
3. データ ソースをターゲット インデックスに接続することによって、インデクサーを作成します。

### データ ソースの作成

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "my-folder" }
	}   

データ ソース作成 API の詳細については、「[Create Datasource](search-api-indexers-2015-02-28-preview.md#create-data-source)」を参照してください。

### インデックスの作成 

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
  		]
	}

インデックス作成 API の詳細については、「[Create Index](https://msdn.microsoft.com/library/dn798941.aspx)」を参照してください。

### インデクサーの作成 

最後に、データ ソースとターゲット インデックスとを参照するインデクサーを作成します。次に例を示します。

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "blob-indexer",
	  "dataSourceName" : "blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。サポートされている最短の間隔は 5 分です。スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。ただし、いつでもオンデマンドでインデクサーを実行できます。

インデクサー作成 API の詳細については、「[インデクサーの作成](search-api-indexers-2015-02-28-preview.md#create-indexer)」をご覧ください。


## サポートされるドキュメントの形式

BLOB インデクサーは、次の形式のドキュメントからテキストを抽出できます。

- PDF
- Microsoft Office 形式: DOCX/DOC、XLSX/XLS、PPTX/PPT、MSG (Outlook 電子メール)
- HTML
- XML
- ZIP
- EML
- プレーン テキスト ファイル (.txt)
- JSON (詳細については、[JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)に関する記事を参照)

## ドキュメント抽出プロセス

Azure Search は、各ドキュメント (BLOB) のインデックスを次のように作成します。

- ドキュメントのテキスト コンテンツ全体が、`content` という名前の文字列フィールドに抽出されます。現時点では、1 つの BLOB から複数のドキュメントを抽出することはできません。
	- たとえば CSV ファイルは、1 つのドキュメントとしてインデックスが作成されます。CSV 内の各行を個別のドキュメントとして扱う必要がある場合は、[こちらの UserVoice の提案](https://feedback.azure.com/forums/263029-azure-search/suggestions/13865325-please-treat-each-line-in-a-csv-file-as-a-separate)に投票してください。
	- 複合ドキュメントや埋め込みドキュメント (PDF が添付された Outlook 電子メールを埋め込んだ Word 文書、ZIP アーカイブなど) も、1 つのドキュメントとしてインデックスが作成されます。

- ユーザー指定のメタデータのプロパティが BLOB に存在する場合、それらのプロパティは、そのまま抽出されます。メタデータのプロパティを使って、ドキュメント抽出プロセスをある程度制御することもできます。詳細については、「[カスタム メタデータを使ったドキュメント抽出の制御](#CustomMetadataControl)」を参照してください。

- 標準的な BLOB のメタデータのプロパティは、次のフィールドに抽出されます。

	- **metadata\_storage\_name** (Edm.String) - BLOB のファイル名。たとえば、/my-container/my-folder/subfolder/resume.pdf という BLOB がある場合、このフィールドの値は `resume.pdf` になります。

	- **metadata\_storage\_path** (Edm.String) - ストレージ アカウントを含む、BLOB の完全な URI です。たとえば、`https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf` のように指定します。

	- **metadata\_storage\_content\_type** (Edm.String) - BLOB をアップロードするためのコードで指定したコンテンツ タイプ。たとえば、「`application/octet-stream`」のように入力します。

	- **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) - 前回変更時の BLOB のタイムスタンプ。インデックスの初回作成後に最初から作成し直さなくても済むよう、変更された BLOB を Azure Search が特定するために、このタイムスタンプが使用されます。

	- **metadata\_storage\_size** (Edm.Int64) - BLOB のサイズ (バイト単位)。

	- **metadata\_storage\_content\_md5** (Edm.String) - BLOB コンテンツの MD5 ハッシュ (利用可能な場合)。

- 各ドキュメント形式に固有のメタデータのプロパティは、[こちら](#ContentSpecificMetadata)に記載したフィールドに抽出されます。

検索インデックスに対し、ここに挙げたすべてのプロパティのフィールドを定義する必要はありません。実際のアプリケーションで必要となるプロパティだけを取り込んでください。

> [AZURE.NOTE] 既存のインデックス内のフィールド名が、ドキュメントの抽出過程で生成されたフィールド名と異なることは少なくありません。Azure Search によって出力されたプロパティ名は、**フィールドのマッピング**を使用して、検索インデックス内のフィールド名に対応付けることができます。フィールドのマッピングの例を次に示します。

## ドキュメントのキー フィールドの選択と各種フィールド名の処理

Azure Search では、ドキュメントがそのキーによって一意に識別されます。それぞれの検索インデックスに、Edm.String 型のキー フィールドが 1 つだけ存在している必要があります。キー フィールドは、インデックスに追加するドキュメントごとに必要となります (唯一の必須フィールド)。
   
抽出されたフィールドとインデックスのキー フィールドとのマッピングは、慎重に検討する必要があります。その例を次に示します。

- **metadata\_storage\_name** - 名前をキーにできればそれに越したことはありませんが、1) 同じ名前の BLOB が別のフォルダーに存在し、名前が重複する可能性があること、2) ドキュメント キーに無効な文字 (ダッシュなど) が名前に含まれている可能性があることに注意する必要があります。無効な文字は、インデクサーのプロパティで `base64EncodeKeys` オプションを有効にすることによって処理することができます。その場合、API 呼び出し (Lookup など) にドキュメント キーを渡すとき、必ずエンコードしてください (たとえば、.NET であれば [UrlTokenEncode メソッド](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)を利用できます)。

- **metadata\_storage\_path** - 完全パスであれば一意性は保証されます。ただし、パスに使われる `/` 文字は、[ドキュメント キーでは無効](https://msdn.microsoft.com/library/azure/dn857353.aspx)です。この場合も、`base64EncodeKeys` オプションを使用してキーをエンコーディングすることができます。

- いずれの選択肢も利用できない場合の最終的な手段として、独自にメタデータのプロパティを BLOB に追加する方法があります。ただし、この方法を選んだ場合、BLOB のアップロード プロセスで、該当するメタデータのプロパティをすべての BLOB に追加する必要があります。キーは必須のプロパティであるため、そのプロパティを持たない BLOB については、インデックスが一切作成されません。

> [AZURE.IMPORTANT] インデックス内のキー フィールドの明示的なマッピングが存在しない場合、Azure Search は自動的に `metadata_storage_path` (上に挙げた 2 つ目の選択肢) をキーに設定し、その base-64 エンコーディングを有効にします。

この例では、`metadata_storage_name` フィールドをドキュメント キーにしましょう。また、既存のインデックスには、`key` という名前のキー フィールドと、ドキュメントのサイズを格納するための `fileSize` フィールドが存在するものとします。それらを適切に対応付けるために、インデクサーを作成または更新するときに、次のフィールド マッピングを指定します。

	"fieldMappings" : [
	  { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	  { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	]

以下に示したのは、それらを反映したコードです。既存のインデクサーに対してフィールドのマッピングを追加し、キーの base-64 エンコーディングを有効にしています。

	PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
	  "fieldMappings" : [
	    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	  ],
	  "parameters" : { "base64EncodeKeys": true }
	}

> [AZURE.NOTE] フィールドのマッピングの詳細については、[こちらの記事](search-indexer-field-mappings.md)を参照してください。

## インデックスの増分作成と削除の検出

スケジュールに従って実行するように BLOB のインデクサーを設定すると、その BLOB の `LastModified` タイムスタンプから判断された変更済みの BLOB のみインデックスが再構築されます。

> [AZURE.NOTE] 変更検出ポリシーを独自に指定する必要はありません。インデックスの増分作成は自動的に有効になります。

特定のドキュメントをインデックスから削除するよう指定する場合は、論理削除方式を使用してください。つまり、該当する BLOB を削除するのではなく、それらが削除されたことを示すカスタムのメタデータ プロパティをデータ ソースに追加し、論理削除の検出ポリシーを設定します。

> [AZURE.WARNING] 削除の検出ポリシーを使用せず単純に BLOB を削除した場合、検索インデックスからは、該当するドキュメントが削除されません。

たとえば、以下に示すポリシーでは、メタデータのプロパティ `IsDeleted` の値が `true` であるとき、BLOB が削除されたと見なされます。

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
		"container" : { "name" : "my-container", "query" : "my-folder" },
		"dataDeletionDetectionPolicy" : {
			"@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", 	
			"softDeleteColumnName" : "IsDeleted",
			"softDeleteMarkerValue" : "true"
		}
	}   

<a name="ContentSpecificMetadata"></a>
## コンテンツの種類ごとのメタデータのプロパティ

以下の表は、各ドキュメント形式に関して実行される処理と、Azure Search によって抽出されるメタデータのプロパティをまとめたものです。

ドキュメントの形式/コンテンツの種類 | コンテンツの種類ごとのメタデータのプロパティ | 処理の詳細
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | HTML マークアップを削除し、テキストを抽出します。
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| テキストを抽出します。埋め込みドキュメントも対象となります (画像を除く)。
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | テキストを抽出します。埋め込みドキュメントも対象となります。
DOC (application/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | テキストを抽出します。埋め込みドキュメントも対象となります。
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | テキストを抽出します。埋め込みドキュメントも対象となります。
XLS (application/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | テキストを抽出します。埋め込みドキュメントも対象となります。
PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | テキストを抽出します。埋め込みドキュメントも対象となります。
PPT (application/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | テキストを抽出します。埋め込みドキュメントも対象となります。
MSG (application/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | テキストを抽出します。添付ファイルも対象となります。
ZIP (application/zip) | `metadata_content_type` | アーカイブ内のすべてのドキュメントからテキストを抽出します。
XML (application/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | XML マークアップを削除し、テキストを抽出します。
JSON (application/json) | `metadata_content_type`</br>`metadata_content_encoding` | テキストを抽出します。<br/>注: JSON BLOB から複数のドキュメント フィールドを抽出する必要がある場合、詳細については、[JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)に関する記事をご覧ください。
EML (message/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | テキストを抽出します。添付ファイルも対象となります。
プレーン テキスト (text/plain) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## カスタム メタデータを使ったドキュメント抽出の制御

BLOB のインデックス作成とドキュメント抽出プロセスは、メタデータのプロパティを BLOB に追加することによって、ある程度制御することができます。現在サポートされているプロパティは次のとおりです。

プロパティ名 | プロパティ値 | 説明
--------------|----------------|------------
AzureSearch\_Skip | "true" | BLOB を完全にスキップするようそのインデクサーに指示するプロパティです。メタデータもコンテンツも、一切抽出されません。特定のコンテンツ タイプをスキップする必要があるときに利用できます。また、特定の BLOB で何度もエラーが発生し、インデックス作成プロセスが中断されるときにも利用できます。
AzureSearch\_SkipContent | "true" | メタデータのインデックス作成のみを行い、BLOB のコンテンツの抽出はスキップするように、BLOB インデクサーに指示します。これは、BLOB のコンテンツは重要ではないが、BLOB に関連付けられているメタデータのインデックス作成は必要である場合に便利です。

<a name="IndexerParametersConfigurationControl"></a>
## インデクサーのパラメーターを使用してドキュメントの抽出を制御する

いくつかのインデクサー構成パラメーターを使用すると、どの BLOB について、また BLOB のコンテンツとメタデータのどの部分についてインデックスを作成するかを制御できます。

### 特定のファイル拡張子を持つ BLOB のみのインデックスを作成する

`indexedFileNameExtensions` インデクサー構成パラメーターを使用すると、指定したファイル名拡張子を持つ BLOB のインデックスだけを作成できます。値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。たとえば、.PDF や .DOCX の BLOB のみのインデックスを作成する場合は、この操作を行います。

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
	}

### 特定のファイル拡張子を持つ BLOB をインデックス作成から除外する

`excludedFileNameExtensions` 構成パラメーターを使用すると、特定のファイル名拡張子を持つ BLOB をインデックス作成から除外できます。値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。たとえば、.PNG と .JPEG の拡張子を持つ BLOB を除くすべての BLOB のインデックスを作成する場合は、この操作を行います。

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
	}

`indexedFileNameExtensions` と `excludedFileNameExtensions` の両方のパラメーターがある場合、Azure Search では最初に `indexedFileNameExtensions` を調べ、次に `excludedFileNameExtensions` を調べます。つまり、同じファイル拡張子が両方の一覧に存在する場合、インデックス作成から除外されます。

### ストレージ メタデータのみのインデックスを作成する

`indexStorageMetadataOnly` 構成プロパティを使用すると、ストレージ メタデータのインデックスだけを作成し、ドキュメントの抽出プロセスを完全にスキップできます。これは、ドキュメントのコンテンツも不要で、コンテンツの種類に固有のメタデータ プロパティも不要な場合に便利です。これを行うには、`indexStorageMetadataOnly` プロパティを `true` に設定します。

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexStorageMetadataOnly" : true } }
	}

### ストレージとコンテンツの種類のメタデータの両方のインデックスを作成するが、コンテンツの抽出をスキップする

すべてのメタデータを抽出する必要があるが、すべての BLOB のコンテンツ抽出をスキップする場合は、`AzureSearch_SkipContent` メタデータを各 BLOB に個々に追加する代わりに、インデクサー構成を使用してこの動作を要求できます。これを行うには、`skipContent` インデクサー構成プロパティを `true` に設定します。

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "skipContent" : true } }
	}

## Azure Search の品質向上にご協力ください

ご希望の機能や品質向上のアイデアがありましたら、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。

<!---HONumber=AcomDC_0713_2016-->