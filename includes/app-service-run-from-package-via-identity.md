---
author: mattchenderson
ms.service: app-service-web
ms.topic: include
ms.date: 06/11/2021
ms.author: mahender
ms.openlocfilehash: 9b05a4d16cbf6d3b9677c894bffb6e47018b5cff
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122261398"
---
[Azure AD で要求を承認](../articles/storage/blobs/authorize-access-azure-active-directory.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)するように Azure Blob Storage を構成できます。 つまり、有効期限がある SAS キーを生成する代わりに、アプリケーションの[マネージド ID](../articles/app-service/overview-managed-identity.md) を使用することができます。 既定では、アプリのシステム割り当て ID が使用されます。 ユーザー割り当て ID を指定する場合は、`WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID` アプリ設定をその ID のリソース ID に設定できます。 この設定は "SystemAssigned" を値として受け入れることもできますが、これは設定をまとめて省略することと同じです。

ID を使用してパッケージをフェッチできるようにするには、次のようにします。

1. Blob が[プライベート アクセス用に構成](../articles/storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)されていることを確認します。

1. ID に、パッケージ blob に対するスコープを持つ [Storage Blob データリーダー](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) ロールを付与します。 ロールの割り当ての作成の詳細については、「[blob データにアクセスするための Azure ロールの割り当て](../articles/storage/blobs/assign-azure-role-data-access.md)」を参照してください。

1. `WEBSITE_RUN_FROM_PACKAGE` アプリケーション設定をパッケージの BLOB URL に設定します。 これは、"https://{storage-account-name}.blob.core.windows.net/{container-name}/{path-to-package}" のような形式になります。