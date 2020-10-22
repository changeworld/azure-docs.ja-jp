---
title: BLOB インデクサーを構成する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でのフルテキスト検索操作用に BLOB コンテンツのインデックス付けを自動化するように Azure Blob インデクサーを設定します。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e3419711c9a7358914f85574f6dbd5af29def1cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403614"
---
# <a name="how-to-configure-a-blob-indexer-in-azure-cognitive-search"></a>Azure Cognitive Search で BLOB インデクサーを使用して BLOB を構成する方法

この記事では、Azure Cognitive Search を使用して、Azure BLOB ストレージに格納されているテキストベースのドキュメント (PDF や Microsoft Office ドキュメント、その他のよく使用されている形式など) のインデックスを作成する方法を示します。 まず、BLOB インデクサーの設定と構成の基礎を説明します。 次に、発生する可能性のある動作とシナリオについて詳しく説明します。

<a name="SupportedFormats"></a>

## <a name="supported-formats"></a>サポートされるフォーマット

BLOB インデクサーは、次の形式のドキュメントからテキストを抽出できます。

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="set-up-blob-indexing"></a>BLOB インデックスの設定

Azure Blob Storage インデクサーを設定するには、以下を使用します。

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> フィールド マッピングなど、機能によってはまだポータルで使用できないものがあります。こうした機能についてはプログラムで使用する必要があります。
>

ここでは、REST API を使用したフローについて説明します。

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

データ ソースでは、インデックスを作成するデータ、データにアクセスするために必要な資格情報、およびデータの変更 (新しい行、変更された行、削除された行) を効率よく識別するためのポリシーを指定します。 データ ソースは、同じ Search サービス内の複数のインデクサーで使用できます。

BLOB インデックス作成の場合は、次の必須プロパティがデータ ソースに必要です。

* **name** は、Search サービス内のデータ ソースの一意の名前です。
* **type** は `azureblob` である必要があります。
* **credentials は、ストレージ アカウントの接続文字列を `credentials.connectionString` パラメーターとして提供します。 詳しくは、後述の「[資格情報を指定する方法](#Credentials)」をご覧ください。
* **container** は、ストレージ アカウントにあるコンテナーを指定します。 既定では、コンテナー内のすべての BLOB を取得できます。 特定の仮想ディレクトリにある BLOB についてのみインデックスを作成する場合は、オプションの **query** パラメーターを使用してそのディレクトリを指定できます。

データ ソースを作成する方法を次に示します。

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }
```

データ ソース作成 API の詳細については、「 [データ ソースの作成](/rest/api/searchservice/create-data-source)」をご覧ください。

<a name="Credentials"></a>

#### <a name="how-to-specify-credentials"></a>資格情報を指定する方法

次のいずれかの方法で BLOB コンテナーに対して資格情報を指定できます。

* **マネージド ID の接続文字列**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 

  この接続文字列にはアカウント キーは必要ありませんが、[マネージド ID を使用して、Azure Storage アカウントへの接続を設定する](search-howto-managed-identities-storage.md)方法に関する指示に従う必要があります。

* **フル アクセス ストレージ アカウントの接続文字列**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`

  この接続文字列は、ストレージ アカウント ブレードに移動し、[設定]、[キー] と選択する (クラシック ストレージ アカウントの場合) か、[設定]、[アクセス キー] と選択する (Azure Resource Manager ストレージ アカウントの場合) ことで Azure Portal から取得できます。

* **ストレージ アカウントの Shared Access Signature** (SAS) の接続文字列: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`

  SAS にはコンテナー上およびオブジェクト (この場合は BLOB) にリストおよび読み取りアクセス許可が必要です。

* **コンテナーの Shared Access Signature**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`

  SAS にはコンテナー上にリストおよび読み取りアクセス許可が必要です。

ストレージの共有アクセス署名の詳細については、「[Shared Access Signature (SAS) の使用](../storage/common/storage-sas-overview.md)」を参照してください。

> [!NOTE]
> SAS の資格情報を使用する場合は、その有効期限が切れないように、データ ソースの資格情報を更新された署名で定期的に更新する必要があります。 SAS の資格情報の有効期限が切れると、インデクサーは「`Credentials provided in the connection string are invalid or have expired.`」のようなエラー メッセージで失敗します。  

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

ここでは、BLOB から抽出されたテキストを格納するために、検索可能な `content` フィールドを含むインデックスを作成する方法を示します。

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

詳細については、[インデックスの作成 (REST API)](/rest/api/searchservice/create-index) に関する記事をご覧ください。

### <a name="step-3-create-an-indexer"></a>手順 3:インデクサーの作成

インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

詳細については、[インデクサーの作成 (REST API)](/rest/api/searchservice/create-indexer) に関するページをご覧ください。 インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-blobs-are-indexed"></a>BLOB のインデックスを付ける方法

[インデクサー構成](#PartsOfBlobToIndex)に応じて、BLOB インデクサーでは、ストレージ メタデータのみ (メタデータのみに注意すればよく、BLOB のコンテンツにインデックスを作成する必要がないときに便利です)、ストレージとコンテンツ メタデータ、またはメタデータとテキスト コンテンツの両方にインデックスを作成することができます。 インデクサーは、既定では、メタデータとコンテンツの両方を抽出します。

> [!NOTE]
> 既定では、JSON や CSV などの構造化コンテンツを持つ BLOB には、1 つのテキスト チャンクとしてインデックスが作成されます。 構造化された方法で JSON および CSV の BLOB のインデックスを作成する場合は、[JSON BLOB のインデックス作成に関するページ](search-howto-index-json-blobs.md)と [CSV BLOB のインデックス作成に関するページ](search-howto-index-csv-blobs.md)で詳細を確認してください。
>
> 複合ドキュメントや埋め込みドキュメント (ZIP アーカイブ、添付ファイルを含む Outlook メールが埋め込まれた Word 文書、添付ファイルを含む .MSG ファイルなど) も、1 つのドキュメントとしてインデックスが作成されます。 例えば、.MSG ファイルの添付ファイルから抽出されたすべての画像が normalized_images フィールドに返されます。

* ドキュメントのテキスト コンテンツが、`content` という名前の文字列フィールドに抽出されます。

  > [!NOTE]
  > どれだけのテキストが抽出されるかは、価格レベルに応じて Azure Cognitive Search によって制限されます。Free レベルの場合は 32,000 文字、Basic の場合は 64,000 文字、Standard の場合は 400 万文字、Standard S2 の場合は 800 万文字、Standard S3 の場合は 1,600 万文字です。 切り捨てられたドキュメントについては、インデクサーの状態の応答に警告が含められます。  

* ユーザー指定のメタデータのプロパティが BLOB に存在する場合、それらのプロパティは、そのまま抽出されます。 これには、BLOB のメタデータ キーと同じ名前のフィールドが、インデックスで定義されている必要があることに注意してください。 たとえば、BLOB に値が `High` のメタデータ キー `Sensitivity` がある場合、検索インデックスで `Sensitivity` という名前のフィールドが定義されている必要があり、値 `High` が設定されます。

* 標準的な BLOB のメタデータのプロパティは、次のフィールドに抽出されます。

  * **metadata\_storage\_name** (Edm.String) - BLOB のファイル名。 たとえば、/my-container/my-folder/subfolder/resume.pdf という BLOB がある場合、このフィールドの値は `resume.pdf` になります。

  * **metadata\_storage\_path** (Edm.String) - ストレージ アカウントを含む、BLOB の完全な URI。 たとえば、`https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf` のように指定します。

  * **metadata\_storage\_content\_type** (Edm.String) - BLOB をアップロードするためのコードで指定したコンテンツ タイプ。 たとえば、「 `application/octet-stream` 」のように入力します。

  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) - 前回変更時の BLOB のタイムスタンプ。 インデックスの初回作成後に最初から作成し直さなくても済むよう、変更された BLOB を Azure Cognitive Search が特定するために、このタイムスタンプが使用されます。

  * **metadata\_storage\_size** (Edm.Int64) - BLOB のサイズ (バイト単位)。

  * **metadata\_storage\_content\_md5** (Edm.String) - BLOB コンテンツの MD5 ハッシュ (利用可能な場合)。

  * **metadata\_storage\_sas\_token** (Edm.String) - BLOB に対してアクセスするために、[カスタム スキル](cognitive-search-custom-skill-interface.md)で使用できる一時的な SAS トークン。 このトークンは、有効期限が切れる可能性があるため、後で使用するために保存しないでください。

* 各ドキュメント形式に固有のメタデータのプロパティは、[こちら](#ContentSpecificMetadata)に記載したフィールドに抽出されます。

検索インデックスに対し、ここに挙げたすべてのプロパティのフィールドを定義する必要はありません。実際のアプリケーションで必要となるプロパティだけを取り込んでください。

> [!NOTE]
> 既存のインデックス内のフィールド名が、ドキュメントの抽出過程で生成されたフィールド名と異なることは少なくありません。 Azure Cognitive Search によって提供されたプロパティ名は、**フィールドのマッピング**を使用して、検索インデックス内のフィールド名にマッピングすることができます。 フィールドのマッピングの例を次に示します。
>

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>ドキュメント キーとフィールド マッピングの定義

Azure Cognitive Search では、ドキュメントがそのキーによって一意に識別されます。 それぞれの検索インデックスに、Edm.String 型のキー フィールドが 1 つだけ存在している必要があります。 キー フィールドは、インデックスに追加するドキュメントごとに必要となります (唯一の必須フィールド)。  

抽出されたフィールドとインデックスのキー フィールドとのマッピングは、慎重に検討する必要があります。 その例を次に示します。

* **metadata\_storage\_name** - 名前をキーにできればそれに越したことはありませんが、1) 同じ名前の BLOB が別のフォルダーに存在し、名前が重複する可能性があること、2) ドキュメント キーに無効な文字 (ダッシュなど) が名前に含まれている可能性があることに注意する必要があります。 無効な文字は、`base64Encode`[フィールド マッピング関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用して処理できます。その場合、API 呼び出し (Lookup など) にドキュメント キーを渡すときに必ずエンコードしてください。 (たとえば、.NET であれば [UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) メソッドを利用できます)。

* **metadata\_storage\_path** - 完全パスであれば一意性は保証されます。ただし、パスに使われる `/` 文字は、[ドキュメント キーでは無効](/rest/api/searchservice/naming-rules)です。  この場合も、`base64Encode`[関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用してキーをエンコードできます。

* 3 番目のオプションは、カスタム メタデータ プロパティを BLOB に追加する方法です。 ただし、この方法を選んだ場合、BLOB のアップロード プロセスで、該当するメタデータのプロパティをすべての BLOB に追加する必要があります。 キーは必須のプロパティであるため、そのプロパティを持たない BLOB については、インデックスが一切作成されません。

> [!IMPORTANT]
> インデックス内のキー フィールドに対して明示的なマッピングが存在しない場合、Azure Cognitive Search は自動的に `metadata_storage_path` をキーおよび Base-64 エンコード キー値として使用します (上記の 2 つ目の選択肢)。
>
>

この例では、`metadata_storage_name` フィールドをドキュメント キーにしましょう。 また、既存のインデックスには、`key` という名前のキー フィールドと、ドキュメントのサイズを格納するための `fileSize` フィールドが存在するものとします。 それらを適切に対応付けるために、インデクサーを作成または更新するときに、次のフィールド マッピングを指定します。

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

以下に示したのは、それらを反映したコードです。既存のインデクサーに対してフィールドのマッピングを追加し、キーの base-64 エンコーディングを有効にしています。

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }
```

詳細については、[フィールドのマッピングと変換](search-indexer-field-mappings.md)に関する記事をご覧ください。

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>フィールドをキーとして使用するためにエンコードする必要がある一方、検索もしたい場合は、どうすればよいでしょうか?

metadata_storage_path のようなフィールドのエンコードされたバージョンをキーとして使用する必要がある一方、そのフィールドを (エンコードせずに) 検索可能にする必要もある場合があります。 この問題を解決するには、2 つのフィールドにマップします。1 つはキーに使用されるもので、もう 1 つは検索のために使用されるものです。 次の例では、*key* フィールドにエンコードされたパスが含まれていますが、*path* フィールドはエンコードされていないので、インデックス内の検索可能フィールドとして使用されます。

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```
<a name="WhichBlobsAreIndexed"></a>

## <a name="index-by-file-type"></a>ファイルの種類でインデックスを作成する

インデックスが作成される BLOB とスキップされる BLOB を制御できます。

### <a name="include-blobs-having-specific-file-extensions"></a>特定のファイル拡張子を持つ BLOB を含める

`indexedFileNameExtensions` インデクサー構成パラメーターを使用すると、指定したファイル名拡張子を持つ BLOB のインデックスだけを作成できます。 値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。 たとえば、.PDF や .DOCX の BLOB のみのインデックスを作成する場合は、この操作を行います。

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-having-specific-file-extensions"></a>特定のファイル拡張子を持つ BLOB を除外する

`excludedFileNameExtensions` 構成パラメーターを使用すると、特定のファイル名拡張子を持つ BLOB をインデックス作成から除外できます。 値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。 たとえば、.PNG と .JPEG の拡張子を持つ BLOB を除くすべての BLOB のインデックスを作成する場合は、この操作を行います。

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

`indexedFileNameExtensions` と `excludedFileNameExtensions` の両方のパラメーターがある場合、Azure Cognitive Search では最初に `indexedFileNameExtensions` を調べ、次に `excludedFileNameExtensions` を調べます。 つまり、同じファイル拡張子が両方の一覧に存在する場合、インデックス作成から除外されます。

<a name="PartsOfBlobToIndex"></a>

## <a name="index-parts-of-a-blob"></a>BLOB のインデックス部分

BLOB のどの部分にインデックスを作成するかは、`dataToExtract` 構成パラメーターを使用して制御できます。 次の値を使用できます。

* `storageMetadata` - [標準的な BLOB のプロパティおよびユーザー指定のメタデータ](../storage/blobs/storage-blob-container-properties-metadata.md)のみにインデックスを作成するように指定します。
* `allMetadata` - ストレージ メタデータと、BLOB コンテンツから抽出された[コンテンツの種類固有のメタデータ](#ContentSpecificMetadata)にインデックスを作成するように指定します。
* `contentAndMetadata` - すべてのメタデータと、BLOB から抽出されたテキスト コンテンツにインデックスを作成するように指定します。 これが既定値です。

たとえば、ストレージ メタデータのみにインデックスを作成するには、次のように使用します。

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }
```

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>BLOB のメタデータを使用した BLOB インデックスの作成方法の制御

上記の構成パラメーターは、すべての BLOB に適用されます。 ときには、"*個々の BLOB*" のインデックスの作成方法を制御することが必要になる場合があります。 これを行うには、次の BLOB メタデータのプロパティと値を追加します。

| プロパティ名 | プロパティ値 | 説明 |
| --- | --- | --- |
| AzureSearch_Skip |"true" |BLOB を完全にスキップするように BLOB インデクサーに指示します。 メタデータとコンテンツのどちらの抽出も行われません。 特定の BLOB で何度もエラーが発生し、インデックス作成プロセスが中断されるときに利用できます。 |
| AzureSearch_SkipContent |"true" |これは、[前](#PartsOfBlobToIndex)に説明した、特定の BLOB を対象とする `"dataToExtract" : "allMetadata"` 設定と同じです。 |

## <a name="index-from-multiple-sources"></a>複数のソースからインデックスを作成する

インデックスの複数のソースからドキュメントを「アセンブル」できます。 たとえば、Cosmos DB に格納された他のメタデータを使用して BLOB からテキストをマージすることもできます。 プッシュ インデックス作成 API を各種インデクサーとともに使用して、複数のパーツから検索ドキュメントを構築することもできます。

これが機能するには、すべてのインデクサーと他のコンポーネントがドキュメント キーに同意する必要があります。 このトピックの詳細については、[複数の Azure データ ソースのインデックスを作成する](./tutorial-multiple-data-sources.md)方法に関するページ、または[ドキュメントを Azure Cognitive Search の他のデータと組み合わせる](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)方法に関するこちらのブログ記事をご覧ください。

## <a name="index-large-datasets"></a>大規模なデータセットにインデックスを付ける

BLOB のインデックス作成プロセスは、時間がかかる場合があります。 インデックスを作成する BLOB が数百万ある場合は、データをパーティション分割し、複数のインデクサーを使用してデータを並列で処理することで、インデックス作成を高速に処理できます。 設定方法は次のとおりです。

* 複数の BLOB コンテナーまたは仮想フォルダーにデータをパーティション分割します。

* コンテナーまたはフォルダーごとに 1 つずつ、Azure Cognitive Search のデータ ソースを設定します。 BLOB フォルダーをポイントするには、`query` パラメーターを使用します。

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

* データ ソースごとに対応するインデクサーを作成します。 すべてのインデクサーから、同じターゲット検索インデックスをポイントできます。  

* サービス内の 1 つの検索単位は、特定の時点で 1 つのインデクサーを実行できます。 上記のように、複数のインデクサーの作成は、これらを実際に並行して実行する場合のみ有用です。 複数のインデクサーを並行して実行するには、適切な数のパーティションとレプリカを作成して、検索サービスをスケールアウトします。 たとえば、検索サービスに 6 つの検索単位がある場合 (たとえば、2 つのパーティション x 3 つのレプリカ)、6 つのインデクサーを同時に実行でき、インデックス作成のスループットが 6 倍になります。 スケーリングと容量計画の詳細については、[Azure Cognitive Search サービスの容量を調整する](search-capacity-planning.md)方法に関するページを参照してください。

<a name="DealingWithErrors"></a>

## <a name="handle-errors"></a>エラーの処理

既定では、BLOB インデクサーは、サポートされていないコンテンツの種類 (画像など) が含まれる BLOB を検出するとすぐに停止されます。 もちろん、`excludedFileNameExtensions` パラメーターを使用して特定のコンテンツの種類をスキップすることもできますが、 存在する可能性のあるすべてのコンテンツの種類が事前にわからないまま BLOB のインデックスを作成する必要がある場合もあります。 サポートされていないコンテンツの種類が検出されたときにインデックス作成を続行するには、`failOnUnsupportedContentType` 構成パラメーターを `false` に設定します。

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

一部の BLOB では、Azure Cognitive Search でコンテンツの種類を判別できないか、他の種類ではサポートされているコンテンツの種類のドキュメントを処理できない場合があります。 この障害モードを無視するには、`failOnUnprocessableDocument` 構成パラメーターを false に設定します。

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Cognitive Search では、インデックスを付ける BLOB のサイズが制限されます。 これらの制限は、[Azure Cognitive Search サービスの制限](./search-limits-quotas-capacity.md)に関する記事で文書化されています。 サイズが大きい BLOB は、既定ではエラーとして扱われます。 ただし、`indexStorageMetadataOnlyForOversizedDocuments` 構成パラメーターを true に設定した場合、サイズが大きい BLOB のストレージ メタデータには引き続きインデックスを付けることができます。

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

BLOB の解析中またはインデックスへのドキュメントの追加中、処理のどこかの時点でエラーが発生した場合に、インデックス付けを続行することもできます。 特定数のエラーを無視するには、構成パラメーター `maxFailedItems` と `maxFailedItemsPerBatch` を望ましい値に設定します。 次に例を示します。

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

<a name="ContentSpecificMetadata"></a>

## <a name="content-type-specific-metadata-properties"></a>コンテンツの種類ごとのメタデータのプロパティ

以下の表は、各ドキュメント形式に関して実行される処理と、Azure Cognitive Search によって抽出されるメタデータのプロパティをまとめたものです。

| ドキュメントの形式/コンテンツの種類 | 抽出されるメタデータ | 処理の詳細 |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML マークアップを削除し、テキストを抽出します。 |
| PDF (application/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります (画像を除く)。 |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| DOCM (application/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| WORD XML (application/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML マークアップを削除し、テキストを抽出します。 |
| WORD 2003 XML (application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML マークアップを削除し、テキストを抽出します。 |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| XLSM (application/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPTM (application/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |テキスト (添付ファイルから抽出されたテキストを含む) を抽出します。 `metadata_message_to_email`、`metadata_message_cc_email`、`metadata_message_bcc_email` は文字列コレクションであり、残りのフィールドは文字列です。|
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ODS (application/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ODP (application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ZIP (application/zip) |`metadata_content_type` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| GZ (application/gzip) |`metadata_content_type` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| EPUB (application/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |XML マークアップを削除し、テキストを抽出します。 |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |テキストを抽出します<br/>注:JSON BLOB から複数のドキュメント フィールドを抽出する必要がある場合、詳細については、[JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)に関する記事をご覧ください |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |テキストを抽出します。添付ファイルも対象となります。 |
| RTF (アプリケーション/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | テキストを抽出します|
| プレーン テキスト (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | テキストを抽出します|

## <a name="see-also"></a>関連項目

* [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
* [AI を使用して BLOB を理解する](search-blob-ai-integration.md)
* [BLOB のインデックス作成の概要](search-blob-storage-integration.md)