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
ms.openlocfilehash: 83b3f4f9b84c25e3fe2822cbabb63adec1fb22ab
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489877"
---
# <a name="security-considerations-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Azure Storage での Azure ロールの割り当て条件についてのセキュリティ上の考慮事項 (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure 属性ベースのアクセス制御 (Azure ABAC)](storage-auth-abac.md) を使用してリソースを完全にセキュリティで保護するには、[Azure ロールの割り当て条件](../../role-based-access-control/conditions-format.md)で使用される[属性](storage-auth-abac-attributes.md)も保護する必要があります。 これには、ロールの割り当て条件で使用される属性を変更するために使用できるすべてのアクセス許可またはアクションをセキュリティで保護する必要があります。 たとえば、パスに基づいてストレージ アカウントの条件を作成する場合は、プリンシパルにファイル パスの名前を変更する無制限のアクセス許可があると、アクセスが侵害される可能性がある点に注意する必要があります。

この記事では、ロールの割り当て条件について考慮する際に、念頭に置いておくべきセキュリティ上の考慮事項について説明します。

## <a name="use-of-other-authorization-mechanisms"></a>その他の承認メカニズムを使用する 

Azure ABAC は、ロールの割り当てに関する条件として実装されます。 これらの条件は Azure Active Directory (Azure AD) で [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を使用する場合にのみ評価されるため、代替の承認方法を使用したアクセスを有効にした場合は、これらの条件をバイパスできます。 たとえば、共有キーまたは共有アクセス署名の承認を使用する場合、条件は評価されません。 同様に、階層型名前空間機能が有効になっているアカウントで[アクセス制御リスト (ACL)](../blobs/data-lake-storage-access-control.md) を使用してファイルまたはフォルダーへのアクセスが許可されている場合、条件は評価されません。 

これは、ストレージ アカウントの[共有キーの承認を無効にする](shared-key-authorization-prevent.md)ことで回避できます。

## <a name="securing-storage-attributes-used-in-conditions"></a>条件で使用されるストレージ属性のセキュリティ保護

### <a name="blob-path"></a>BLOB パス

条件の *@Resource* 属性として BLOB パスを使用する場合は、階層型名前空間を持つアカウントを使用するときに、ユーザーが BLOB の名前を変更してファイルにアクセスできないようにする必要もあります。 たとえば、BLOB パスに基づいて条件を作成する場合は、次の操作に対するユーザーのアクセスを制限する必要もあります。

| アクション | 説明 |
| :--- | :--- |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | これにより、ユーザーは Path Create API を使用してファイル名を変更できます。 |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | これにより、さまざまなファイル システムおよびパス操作にアクセスできます。 |

### <a name="blob-index-tags"></a>BLOB インデックス タグ

[BLOB インデックス タグ](../blobs/storage-manage-find-blobs.md)は、ストレージ内の条件の自由形式属性として使用されます。 これらのタグを使用してアクセス条件を作成する場合は、タグ自体も保護する必要があります。 具体的には、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` DataAction を使用すると、ユーザーはストレージ オブジェクトでタグを変更できます。 また、このアクションへのセキュリティ プリンシパルのアクセスを適切に制限することで、タグ キーまたは値を変更することによって、本来アクセスできないように格納されているオブジェクトへのアクセスを取得できないようにする必要があります。

加えて、BLOB インデックス タグが条件で使用されている場合、データおよび関連付けられたインデックス タグが個別の操作で更新されると、データが定期的に脆弱になる可能性があります。 データがストレージに書き込まれる瞬間からデータを保護するには、BLOB 書き込み操作の条件で、関連付けられているインデックス タグに適切な値を使用し、BLOB を同じ更新操作で適切な値に設定する必要があります。

#### <a name="tags-on-copied-blobs"></a>コピーされた BLOB のタグ

[Copy Blob](/rest/api/storageservices/Copy-Blob) API またはそのいずれかのバリアントが使用されている場合、BLOB インデックス タグは、コピー元 BLOB からコピー先に既定ではコピーされません。 コピー時の BLOB のアクセス範囲を維持するには、そのタグも明示的にコピーする必要があります。

#### <a name="tags-on-snapshots"></a>スナップショットのタグ

プレビューでは、BLOB スナップショットでのタグの更新はサポートされていません。 これは、スナップショットを取得する前に、BLOB のタグを更新する必要があることを意味します。 タグの更新は、ベース BLOB にのみ適用されます。 スナップショットのタグには、引き続き以前の値が設定されます。

スナップショットが取得された後にベース BLOB のタグが変更され、そのタグを使用する条件がある場合、ベース BLOB のアクセスの範囲は BLOB スナップショットの場合とは異なる場合があります。

#### <a name="tags-on-blob-versions"></a>BLOB バージョンでのタグ

BLOB インデックス タグは、[Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、または [Copy Blob](/rest/api/storageservices/Copy-Blob) API を使用して BLOB バージョンを作成するときにコピーされません。 これらの API のヘッダーを使用してタグを指定できます。

異なるバージョンの BLOB でタグを変更できますが、ベース BLOB のタグが変更された場合、これらは自動的には更新されません。 タグを使用して BLOB とそのすべてのバージョンのアクセスの範囲を変更する場合は、ベース BLOB とそのすべてのバージョンのタグを更新する必要があります。

#### <a name="querying-and-filtering-limitations-for-versions-and-snapshots"></a>バージョンとスナップショットのクエリとフィルター処理の制限事項

タグを使用してコンテナー内の BLOB のクエリとフィルター処理を行う場合、応答に含まれるのはベース BLOB のみです。 要求されたキーと値を持つ BLOB バージョンまたはスナップショットは含まれません。

## <a name="roles-and-permissions"></a>ロールとアクセス許可

[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)に対してロールの割り当て条件を使用している場合は、ロールがプリンシパルに付与するアクセス許可をすべて慎重に確認する必要があります。

### <a name="inherited-role-assignments"></a>継承されたロールの割り当て

ロールの割り当ては、管理グループ、サブスクリプション、リソース グループ、ストレージ アカウント、またはコンテナーに対して構成できます。また、各レベルで指定された順序で継承されます。 Azure RBAC には加法モデルがあるため、各レベルでのロール割り当ての合計が有効なアクセス許可になります。 セキュリティ プリンシパルに複数のロールまたは複数のレベルの特定のロールを介して割り当てられたアクセス許可がある場合、そのアクセス許可を使用する操作のアクセス権は、各レベルで割り当てられたロールごとに個別に評価されます。

条件はロールの割り当ての条件として実装されるため、無条件のロールの割り当てを行うと、ユーザーは条件ポリシーによって意図されたアクセス制限をバイパスできます。 たとえば、サブスクリプション レベルとストレージ アカウント レベルの両方で *ストレージ BLOB データ共同作成者* などのロールがセキュリティ プリンシパルに割り当てられている場合、ロールの割り当て条件がストレージ アカウント レベルでのみ定義されている場合、プリンシパルはサブスクリプション レベルでのロールの割り当てを通じてアカウントに無制限にアクセスできます。その逆も同様です。

そのため、セキュリティ プリンシパルにリソースへのアクセスが許可されているリソース階層のすべてのレベルで、条件を一貫して適用する必要があります。

## <a name="other-considerations"></a>その他の注意事項

### <a name="condition-operations-that-write-blobs"></a>BLOB を書き込む条件操作

BLOB を書き込む操作の多くでは、`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` または `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` のいずれかのアクセス許可が必要になります。 [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)や[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)などの組み込みロールは、セキュリティ プリンシパルに両方の権限を付与します。

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

