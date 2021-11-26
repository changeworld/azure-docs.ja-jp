---
title: BLOB インデックス タグを使用して Azure BLOB データを管理および検索する
description: BLOB インデックス タグを使用して、BLOB オブジェクトの分類、管理、およびクエリを実行する方法について説明します。
author: normesta
ms.author: normesta
ms.date: 11/01/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 636130113f7c8ce40e5abfd99ae2d18c43740c3c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719207"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags"></a>BLOB インデックス タグを使用して Azure BLOB データを管理および検索する

データセットのサイズが大きくなるほど、大量のデータから特定のオブジェクトを見つけるのが難しくなることがあります。 BLOB インデックス タグを使用すると、キー値インデックス タグ属性を使用して、データ管理機能と探索機能が提供されます。 ストレージ アカウントの 1 つのコンテナー内またはすべてのコンテナー内のオブジェクトを分類および検索できます。 データ要件が変化したら、インデックス タグを更新することによってオブジェクトを動的に分類できます。 現在のコンテナーの編成に合わせてオブジェクトを保持できます。

BLOB インデックス タグを使用すると、次のことができます。

- キー値インデックス タグを使用して、BLOB を動的に分類する

- ストレージ アカウント全体で特定のタグが付いた BLOB をすばやく検索する

- インデックス タグの評価に基づいて BLOB API の条件付き動作を指定する

- [BLOB ライフサイクル管理](./lifecycle-management-overview.md)などの機能の高度な制御に、インデックス タグを使用する

ストレージ アカウントに何百万もの BLOB があり、多数の異なるアプリケーションによってアクセスされるシナリオについて考えてみましょう。 1 つのプロジェクトからすべての関連データを検索する必要があります。 データは異なる名前付け規則を使用して複数のコンテナーに分散されている可能性があるので、スコープ内に何があるか確実にはわかりません。 ただし、アプリケーションを使用すると、すべてのデータにプロジェクトに基づくタグが付けられてアップロードされます。 何百万もの BLOB を検索して名前とプロパティを比較するのではなく、`Project = Contoso` を検出条件として使用できます。 BLOB インデックスにより、ストレージ アカウント全体のすべてのコンテナーがフィルター処理され、`Project = Contoso` から 50 個の BLOB のセットだけがすばやく検出されて返されます。

BLOB インデックスの使用方法の例については、[BLOB インデックス タグを使用したデータの管理と検索](storage-blob-index-how-to.md)に関する記事を参照してください。

## <a name="blob-index-tags-and-data-management"></a>BLOB インデックス タグとデータ管理

コンテナーと BLOB 名のプレフィックスは、1 次元の分類です。 BLOB インデックス タグを使用すると、[BLOB データ型 (ブロック、追加、またはページ)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) に対して多次元の分類を行うことができます。 多次元の分類は Azure Blob Storage によってネイティブにインデックスが付けられるため、データをすばやく見つけることができます。

ストレージ アカウントに次の 5 つの BLOB があるとします。

- *container1/transaction.csv*

- *container2/campaign.docx*

- *photos/bannerphoto.png*

- *archives/completed/2019review.pdf*

- *logs/2020/01/01/logfile.txt*

これらの BLOB は、"*コンテナー/仮想フォルダー/BLOB 名*" のプレフィックスを使用して分離されています。 これらの 5 つの BLOB に対して `Project = Contoso` というインデックス タグ属性を設定することで、現在のプレフィックス編成を維持しながら、それらをまとめて分類することができます。 インデックス タグを追加すると、インデックスを使用してデータをフィルター処理および検索する機能が提供されるため、データを移動する必要がなくなります。

## <a name="setting-blob-index-tags"></a>BLOB インデックス タグの設定

BLOB インデックス タグは、ストレージ アカウント内の新規または既存のオブジェクトに適用できるキー値属性です。 アップロード プロセスの間にインデックス タグを指定するには、[Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、または [Copy Blob](/rest/api/storageservices/copy-blob) 操作と、省略可能な `x-ms-tags` ヘッダーを使用します。 ストレージ アカウントに BLOB が既にある場合は、[Set Blob Tags](/rest/api/storageservices/set-blob-tags) を呼び出し、要求の本文でフォーマット済みの XML ドキュメントとインデックス タグを渡します。

> [!IMPORTANT]
> BLOB インデックス タグの設定は、[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)と、BLOB のタグにアクセスするためのアクセス許可 (`t` SAS アクセス許可) が割り当てられた Shared Access Signature を持つ任意のユーザーが実行できます。
>
> さらに、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` アクセス許可を持つ RBAC ユーザーも、この操作を実行できます。

データの処理が完了した日時を記述する 1 つのタグを BLOB に適用できます。

> "processedDate" = '2020-01-01'

BLOB に複数のタグを適用して、データをよりわかりやすくすることができます。

> "Project" = 'Contoso' "Classified" = 'True' "Status" = 'Unprocessed' "Priority" = '01'

既存のインデックス タグ属性を変更するには、既存のタグ属性を取得し、そのタグ属性を変更してから、[Set Blob Tags](/rest/api/storageservices/set-blob-tags) 操作を使用して置き換えます。 BLOB からすべてのインデックス タグを削除するには、タグ属性を指定せずに `Set Blob Tags` 操作を呼び出します。 BLOB インデックス タグは BLOB データ コンテンツのサブリソースであるため、`Set Blob Tags` により基になるコンテンツは変更されず、BLOB の last-modified-time または eTag も変更されません。 現在のすべてのベース BLOB に対するインデックス タグを作成または変更できます。 以前のバージョンでもインデックス タグは保持されますが、BLOB インデックス エンジンには渡されません。そのため、インデックス タグのクエリを実行して以前のバージョンを取得することはできません。 スナップショットや論理的に削除された BLOB のタグを変更することはできません。

BLOB インデックス タグには次の制限が適用されます。

- 各 BLOB には、最大 10 個の BLOB インデックス タグを設定できます

- タグ キーは、1 文字から 128 文字の長さにする必要があります

- タグ値は、0 文字から 256 文字の長さにする必要があります

- タグ キーとタグ値では、大文字と小文字が区別されます

- タグのキーと値によってサポートされるのは、文字列データ型のみです。 数値、日付、時刻、または特殊文字はすべて、文字列として保存されます

- タグ キーとタグ値は、次の名前付け規則に従う必要があります。

  - 英数字:

    - **a** から **z** (小文字)

    - **A** から **Z** (大文字)

    - **0** から **9** (数字)

  - 有効な特殊文字: スペース、プラス、マイナス、ピリオド、コロン、等号、アンダースコア、スラッシュ (` +-.:=_/`)

## <a name="getting-and-listing-blob-index-tags"></a>BLOB インデックス タグの取得と一覧表示

BLOB インデックス タグは BLOB データと共にサブリソースとして格納され、基になる BLOB データ コンテンツとは別に取得できます。 1 つの BLOB に対する BLOB インデックス タグは、[Get Blob Tags](/rest/api/storageservices/get-blob-tags) 操作で取得できます。 `include:tags` パラメーターを指定した [List Blobs](/rest/api/storageservices/list-blobs) 操作でも、コンテナー内のすべての BLOB が、BLOB インデックス タグと共に返されます。

> [!IMPORTANT]
> BLOB インデックス タグの取得と列挙は、[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)と、BLOB のタグにアクセスするためのアクセス許可 (`t` SAS アクセス許可) が割り当てられた Shared Access Signature を持つ任意のユーザーが実行できます。
>
> さらに、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` アクセス許可を持つ RBAC ユーザーも、この操作を実行できます。

少なくとも 1 つの BLOB インデックス タグが設定されている BLOB に対し、[List Blobs](/rest/api/storageservices/list-blobs)、[Get Blob](/rest/api/storageservices/get-blob)、[Get Blob Properties](/rest/api/storageservices/get-blob-properties) の各操作で、BLOB のインデックス タグの数を示す `x-ms-tag-count` が返されます。

## <a name="finding-data-using-blob-index-tags"></a>BLOB インデックス タグを使用したデータの検索

インデックス エンジンにより、キー値属性が多次元インデックスに公開されます。 インデックス タグを設定すると、それらは BLOB に存在するようになり、すぐに取得できます。 BLOB インデックスが更新されるまでには、時間がかかる場合があります。 BLOB インデックスが更新されると、Blob Storage によって提供されるネイティブ クエリと検出の機能を使用できるようになります。

[Find Blobs by Tags](/rest/api/storageservices/find-blobs-by-tags) 操作を使用すると、指定したクエリ式に一致するインデックス タグを持つ BLOB のセットをフィルター処理して取得できます。 `Find Blobs by Tags` を使用すると、ストレージ アカウント内のすべてのコンテナーをフィルター処理することも、フィルター処理のスコープを 1 つのコンテナーだけに限定することもできます。 すべてのインデックス タグのキーと値は文字列であるため、関係演算子により辞書式の並べ替えが使用されます。

> [!IMPORTANT]
> BLOB インデックス タグを使用したデータの検索は、[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)と、タグを使用して BLOB を検索するためのアクセス許可 (`f` SAS アクセス許可) が割り当てられた Shared Access Signature を持つ任意のユーザーが実行できます。
>
> さらに、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` アクセス許可を持つ RBAC ユーザーも、この操作を実行できます。

BLOB インデックスのフィルター処理には、次の条件が適用されます。

- タグ キーは二重引用符 (") で囲む必要があります

- タグ値とコンテナー名は、単一引用符 (') で囲む必要があります

- @ 文字は、特定のコンテナー名でのフィルター処理にのみ使用できます (例: `@container = 'ContainerName'`)

- フィルターは、文字列に対する辞書式の並べ替えで適用されます

- 同じキーに対する同じ側の範囲操作は無効です (例: `"Rank" > '10' AND "Rank" >= '15'`)

- REST を使用してフィルター式を作成する場合は、文字を URI でエンコードする必要があります

- タグ クエリは、単一のタグを使用して等号一致するように最適化されています (例: StoreID = "100")。  >, >=, <, <= を含む単一のタグを使用した範囲クエリも効率的です。 AND を複数のタグとともに使用するクエリは、それほど効率的ではありません。  たとえば、Cost > "01" AND Cost <= "100" は効率的です。 Cost > "01 AND StoreID = "2" は効率的ではありません。

次の表では、`Find Blobs by Tags` に対して有効なすべての演算子を示します。

|  演算子  |  説明  | 例 |
|------------|---------------|---------|
|     =      |     等しい     | `"Status" = 'In Progress'` |
|     >      |  より大きい | `"Date" > '2018-06-18'` |
|     >=     |  以上 | `"Priority" >= '5'` |
|     <      |  より小さい   | `"Age" < '32'` |
|     <=     |  以下  | `"Company" <= 'Contoso'` |
|    AND     |  論理 AND  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | 特定のコンテナーに範囲指定します | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> タグの設定とクエリを行うときは、辞書の順序を理解しておいてください。
>
> - 数字は、文字の前に並べられます。 数字は、1 桁目の数字に基づいて並べられます。
> - 大文字は、小文字の前に並べられます。
> - 記号は標準ではありません。 一部の記号は、数値の前に並べられます。 その他の記号は、文字の前または後に並べられます。

## <a name="conditional-blob-operations-with-blob-index-tags"></a>BLOB インデックス タグを使用した条件付き BLOB 操作

REST バージョン 2019-10-10 以降では、ほとんどの [BLOB サービス API](/rest/api/storageservices/operations-on-blobs) で、指定された BLOB インデックス条件が満たされた場合にのみ操作が成功するように、条件ヘッダー `x-ms-if-tags` がサポートされるようになりました。 条件が満たされない場合は、`error 412: The condition specified using HTTP conditional header(s) is not met` が表示されます。

`x-ms-if-tags` ヘッダーは、他の既存の HTTP 条件ヘッダーと組み合わせることができます (If-Match、If-None-Match など)。 要求に複数の条件ヘッダーが指定されている場合、操作が成功するには、それらのすべてが true に評価される必要があります。 すべての条件ヘッダーは実質的に、論理 AND で結合されます。

次の表では、条件付き操作に対して有効な演算子を示します。

|  演算子  |  説明  | 例 |
|------------|---------------|---------|
|     =      |     等しい     | `"Status" = 'In Progress'` |
|     <>     |   等しくない   | `"Status" <> 'Done'` |
|     >      |  より大きい | `"Date" > '2018-06-18'` |
|     >=     |  以上 | `"Priority" >= '5'` |
|     <      |  より小さい   | `"Age" < '32'` |
|     <=     |  以下  | `"Company" <= 'Contoso'` |
|    AND     |  論理 AND  | `"Rank" >= '010' AND "Rank" < '100'` |
|     OR     | 論理 OR   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> `Find Blobs by Tags` 操作には存在しませんが、BLOB 操作に対する条件付きヘッダー `x-ms-if-tags` で使用できる演算子として、"等しくない" と "論理 OR" の 2 つが追加されています。

## <a name="platform-integrations-with-blob-index-tags"></a>BLOB インデックス タグを使用したプラットフォーム統合

BLOB インデックス タグを使用すると、BLOB データの分類、管理、検索に役立つだけでなく、[ライフサイクル管理](./lifecycle-management-overview.md)などの他の Blob Storage 機能との統合も提供されます。

### <a name="lifecycle-management"></a>ライフサイクル管理

`blobIndexMatch` をライフサイクル管理のルール フィルターとして使用すると、BLOB に適用されるインデックス タグに基づいて、データをよりクールな層に移動したり、データを削除したりできます。 ルールをより細かく設定でき、指定したタグ条件に一致する場合にのみ BLOB を移動または削除することができます。

BLOB インデックス一致をライフサイクル ルールのスタンドアロン フィルター セットとして設定して、タグ付きデータにアクションを適用することができます。 または、プレフィックスと BLOB インデックスの両方を組み合わせて、より具体的なデータ セットに一致させることができます。 ライフサイクル ルールで複数のフィルターを指定すると、論理 AND 演算が適用されます。 "*すべての*" フィルター条件に一致する場合にのみ、アクションが適用されます。

次のライフサイクル管理ルールの例は、`videofiles` という名前のコンテナー内のブロック BLOB に適用されます。 データが `"Status" == 'Processed' AND "Source" == 'RAW'` の BLOB インデックス タグ条件と一致する場合にのみ、ルールにより BLOB はストレージをアーカイブするために階層化されます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

![Azure portal でのライフサイクル管理における BLOB インデックス一致ルールの例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>アクセス許可と承認

次のいずれかの方法を使用して、BLOB インデックス タグへのアクセスを承認できます。

- Azure Active Directory (Azure AD) セキュリティ プリンシパルへのアクセス許可を付与するための、Azure のロールベースのアクセス制御 (Azure RBAC) の使用。 優れたセキュリティと使いやすさのためには、Azure AD を使用します。 BLOB 操作での Azure AD の使用に関する詳細については、「[Azure Storage 内のデータへのアクセスを承認する](../common/authorize-data-access.md)」を参照してください。

- BLOB インデックスへのアクセスを委任するための、Shared Access Signature (SAS) の使用。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)」を参照してください。

- 共有キーを使用した操作を承認するための、アカウント アクセス キーの使用。 詳細については、[共有キーによる承認](/rest/api/storageservices/authorize-with-shared-key)に関するページを参照してください。

BLOB インデックス タグは、BLOB データに対するサブリソースです。 BLOB の読み取りまたは書き込みを行うアクセス許可または SAS トークンを持つユーザーは、BLOB インデックス タグにアクセスできない可能性があります。

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

[Azure AD ID](../common/authorize-data-access.md) を使用する呼び出し元には、BLOB インデックス タグを操作するための次のアクセス許可が付与される場合があります。

| BLOB インデックス タグの操作                                          | Azure RBAC アクション                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [BLOB タグの設定](/rest/api/storageservices/set-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write    |
| [BLOB タグの取得](/rest/api/storageservices/get-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read     |
| [タグによる BLOB の検索](/rest/api/storageservices/find-blobs-by-tags) | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action |

インデックス タグの操作には、基になる BLOB データとは別に、追加のアクセス許可が必要です。 [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールには、3 つの BLOB インデックス タグ操作すべてに対するアクセス許可が付与されます。 

### <a name="sas-permissions"></a>SAS のアクセス許可

[Shared Access Signature (SAS)](../common/storage-sas-overview.md) を使用する呼び出し元には、BLOB インデックス タグを操作するためのスコープ付きアクセス許可が付与される場合があります。

#### <a name="service-sas-for-a-blob"></a>BLOB のサービス SAS

BLOB インデックス タグへのアクセスを許可するために、BLOB のサービス SAS に次のアクセス許可が付与される場合があります。 BLOB の読み取り (`r`) と書き込み (`w`) のアクセス許可だけでは、インデックス タグの読み取りや書き込みを行うことはできません。

| アクセス許可 | URI の略記 | 許可される操作                |
|------------|------------|-----------------------------------|
| インデックス タグ |     t      | BLOB のインデックス タグを取得および設定する |

#### <a name="service-sas-for-a-container"></a>コンテナーのサービス SAS

BLOB タグのフィルター処理を許可するために、コンテナーのサービス SAS に次のアクセス許可が付与される場合があります。 BLOB リスト (`i`) のアクセス許可では、インデックス タグによる BLOB のフィルター処理を行うことはできません。

| アクセス許可 | URI の略記 | 許可される操作         |
|------------|------------|----------------------------|
| インデックス タグ |     f      | インデックス タグを使用して BLOB を検索する |

#### <a name="account-sas"></a>アカウント SAS

BLOB インデックス タグへのアクセスと BLOB タグのフィルター処理を許可するために、アカウント SAS に次のアクセス許可が付与される場合があります。 

| アクセス許可 | URI の略記 | 許可される操作                |
|------------|------------|-----------------------------------|
| インデックス タグ |     t      | BLOB のインデックス タグを取得および設定する |
| インデックス タグ |     f      | インデックス タグを使用して BLOB を検索する |

BLOB の読み取り (`r`) と書き込み (`w`) のアクセス許可だけでは、インデックス タグの読み取りや書き込みを行うことはできません。リスト (`i`) のアクセス許可では、インデックス タグによる BLOB のフィルター処理を行うことはできません。

## <a name="choosing-between-metadata-and-blob-index-tags"></a>メタデータと BLOB インデックス タグのいずれかの選択

BLOB インデックス タグとメタデータはどちらも、ユーザー定義の任意のキー値プロパティを BLOB リソースと共に格納することができます。 どちらも、BLOB の内容を返したり変更したりせずに、直接取得して設定することができます。 メタデータとインデックス タグの両方を使用できます。

ネイティブの Blob Storage サービスによって自動的にインデックス付けされてクエリ可能になるのは、インデックス タグだけです。 メタデータについては、ネイティブなインデックス付けまたは検索はできません。 [Azure Search](../../search/search-blob-ai-integration.md) などの別のサービスを使用する必要があります。 BLOB インデックス タグには、基になる BLOB データとは別の読み取り、フィルター処理、書き込みのための追加のアクセス許可があります。 メタデータは、BLOB と同じアクセス許可が使用され、[Get Blob](/rest/api/storageservices/get-blob) および [Get Blob Properties](/rest/api/storageservices/get-blob-properties) 操作によって HTTP ヘッダーとして返されます。 BLOB インデックス タグは、[Microsoft マネージド キー](../common/storage-service-encryption.md)を使用して、保存時に暗号化されます。 メタデータは、BLOB データに対して指定されているものと同じ暗号化キーを使用して、保存時に暗号化されます。

以下の表は、メタデータと BLOB インデックス タグの違いをまとめたものです。

|              |   Metadata   |   BLOB インデックス タグ  |
|--------------|--------------|--------------------|
| **制限**      | 数に制限はなく、合計 8 KB で、大文字と小文字の区別はありません | BLOB あたり最大 10 個のタグ、タグあたり 768 バイトで、大文字と小文字が区別されます |
| **更新プログラム**    | アーカイブ層では許可されません。`Set Blob Metadata` により、既存のすべてのメタデータが置き換えられます。`Set Blob Metadata` により、BLOB の last-modified-time が変更されます | すべてのアクセス層で許可されます。`Set Blob Tags` により、既存のすべてのタグが置き換えられます。`Set Blob Tags` により、BLOB の last-modified-time は変更されません |
| **Storage**     | BLOB データと共に格納されます | BLOB データのサブリソース |
| **インデックス作成とクエリの実行** | Azure Search などの別のサービスを使用する必要があります | インデックス付けとクエリ実行機能は、Blob Storage に組み込まれています |
| **暗号化** | BLOB データに使用されるのと同じ暗号化キーを使用して保存時に暗号化 | Microsoft が管理する暗号化キーを使用して保存時に暗号化 |
| **料金** | メタデータのサイズは BLOB のストレージ コストに含まれます | インデックス タグあたりの固定コスト |
| **ヘッダー応答** | メタデータは、`Get Blob` および `Get Blob Properties` のヘッダーとして返されます | タグ カウントは `Get Blob` または `Get Blob Properties` によって返され、タグは `Get Blob Tags` と `List Blobs` によってのみ返されます |
| **アクセス許可**  | BLOB データに対する読み取りまたは書き込みのアクセス許可がメタデータに拡張されます | インデックス タグの読み取り、フィルター処理、または書き込みには、追加のアクセス許可が必要です |
| **名前を付ける** | メタデータの名前は C# 識別子の名前付け規則に従う必要があります | より広い範囲の英数字が BLOB インデックス タグによってサポートされています |

## <a name="pricing"></a>価格

ストレージ アカウント内のインデックス タグの月単位の平均数に対して課金されます。 インデックス作成エンジンには料金はかかりません。 BLOB タグの設定、BLOB タグの取得、および BLOB タグの検索の要求は、現在のそれぞれのトランザクション レートで課金されます。 タグによる BLOB の検索トランザクションを実行するときに試用されるリスト トランザクションの数は、要求に含まれる句の数と同じであることに注意してください。 たとえば、クエリ (StoreID = 100) は 1 つのリスト トランザクションです。  クエリ (StoreID = 100 AND SKU = 10010) は 2 つのリスト トランザクションです。 詳しくは、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

<a id="regional-availability-and-storage-account-support"></a>

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![いいえ](../media/icons/no-icon.png)              | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ブロック BLOB          | ![いいえ](../media/icons/no-icon.png)|![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

## <a name="conditions-and-known-issues"></a>条件と既知の問題

このセクションでは、既知の問題と条件について説明します。

- 汎用 v2 アカウントのみがサポートされています。 Premium ブロック BLOB、レガシ BLOB、および階層型名前空間が有効になっているアカウントはサポートされていません。 汎用 v1 アカウントはサポートされません。

- インデックス タグがあるページ BLOB をアップロードしても、タグは保持されません。 ページ BLOB をアップロードした後で、タグを設定してください。

- BLOB ストレージのバージョン管理が有効になっている場合でも、現在のバージョンでインデックス タグを使用できます。 インデックス タグは以前のバージョンでも保持されますが、これらのタグは BLOB インデックス エンジンには渡されません。そのため、前のバージョンを取得するために使用することはできません。 以前のバージョンを現在のバージョンに昇格させると、その以前のバージョンのタグが現在のバージョンのタグになります。 これらのタグは現在のバージョンに関連付けられているため、BLOB インデックス エンジンに渡され、クエリが実行できるようになります。

- インデックス タグにインデックスが付けられているかどうかを判断する API はありません。

- ライフサイクル管理によってサポートされているのは、BLOB インデックス一致による等値チェックのみです。

- `Copy Blob` を使用しても、BLOB インデックス タグはコピー元 BLOB から新しいコピー先 BLOB にコピーされません。 コピー操作中にコピー先 BLOB に適用するタグを指定できます。

## <a name="faq"></a>よく寄せられる質問

**BLOB インデックスは、BLOB 内のコンテンツをフィルター処理したりクエリを実行したりするのに役立ちますか?**

いいえ、BLOB データ内で検索する必要がある場合は、クエリ アクセラレーションまたは Azure Search を使用してください。

**インデックス タグの値には何か要件がありますか?**

文字列データ型のみが BLOB インデックス タグによってサポートされ、クエリを実行すると辞書の順序で結果が返されます。 数値の場合は、0 を埋めます。 日付と時刻の場合は、ISO 8601 準拠の形式で格納します。

**BLOB インデックス タグと Azure Resource Manager タグは関連していますか?**

いいえ。Resource Manager タグは、サブスクリプション、リソース グループ、ストレージ アカウントなどのコントロール プレーン リソースを整理するのに役立ちます。 インデックス タグにより、データ プレーンでの BLOB の管理と検出が提供されます。

## <a name="next-steps"></a>次の手順

BLOB インデックスを使用する方法の例については、[BLOB インデックスを使用してデータを管理して検索する](storage-blob-index-how-to.md)方法に関する記事を参照してください。

[ライフサイクルの管理](./lifecycle-management-overview.md)について確認し、BLOB インデックスの一致を含むルールを設定します。
