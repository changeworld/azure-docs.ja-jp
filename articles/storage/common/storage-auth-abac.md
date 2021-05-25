---
title: Azure ロールの割り当て条件を使用して BLOB へのアクセスを認可する (プレビュー)
titleSuffix: Azure Storage
description: Azure ロールの割り当て条件と Azure 属性ベースのアクセス制御 (Azure ABAC) を使用して Azure の BLOB へのアクセスを認可します。 ストレージ属性を使用してロールの割り当てに関する条件を定義します。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 5bba36eb13405d70379feaa561d258813ab52bec
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489872"
---
# <a name="authorize-access-to-blobs-using-azure-role-assignment-conditions-preview"></a>Azure ロールの割り当て条件を使用して BLOB へのアクセスを認可する (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

属性ベースのアクセス制御 (ABAC) は、セキュリティ プリンシパル、リソース、要求、環境に関連付けられた属性に基づいてアクセス レベルを定義する認可戦略です。 Azure ABAC は、既存の ID およびアクセス管理 (IAM) システムの [Azure ロールの割り当てへの条件](../../role-based-access-control/conditions-overview.md)の追加によって、Azure のロールベースのアクセス制御 (Azure RBAC) 上に構築されます。 このプレビューには、BLOB でのロールの割り当て条件のサポートが含まれているため、リソース属性と要求属性に基づいてロールの割り当て条件を作成できます。

## <a name="overview-of-conditions-in-azure-storage"></a>Azure Storage での条件の概要

Azure Storage では、[Azure Active Directory (Azure AD) を使用](storage-auth-aad.md)して Blob Storage および Queue Storage への要求を認可することができます。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、セキュリティで保護されたリソースへのアクセス権が認可されます。 BLOB およびキュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure [組み込みロール](../../role-based-access-control/built-in-roles.md#storage)は、Azure Storage によって定義されます。 選択したアクセス許可セットを使用してカスタム ロールを定義することもできます。 Azure Storage では、ストレージ アカウントまたは BLOB コンテナーに対するロールの割り当てがサポートされます。

ただし、場合によっては、ストレージ リソースへの詳細に設定されたアクセスを有効にしたり、ストレージ リソースに対する数百件のロールの割り当ての管理を簡素化したりする必要がある場合があります。 このような場合、[DataActions](../../role-based-access-control/role-definitions.md#dataactions) に対する[ロールの割り当てに関する条件](../../role-based-access-control/conditions-overview.md)として条件を構成することができます。 [ストレージ リソース プロバイダー](/rest/api/storagerp)の管理操作では、条件は現在サポートされていません。

[カスタム役割](../../role-based-access-control/custom-roles.md)で条件を使用したり、組み込みロールを選択したりすることができます。 カスタム役割を使用すると、ユーザーに付与する特定のアクセス許可またはデータ アクションのみを含むように設計できるという利点があります。 このプレビューでサポートされている組み込みロールには、Storage Blob データ共同作成者と Storage Blob データ閲覧者があります。 BLOB インデックス タグに基づく条件を使用している場合は、タグ操作のアクセス許可が所有者ロールに含まれているので、Storage Blob データ所有者を使用する必要がある場合があります。

BLOB では Azure Storage の条件がサポートされます。 これには、階層型名前空間の機能が有効になっているアカウントの BLOB が含まれます。 現在、ファイル、キュー、テーブルでは条件はサポートされていません。

## <a name="supported-attributes-and-operations"></a>サポートされる属性と操作

このプレビューでは、[Storage Blob データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)や [Storage Blob データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)など、BLOB データにアクセスするための組み込みロールに条件を追加できます。 [BLOB インデックス タグ](../blobs/storage-manage-find-blobs.md)に基づく条件を使用している場合は、タグ操作のアクセス許可が [Storage Blob データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)に含まれているので、このロールを使用する必要がある場合があります。

[Azure ロールの割り当て条件の形式](../../role-based-access-control/conditions-format.md)では、条件で `@Resource` または `@Request` 属性を使用できます。 `@Resource` 属性では、アクセスされているストレージ リソースの既存の属性 (ストレージ アカウント、コンテナー、BLOB など) を参照します。 `@Request` 属性では、ストレージ操作要求に含まれる属性を参照します。

Azure Storage では、各 DataAction のロールの割り当ての条件で使用できる要求またはリソース属性の選択したセットがサポートされます。 各 DataAction でサポートされる属性の完全な一覧については、[Azure Storage での Azure ロールの割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md) に関するページをご覧ください。

## <a name="see-also"></a>こちらもご覧ください

- [Azure Storage での Azure ロールの割り当て条件についてのセキュリティ上の考慮事項 (プレビュー)](storage-auth-abac-security.md)
- [Azure Storage での Azure ロールの割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md)
- [Azure 属性ベースのアクセス制御 (Azure ABAC) とは (プレビュー)](../../role-based-access-control/conditions-overview.md)