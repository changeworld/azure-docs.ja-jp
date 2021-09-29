---
title: Azure Storage 内での Azure のロールの割り当て条件のアクションと属性 (プレビュー)
titleSuffix: Azure Storage
description: Azure Storage 内で Azure ロールの割り当て条件と Azure 属性ベースのアクセス制御 (Azure ABAC) でサポートされるアクションと属性。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 791e533cc1f0dafa8724faa8cc3a46d1f8c770d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666005"
---
# <a name="actions-and-attributes-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Azure Storage 内での Azure のロールの割り当て条件のアクションと属性 (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure ロールの割り当て条件で使用できるサポートされている属性ディクショナリについて、Azure Storage の [DataAction](../../role-based-access-control/role-definitions.md#dataactions) ごとに説明します。 特定のアクセス許可または DataAction の影響を受ける Blob service 操作リストについては、「[Blob service 操作のアクセス許可](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-blob-service-operations)」をご覧ください。

ロールの割り当て条件の形式については、「[Azure ロールの割り当て条件の形式と構文](../../role-based-access-control/conditions-format.md)」をご覧ください。

## <a name="suboperations"></a>サブ操作

複数の Storage サービス操作を、1 つのアクセス許可または DataAction に関連付けることができます。 ただし、同じアクセス許可に関連付けられているこれらの操作がそれぞれ、異なるパラメーターをサポートする場合があります。 "*サブ操作*" を使用すると、同じアクセス許可を必要としながら、条件となる一連の属性が異なるサービス操作を区別できます。 したがって、サブ操作を使って、特定のパラメーターをサポートする操作のサブセットへのアクセスに対して、1 つの条件を指定することができます。 その後、そのパラメーターをサポートしない同じアクションを使用して、操作に対して別のアクセス条件を使用できます。

たとえば、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` アクションは、10 数種類のサービス操作に必要です。 一部の操作は要求パラメーターとして BLOB インデックス タグを受け入れ可能ですが、受け入れることができない操作もあります。 BLOB インデックス タグをパラメーターとして受け入れる操作については、要求条件内で BLOB インデックス タグを使用できます。 しかし、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` アクションでこのような条件が定義されていると、要求パラメーターとしてタグを受け入れないすべての操作がこの条件を評価できず、承認アクセス チェックに失敗します。

この場合は、省略可能なサブ操作 `Blob.Write.WithTagHeaders` を使用して、要求パラメーターとして BLOB インデックス タグをサポートするこれらの操作にのみ条件を適用することができます。

同様に、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` アクションに対する select 操作のみが、アクセスの前提条件として BLOB インデックス タグをサポートできます。 この操作のサブセットは、`Blob.Read.WithTagConditions` サブ操作によって特定されます。

> [!NOTE]
> BLOB は、任意のユーザー定義のキー値メタデータを格納する機能もサポートしています。 メタデータと BLOB インデックス タグは似ていますが、BLOB インデックス タグは条件付きで使用する必要があります。 詳細については、「[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md)」をご覧ください。

このプレビューでストレージ アカウントがサポートするサブ操作は次のとおりです。

> [!div class="mx-tableFixed"]
> | DataAction | サブ操作 | Display name | 説明 |
> | :--- | :--- | :--- | :--- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `Blob.Read.WithTagConditions` | タグの条件をサポートする BLOB 読み取り操作 | REST 操作 Get Blob、Get Blob Metadata、Get Blob Properties、Get Block List、Get Page Ranges、Query Blob Contents が含まれます。 |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` <br/> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `Blob.Write.WithTagHeaders` | 省略可能なタグを使用したコンテンツの BLOB 書き込み | REST 操作 Put Blob、Copy Blob、Copy Blob From URL、Put Block List が含まれます。 |

## <a name="actions-and-suboperations"></a>アクションとサブ操作

次の表に、Azure Storage の条件に対してサポートされているアクションとサブ操作を示します。

> [!div class="mx-tableFixed"]
> | Display name | 説明 | DataAction |
> | --- | --- | --- |
> | BLOB を削除する | BLOB を削除するための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` |
> | Read a blob (BLOB を読み取る) | BLOB を読み取るための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
> | Read content from a blob with tag conditions (タグ条件を使用して BLOB からコンテンツを読み取る)  | REST 操作: Get Blob、Get Blob Metadata、Get Blob Properties、Get Block List、Get Page Ranges、Query Blob Contents。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>**サブ操作**<br/>`Blob.Read.WithTagConditions` |
> | Write to a blob (BLOB に書き込む) | BLOB に書き込むための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` |
> | Write to a blob with blob index tags (BLOB インデックス タグを使用して BLOB に書き込む) | REST 操作: Put Blob、Put Block List、Copy Blob、Copy Blob From URL。 |`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>**サブ操作**<br/>`Blob.Write.WithTagHeaders` |
> | Create a blob or snapshot, or append data (BLOB またはスナップショットを作成するか、データを追加する) | BLOB を作成するための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` |
> | Write content to a blob with blob index tags (BLOB インデックス タグを使用して BLOB にコンテンツを書き込む) | REST 操作: Put Blob、Put Block List、Copy Blob、Copy Blob From URL。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>**サブ操作**<br/>`Blob.Write.WithTagHeaders` |
> | Delete a version of a blob (BLOB のバージョンを削除する) | BLOB のバージョンを削除するための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` |
> | Changes ownership of a blob (BLOB の所有権を変更する) | BLOB の所有権を変更するための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` |
> | Modify permissions of a blob (BLOB のアクセス許可を変更する) | BLOB のアクセス許可を変更するための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` |
> | Rename file or directory (ファイルまたはディレクトリの名前を変更する) | ファイルまたはディレクトリの名前を変更するための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` |
> | Permanently delete a blob overriding soft-delete (論理的な削除をオーバーライドする BLOB を完全に削除する) | 論理的な削除をオーバーライドする BLOB を完全に削除するための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` |
> | All data operations for accounts with HNS (HNS を使用するアカウントのすべてのデータ操作) | HNS を使用するストレージ アカウント上のすべてのデータ操作に対する DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` |
> | Read blob index tags (BLOB インデックス タグを読み取る) | BLOB インデックス タグを読み取るための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` |
> | Write blob index tags (BLOB インデックス タグを書き込む) | BLOB インデックス タグを書き込むための DataAction。 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` |

## <a name="attributes"></a>属性

次の表に、Azure Storage の条件に対してサポートされている属性の説明を示します。

> [!div class="mx-tableFixed"]
> | Display name | 説明 | 属性 |
> | --- | --- | --- |
> | コンテナー名| ストレージ コンテナーまたはファイル システムの名前。 コンテナー名を確認するときに使用します。 | `containers:name` |
> | BLOB パス | 仮想ディレクトリ、BLOB、フォルダー、ファイル リソースのいずれかのパス。 BLOB パス内の BLOB の名前またはフォルダーを確認するときに使用します。 | `blobs:path` |
> | Blob index tags [Keys] (BLOB インデックス タグ [キー]) | BLOB リソース上のインデックス タグ。 BLOB リソースと共に保存できる任意のユーザー定義キー値プロパティ。 BLOB インデックス タグ内のキーを確認するときに使用します。 | `tags&$keys$&` |
> | Blob index tags [Values in key] (BLOB インデックス タグ [キー内の値]) | BLOB リソース上のインデックス タグ。 BLOB リソースと共に保存できる任意のユーザー定義キー値プロパティ。 BLOB インデックス タグ内のキー (大文字と小文字を区別する) と値の両方を確認するときに使用します。 | `tags:`*keyname*`<$key_case_sensitive$>` |

> [!NOTE]
> 表示されている属性と値は、特に明記されていない限り、大文字と小文字が区別されません。

> [!NOTE]
> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path` 属性の条件を指定する場合、値にはコンテナー名または前の '/' 文字を含めることはできません。 パス文字は URL エンコードなしで使用してください。

> [!NOTE]
> BLOB インデックス タグは、[階層型名前空間](../blobs/data-lake-storage-namespace.md) (HNS) を使用する Data Lake Storage Gen2 ストレージ アカウントではサポートされていません。 HNS が有効になっているストレージ アカウントでは、インデックス タグを使用してロール割り当て条件を作成しないでください。

## <a name="attributes-available-for-each-action"></a>各アクションで使用できる属性

次の表に、対象アクションに応じて条件式で使用できる属性を示します。 1 つの条件に対して複数のアクションを選択した場合、属性は、選択したすべてのアクションに対して使用できる必要があるため、その条件に対して選択できる属性の数は少なくなる場合があります。

> [!div class="mx-tableFixed"]
> | DataAction | 属性 | Type | 適用対象 |
> | --- | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>**サブ操作**<br/>`Blob.Read.WithTagConditions` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>**サブ操作**<br/>`Blob.Write.WithTagHeaders` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>**サブ操作**<br/>`Blob.Write.WithTagHeaders` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |

## <a name="see-also"></a>こちらもご覧ください

- [Azure ロールの割り当て条件の例 (プレビュー)](storage-auth-abac-examples.md)
- [Azure ロールの割り当て条件の形式と構文 (プレビュー)](../../role-based-access-control/conditions-format.md)
- [Azure の属性ベースのアクセス制御 (Azure ABAC) とは (プレビュー)](../../role-based-access-control/conditions-overview.md)
