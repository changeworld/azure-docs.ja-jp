---
title: Azure ロールの割り当て条件を使用して BLOB へのアクセスを認可する (プレビュー)
titleSuffix: Azure Storage
description: Azure ロールの割り当て条件と Azure 属性ベースのアクセス制御 (Azure ABAC) を使用して Azure の BLOB へのアクセスを認可します。 ストレージ属性を使用してロールの割り当てに関する条件を定義します。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 0aeb8766822bbfe19dfdd70ed242e7176dd44117
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062785"
---
# <a name="authorize-access-to-blobs-using-azure-role-assignment-conditions-preview"></a>Azure ロールの割り当て条件を使用して BLOB へのアクセスを認可する (プレビュー)

属性ベースのアクセス制御 (ABAC) は、セキュリティ プリンシパル、リソース、要求、環境に関連付けられた属性に基づいてアクセス レベルを定義する認可戦略です。 Azure ABAC は、既存の ID およびアクセス管理 (IAM) システムの [Azure ロールの割り当てへの条件](../../role-based-access-control/conditions-overview.md)の追加によって、Azure のロールベースのアクセス制御 (Azure RBAC) 上に構築されます。 このプレビューには、BLOB と Data Lake Storage Gen2 のロールの割り当て条件のサポートが含まれています。 これにより、リソースおよび要求の属性に基づいてロールの割り当て条件を作成することができます。

> [!IMPORTANT]
> Azure ABAC と Azure のロールの割り当て条件は、現在プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="overview-of-conditions-in-azure-storage"></a>Azure Storage での条件の概要

Azure Storage では、[Azure Active Directory (Azure AD) を使用](authorize-data-access.md)して BLOB、キューおよびテーブル リソースへの要求を認可することができます。 Azure AD は、Azure RBAC を使用して、セキュリティで保護されたリソースへのアクセス権を承認します。 BLOB およびキュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure [組み込みロール](../../role-based-access-control/built-in-roles.md#storage)は、Azure Storage によって定義されます。 選択したアクセス許可セットを使用してカスタム ロールを定義することもできます。 Azure Storage では、ストレージ アカウントまたは BLOB コンテナーに対するロールの割り当てがサポートされます。

ただし、場合によっては、リソースへのきめ細かなアクセスを有効にしたり、ストレージ リソースに対する数百件のロール割り当てを簡素化したりする必要があります。 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) の[ロールの割り当ての条件](../../role-based-access-control/conditions-overview.md)を構成して、これらのゴールを達成できます。 [カスタム役割](../../role-based-access-control/custom-roles.md)で条件を使用したり、組み込みロールを選択したりすることができます。 [ストレージ リソース プロバイダー](/rest/api/storagerp)を介した管理[アクション](../../role-based-access-control/role-definitions.md#actions)では条件はサポートされていません。

BLOB では Azure Storage の条件がサポートされます。 [階層型名前空間](../blobs/data-lake-storage-namespace.md) (HNS) 機能が有効になっているアカウントで条件を使用できます。 現在、Azure Storage のキュー、テーブル、またはファイル リソースでは、条件はサポートされていません。

## <a name="supported-attributes-and-operations"></a>サポートされる属性と操作

このプレビューでは、組み込みロールまたはカスタム ロールに条件を追加できます。 カスタム ロールを使用すると、重要なアクセス許可またはデータ アクションのみをユーザーに付与できます。 このプレビューでサポートされている組み込みロールには、[Storage Blob データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)、[Storage Blob データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)、[Storage Blob データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)があります。

[BLOB インデックス タグ](../blobs/storage-manage-find-blobs.md)に基づく条件を使用している場合は、タグ操作のアクセス許可が *Storage Blob データ所有者* に含まれているので、このロールを使用する必要があります。

> [!NOTE]
> BLOB インデックス タグは、階層型名前空間を使用する Data Lake Storage Gen2 ストレージ アカウントではサポートされていません。 HNS が有効になっているストレージ アカウントでは、インデックス タグを使用してロール割り当て条件を作成しないでください。

[Azure ロールの割り当て条件の形式](../../role-based-access-control/conditions-format.md)では、条件で `@Resource` または `@Request` 属性を使用できます。 `@Resource` 属性では、アクセスされているストレージ リソースの既存の属性 (ストレージ アカウント、コンテナー、BLOB など) を参照します。 `@Request` 属性では、ストレージ操作要求に含まれる属性を参照します。

各 DataAction でサポートされる属性の完全な一覧については、[Azure Storage での Azure ロールの割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md) に関するページをご覧ください。

## <a name="see-also"></a>こちらもご覧ください

- [Azure Storage での Azure ロールの割り当て条件についてのセキュリティ上の考慮事項 (プレビュー)](storage-auth-abac-security.md)
- [Azure Storage での Azure ロールの割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md)
- [Azure 属性ベースのアクセス制御 (Azure ABAC) とは (プレビュー)](../../role-based-access-control/conditions-overview.md)