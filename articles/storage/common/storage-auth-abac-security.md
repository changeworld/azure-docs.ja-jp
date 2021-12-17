---
title: Azure Storage での Azure ロールの割り当て条件についてのセキュリティ上の考慮事項 (プレビュー)
titleSuffix: Azure Storage
description: Azure ロール割り当て条件および Azure 属性ベースのアクセス制御 (Azure ABAC) のセキュリティ上の考慮事項。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 46be6327052aa280b9d23bc05764550a05739c5e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627921"
---
# <a name="security-considerations-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Azure Storage での Azure ロールの割り当て条件についてのセキュリティ上の考慮事項 (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure 属性ベースのアクセス制御 (Azure ABAC)](storage-auth-abac.md) を使用してリソースを完全にセキュリティで保護するには、[Azure ロールの割り当て条件](../../role-based-access-control/conditions-format.md)で使用される[属性](storage-auth-abac-attributes.md)も保護する必要があります。 たとえば、ファイル パスに基づいて条件を作成する場合は、プリンシパルにファイル パスの名前を変更する無制限のアクセス許可があると、アクセスが侵害される可能性がある点に注意する必要があります。

この記事では、ロールの割り当て条件について考慮する際に、念頭に置いておくべきセキュリティ上の考慮事項について説明します。

## <a name="use-of-other-authorization-mechanisms"></a>その他の承認メカニズムを使用する

ロールの割り当て条件は、承認のために Azure RBAC を使用している場合にのみ評価されます。 代替の承認方法を使用してアクセスを許可する場合、これらの条件はバイパスできます。
- [共有キー](/rest/api/storageservices/authorize-with-shared-key)認可
- [アカウントの Shared Access Signature](/rest/api/storageservices/create-account-sas) (SAS)
- [サービス SAS](/rest/api/storageservices/create-service-sas)。

同様に、[階層型名前空間](../blobs/data-lake-storage-namespace.md) (HNS) を持つストレージ アカウント内で[アクセス制御リスト (ACL)](../blobs/data-lake-storage-access-control.md) を使用してアクセスが許可されている場合、条件は評価されません。

共有キー、アカウント レベルの SAS、サービス レベルの SAS の認可が行われないようにするには、ご自身のストレージ アカウントに対して[共有キー認可を無効](shared-key-authorization-prevent.md)にします。 ユーザー委任 SAS は Azure RBAC に依存しているため、この認可方法を使用するときは、ロールの割り当て条件が評価されます。

> [!NOTE]
> Data Lake Storage Gen2 で ACL を使用してアクセスが許可されている場合、ロールの割り当て条件は評価されません。 この場合、ACL を通じて許可されているものと重複しないように、アクセス範囲を計画する必要があります。

## <a name="securing-storage-attributes-used-in-conditions"></a>条件で使用されるストレージ属性のセキュリティ保護

### <a name="blob-path"></a>BLOB パス

条件の *@Resource* 属性として BLOB パスを使用する場合は、階層型名前空間を持つアカウントを使用するときに、ユーザーが BLOB の名前を変更してファイルにアクセスできないようにする必要もあります。 たとえば、BLOB パスに基づいて条件を作成する場合は、次の操作に対するユーザーのアクセスを制限する必要もあります。

| アクション | 説明 |
| :--- | :--- |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | このアクションにより、ユーザーが Path Create API を使用してファイル名を変更できます。 |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | このアクションにより、さまざまなファイル システムおよびパス操作にアクセスできます。 |

### <a name="blob-index-tags"></a>BLOB インデックス タグ

[BLOB インデックス タグ](../blobs/storage-manage-find-blobs.md)は、ストレージ内の条件の自由形式属性として使用されます。 これらのタグを使用してアクセス条件を作成する場合は、タグ自体も保護する必要があります。 具体的には、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` DataAction を使用すると、ユーザーはストレージ オブジェクトでタグを変更できます。 このアクションを制限することで、ユーザーがタグ キーまたは値を操作して、許可されていないオブジェクトにアクセスするのを防ぐことができます。

さらに、BLOB インデックス タグが条件内で使用されている場合、データおよび関連付けられたインデックス タグが個別の操作で更新されると、データが脆弱になる可能性があります。 BLOB 書き込み操作で `@Request` 条件を使用すると、同じ更新操作でインデックス タグを設定するように要求することができます。 この方法により、データを、ストレージに書き込まれた瞬間からセキュリティで保護することができます。

#### <a name="tags-on-copied-blobs"></a>コピーされた BLOB のタグ

[BLOB のコピー](/rest/api/storageservices/Copy-Blob) API またはそのいずれかのバリアントを使用している場合、BLOB インデックス タグは、既定では、コピー元 BLOB からコピー先にコピーされません。 コピー時に BLOB のアクセス範囲を保持するには、タグもコピーする必要があります。

#### <a name="tags-on-snapshots"></a>スナップショットのタグ

BLOB スナップショット上のタグは変更できません。 これは、スナップショットを取得する前に、BLOB のタグを更新する必要があることを意味します。 ベース BLOB 上のタグを変更すると、そのスナップショット上のタグには以前の値が引き続き保持されます。

スナップショット取得後にベース BLOB 上のタグが変更された場合、アクセス範囲がベース BLOB とスナップショットで異なる場合があります。

#### <a name="tags-on-blob-versions"></a>BLOB バージョンでのタグ

BLOB インデックス タグは、[Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、または [Copy Blob](/rest/api/storageservices/Copy-Blob) API を使用して BLOB バージョンを作成するときにコピーされません。 これらの API のヘッダーを使用してタグを指定できます。

現在のベース BLOB 上のタグと、各 BLOB バージョン上のタグは個別に設定できます。 ベース BLOB 上のタグを変更しても、以前のバージョンのタグは更新されません。 タグを使用して BLOB とそのすべてのバージョンのアクセス範囲を変更する必要がある場合は、各バージョンのタグを更新する必要があります。

#### <a name="querying-and-filtering-limitations-for-versions-and-snapshots"></a>バージョンとスナップショットのクエリとフィルター処理の制限事項

タグを使用してコンテナー内の BLOB のクエリとフィルター処理を行う場合、応答に含まれるのはベース BLOB のみです。 要求されたキーと値を持つ BLOB バージョンまたはスナップショットは含まれません。

## <a name="roles-and-permissions"></a>ロールとアクセス許可

[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)に対してロールの割り当て条件を使用している場合は、ロールがプリンシパルに付与するアクセス許可をすべて慎重に確認する必要があります。

### <a name="inherited-role-assignments"></a>継承されたロールの割り当て

ロールの割り当ては、管理グループ、サブスクリプション、リソース グループ、ストレージ アカウント、またはコンテナーに対して構成できます。また、各レベルで指定された順序で継承されます。 Azure RBAC には加法モデルがあるため、各レベルでのロール割り当ての合計が有効なアクセス許可になります。 複数のロールの割り当てを使用して、プリンシパルに同じアクセス許可が割り当てられている場合、そのアクセス許可を使用している操作に対するアクセスは、すべてのレベルで割り当てごとに個別に評価されます。

条件はロールの割り当ての条件として実装されるため、無条件のロールの割り当てを使用すると、ユーザーは条件をバイパスできます。 たとえば、*ストレージ BLOB データ共同作成者* ロールは、ストレージ アカウントとサブスクリプションのユーザーに割り当てて、条件は、ストレージ アカウントの割り当てにのみ追加するとします。 この場合、ユーザーは、サブスクリプション レベルでのロールの割り当てによって、ストレージ アカウントに無制限にアクセスできます。

リソース階層全体のすべてのロールの割り当てに対して、一貫した条件を適用する必要があるのは、このためです。

## <a name="other-considerations"></a>その他の注意事項

### <a name="condition-operations-that-write-blobs"></a>BLOB を書き込む条件操作

BLOB を書き込む多くの操作に、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` または `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` のいずれかのアクセス許可が必要です。 [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)や[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)などの組み込みロールは、セキュリティ プリンシパルに両方の権限を付与します。

これらのロールに対してロールの割り当て条件を定義する場合は、これらのアクセス許可の両方で同じ条件を使用し、書き込み操作に対して一貫性のあるアクセス制限を確保する必要があります。

### <a name="behavior-for-copy-blob-and-copy-blob-from-url"></a>Copy Blob と Copy Blob From URL の動作

[Copy Blob](/rest/api/storageservices/Copy-Blob) および [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url) 操作の場合、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` アクションの属性として BLOB パスを使用する条件 `@Request` とそのサブ操作は、コピー先 BLOB に対してのみ評価されます。

コピー元 BLOB の条件では、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` アクションの条件 `@Resource` が評価されます。

### <a name="behavior-for-get-page-ranges"></a>Get Page Ranges の動作

[Get Page Ranges](/rest/api/storageservices/get-page-ranges) 操作では、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` アクションの属性として `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags` を使用する条件 `@Resource` とそのサブ操作は、コピー先 BLOB に対してのみ評価されます。

API の `prevsnapshot` URI パラメーターで指定された BLOB へのアクセスには、条件が適用されません。

## <a name="see-also"></a>こちらもご覧ください

- [Azure ロールの割り当て条件を使用して BLOB へのアクセスを承認する (プレビュー)](storage-auth-abac.md)
- [Azure Storage での Azure のロールの割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md)
- [Azure 属性ベースのアクセス制御 (Azure ABAC) とは (プレビュー)](../../role-based-access-control/conditions-overview.md)
