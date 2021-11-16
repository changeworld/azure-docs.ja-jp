---
title: Azure Files のインデックス データ (プレビュー)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でファイル共有のインデックス作成を自動化するように Azure Files インデクサーを設定します。
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 37f33bb17b48f482f27a449871acc7256713c100
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157562"
---
# <a name="index-data-from-azure-files"></a>Azure Files のインデックス データ

> [!IMPORTANT] 
> Azure Files は、現在、[追加の使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下でパブリック プレビュー段階にあります。 [プレビュー REST API (2021-04-30-preview 以降)](search-api-preview.md) を使用して、コンテンツのインデックスを作成します。 現時点で、ポータルによるサポートは制限されており、.NET SDK によるサポートはありません。

この記事では、Azure ファイル共有からコンテンツとメタデータを抽出し、それを Azure Cognitive Search の検索インデックスに送信するための基本的なワークフローについて説明します。 結果のインデックスには、フルテキスト検索を使用してクエリを実行できます。

> [!NOTE]
> ワークフローとコンポジションについて既によく理解している場合、 次の手順は「[ファイル インデクサーを構成する方法](#configure)」です。

## <a name="functionality"></a>機能

Azure Cognitive Search のインデクサーは、データ ソースから検索可能なデータとメタデータを抽出するクローラーです。 Azure Files インデクサーは、Azure ファイル共有とインデックス ファイルに接続します。 インデクサーには以下の機能があります。

+ Azure ファイル共有からコンテンツのインデックスを作成します。
+ インデクサーでは、インデックスの増分作成がサポートされています。つまり、Azure ファイル共有内の変更されたコンテンツが特定され、それ以降のインデックス作成の実行時には更新されたコンテンツのインデックスのみが作成されます。 たとえば、最初に 5 つの PDF のインデックスがインデクサーによって作成され、その後に 1 つが更新され、それからインデクサーが再度実行された場合、更新された 1 つの PDF のインデックスのみがインデクサーによって作成されます。
+ インデックスが作成されるファイルからは、既定でテキストと正規化された画像が抽出されます。 必要に応じて、さらにコンテンツをエンリッチするためにスキルセットをパイプラインに追加できます。 スキルセットの詳細については、記事「[Azure Cognitive Search のスキルセットの概念](cognitive-search-working-with-skillsets.md)」を参照してください。

## <a name="supported-document-formats"></a>サポートされるドキュメントの形式

Azure Cognitive Search の Azure Files インデクサーは、次の形式のドキュメントからテキストを抽出できます。

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="required-resources"></a>必要なリソース

Azure Cognitive Search と [Azure Files](https://azure.microsoft.com/services/storage/files/) の両方が必要です。 Azure Files 内に、ソース コンテンツを提供するファイル共有が必要です。

> [!NOTE]
> ファイル共有のインデックスを作成するには、その共有で、[ファイル データ プレーン REST API](/rest/api/storageservices/file-service-rest-api) を使用したアクセスがサポートされている必要があります。 [NFS 共有](../storage/files/files-nfs-protocol.md#support-for-azure-storage-features)ではファイル データ プレーン REST API がサポートされていないため、Azure Cognitive Search インデクサーを使用できません。[SMB 共有](../storage/files/files-smb-protocol.md)ではファイル データ プレーン REST API がサポートされているため、Azure Cognitive Search インデクサーを使用できます。

<a name="configure"></a>

## <a name="configuring-a-file-indexer"></a>ファイル インデクサーの構成

Azure Files インデクサーは、多くの一般的な構成オプションを [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)と共有しています。 たとえば、Azure Files インデクサーでは、[1 つのファイル](search-howto-index-one-to-many-blobs.md)、[プレーンテキスト ファイル](search-howto-index-plaintext-blobs.md)、[JSON ファイル](search-howto-index-json-blobs.md)、[暗号化されたファイル](search-howto-index-encrypted-blobs.md)から複数の検索ドキュメントを生成できます。 また、多くの同じ[構成オプション](search-howto-indexing-azure-blob-storage.md)が適用されます。 重要な違いを以下に示します。

## <a name="data-source-definitions"></a>データ ソース定義

ファイル インデクサーとその他のインデクサーの主な違いは、インデクサーに割り当てられるデータ ソース定義です。 データ ソース定義では、データ ソースの種類 ("type": "azurefile") と、インデックスを作成するコンテンツへの認証と接続に使用するその他のプロパティを指定します。

ファイルのデータ ソース定義は、次の例のようになります。

```http
{
    "name" : "my-file-datasource",
    "type" : "azurefile",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-file", "query" : "<optional-directory-name>" }
}
```

`"credentials"` プロパティは、上の例に示すような接続文字列か、次のセクションで説明する別の方法のいずれかで指定できます。 `"container"` プロパティで Azure Storage 内のファイル共有を指定し、`"query"` を使用して共有内のサブフォルダーを指定します。 データ ソース定義の詳細については、「[データ ソースの作成 (REST)](/rest/api/searchservice/create-data-source)」を参照してください。

<a name="Credentials"></a>

## <a name="credentials"></a>資格情報

次のいずれかの方法でファイル共有の資格情報を指定できます。

**マネージド ID の接続文字列**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

この接続文字列にはアカウント キーは必要ありませんが、[マネージド ID を使用して、Azure Storage アカウントへの接続を設定する](search-howto-managed-identities-storage.md)方法に関する指示に従う必要があります。

**フル アクセス ストレージ アカウントの接続文字列**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

この接続文字列は、ストレージ アカウント ブレードに移動し、[設定] > [キー] と選択する (クラシック ストレージ アカウントの場合) か、[セキュリティとネットワーク] > [アクセス キー] と選択する (Azure Resource Manager ストレージ アカウントの場合) ことで Azure portal から取得できます。

**ストレージ アカウントの Shared Access Signature** (SAS) の接続文字列: `{ "connectionString" : "BlobEndpoint=https://<your account>.file.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&sp=rl&sr=s;" }`

SAS には、ファイル共有に対する一覧表示および読み取りアクセス許可が必要です。

**コンテナーの Shared Access Signature**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.file.core.windows.net/<share name>?sv=2016-05-31&sr=s&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

SAS には、ファイル共有に対する一覧表示および読み取りアクセス許可が必要です。 ストレージの共有アクセス署名の詳細については、「[Shared Access Signature (SAS) の使用](../storage/common/storage-sas-overview.md)」を参照してください。

> [!NOTE]
> SAS の資格情報を使用する場合は、その有効期限が切れないように、データ ソースの資格情報を更新された署名で定期的に更新する必要があります。 SAS の資格情報の有効期限が切れた場合、インデクサーは失敗し、「接続文字列で指定された資格情報が無効か期限が切れています」のようなエラー メッセージが表示されます。

## <a name="indexing-file-metadata"></a>ファイル メタデータのインデックス作成

任意のコンテンツの種類を含むファイルの並べ替えを容易にする一般的なシナリオは、各ファイルでカスタム メタデータとシステム プロパティの両方のインデックスを作成することです。 そうすることで、検索サービス内のインデックスに格納されているドキュメントの種類に関係なく、すべてのファイルの情報のインデックスが作成されます。 新しいインデックスを使用すれば、すべてのファイル ストレージ コンテンツの並べ替え、フィルター処理、ファセット処理を行うことができます。

標準的なファイル メタデータ プロパティを下記のフィールドに抽出できます。 ファイル インデクサーにより、これらのファイル メタデータ プロパティの内部フィールド マッピングが自動的に作成されます。 なお、インデックス定義を使用するには、必要なフィールドを追加する必要がありますが、インデクサーでのフィールド マッピングの作成は省略できます。

+ **metadata_storage_name** (`Edm.String`) - ファイル名。 たとえば、/my-share/my-folder/subfolder/resume.pdf というファイルがある場合、このフィールドの値は `resume.pdf` になります。
+ **metadata_storage_path** (`Edm.String`) - ストレージ アカウントを含む、ファイルの完全な URI。 たとえば、`https://myaccount.file.core.windows.net/my-share/my-folder/subfolder/resume.pdf` のように指定します。
+ **metadata_storage_content_type** (`Edm.String`) - ファイルをアップロードするためのコードで指定したコンテンツ タイプ。 たとえば、「 `application/octet-stream` 」のように入力します。
+ **metadata_storage_last_modified** (`Edm.DateTimeOffset`) - 前回変更時のファイルのタイムスタンプ。 インデックスの初回作成後に最初から作成し直さなくても済むよう、変更されたファイルを Azure Cognitive Search が特定するために、このタイムスタンプが使用されます。
+ **metadata_storage_size** (`Edm.Int64`) - ファイルのサイズ (バイト単位)。
+ **metadata_storage_content_md5** (`Edm.String`) - ファイル コンテンツの MD5 ハッシュ (利用可能な場合)。
+ **metadata_storage_sas_token** (`Edm.String`) - ファイルにアクセスするために[カスタム スキル](cognitive-search-custom-skill-interface.md)で使用できる一時的な SAS トークン。 このトークンは、有効期限が切れる可能性があるため、後で使用するために保存しないでください。

## <a name="index-by-file-type"></a>ファイルの種類でインデックスを作成する

インデックスが作成されるドキュメントとスキップされるドキュメントを制御できます。

### <a name="include-documents-having-specific-file-extensions"></a>特定のファイル拡張子のドキュメントを含める

`indexedFileNameExtensions` インデクサー構成パラメーターを使用すると、指定したファイル名拡張子のドキュメントのインデックスだけを作成できます。 値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。 たとえば、.PDF と .DOCX のドキュメントのみのインデックスを作成する場合は、この操作を行います。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>特定のファイル拡張子のドキュメントを除外する

`excludedFileNameExtensions` 構成パラメーターを使用すると、特定のファイル名拡張子のドキュメントをインデックス作成から除外できます。 値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。 たとえば、.PNG と .JPEG という拡張子のものを除くすべてのコンテンツのインデックスを作成する場合は、この操作を行います。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

`indexedFileNameExtensions` と `excludedFileNameExtensions` の両方のパラメーターがある場合、Azure Cognitive Search では最初に `indexedFileNameExtensions` を調べ、次に `excludedFileNameExtensions` を調べます。 つまり、同じファイル拡張子が両方の一覧に存在する場合、インデックス作成から除外されます。

<a name="deleted-files"></a>

## <a name="detecting-deleted-files"></a>削除されたファイルを検出する

最初の検索インデックスが作成された後、以降のインデクサー ジョブでは、新規および変更されたドキュメントのみを取得することができます。 検索コンテンツが Azure File Storage のものである場合、スケジュールを使用してインデックス作成をトリガーすると、変更の検出が自動的に行われます。 既定では、ファイルの `LastModified` タイムスタンプの指定に従い、変更されたファイルのインデックスのみがサービスによって再作成されます。 検索インデクサーでサポートされている他のデータ ソースとは対照的に、ファイルには常にタイムスタンプがあるため、変更検出ポリシーを手動で設定する必要がなくなります。

変更の検出は指定されていますが、削除検出は指定されていません。 削除されたファイルを検出する場合は、必ず "論理的な削除" アプローチを使用してください。 ファイルを完全に削除すると、対応するドキュメントが検索インデックスから削除されません。

## <a name="soft-delete-using-custom-metadata"></a>カスタム メタデータを使用した論理的な削除

この方法では、ファイルのメタデータを使用して、検索ドキュメントをインデックスから削除する必要があるかどうかを判断します。 この方法では、インデックスから検索ドキュメントを削除した後、Azure Storage でファイルを削除するという 2 つの個別のアクションが必要です。

ファイル ストレージと Cognitive Search の両方で実行する手順がありますが、その他の機能の依存関係はありません。 この機能は、一般公開されている API でサポートされています。

1. カスタム メタデータのキーと値のペアをファイルに追加して、そのファイルが論理的に削除されていることを Azure Cognitive Search に示します。

1. データ ソースで論理的な削除の列の検出ポリシーを構成します。 たとえば、次のポリシーでは、ファイルのメタデータ プロパティ `IsDeleted` の値が `true` のときに、そのファイルが削除されるものと見なされます。

    ```http
    PUT https://[service name].search.windows.net/datasources/file-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "file-datasource",
        "type" : "azurefile",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-share", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. インデクサーによって BLOB が処理され、インデックスからドキュメントが削除されたら、Azure Blob Storage の BLOB を削除できます。

### <a name="reindexing-undeleted-files-using-custom-metadata"></a>削除が取り消されたファイルのインデックスの再作成 (カスタム メタデータを使用)

削除されたファイルがインデクサーによって処理され、対応する検索ドキュメントがインデックスから削除された後、BLOB の `LastModified` タイムスタンプが最後に実行されたインデクサーよりも古い場合、後でそれを復元しても、そのファイルにもう一度アクセスすることはありません。

そのドキュメントのインデックスを再作成する場合は、そのファイルの `"softDeleteMarkerValue" : "false"` を変更して、インデクサーを再実行します。

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
+ [Azure Files とは何ですか。](../storage/files/storage-files-introduction.md)
