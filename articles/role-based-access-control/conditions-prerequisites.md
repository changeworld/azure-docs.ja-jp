---
title: Azure ロールの割り当て条件 (プレビュー) の前提条件
description: Azure ロールの割り当て条件 (プレビュー) の前提条件。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: e0da284f7ce9ac1c8699a6f22f2144ff6f3f8c80
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489801"
---
# <a name="prerequisites-for-azure-role-assignment-conditions-preview"></a>Azure ロールの割り当て条件 (プレビュー) の前提条件

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure ロールの割り当て条件を追加または編集するには、次の前提条件が必要です。

## <a name="storage-accounts"></a>ストレージ アカウント

BLOB インデックス タグを使用する条件の場合は、BLOB インデックス機能と互換性のあるストレージ アカウントを使用する必要があります。 たとえば、階層型名前空間 (HNS) が無効になっている General Purpose v2 (GPv2) ストレージ アカウントだけが現在サポートされています。 詳細については、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../storage/blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)」を参照してください

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用して条件を追加または更新する場合は、次のバージョンを使用する必要があります。

- [Az モジュール 5.5.0 以降](https://www.powershellgallery.com/packages/Az/5.5.0)
- [Az.Resources モジュール 3.2.1 以降](https://www.powershellgallery.com/packages/Az.Resources/3.2.1)
    - Az モジュール v5.5.0 以降に含まれていますが、PowerShell ギャラリーを使用して手動でインストールできます
- [Az.Storage プレビュー モジュール 2.5.2-preview 以降](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview)

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して条件を追加または更新する場合は、次のバージョンを使用する必要があります。

- [Azure CLI 2.18 以降](/cli/azure/install-azure-cli)

## <a name="permissions"></a>アクセス許可

ロールの割り当てと同様に、条件を追加または更新するには、`Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` アクセス許可を持つユーザー ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など) を使用して Azure にサインインする必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure ロール割り当て条件の例 (プレビュー)](../storage/common/storage-auth-abac-examples.md)
- [チュートリアル: Azure portal を使用して BLOB へのアクセスを制限するロール割り当て条件を追加する (プレビュー)](../storage/common/storage-auth-abac-portal.md)
