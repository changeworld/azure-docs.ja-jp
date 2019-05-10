---
title: Azure Active Directory 統合による Azure Cosmos DB のロールベースのアクセス制御
description: Azure Cosmos DB が Active Directory 統合 (RBAC) を使用してデータベース保護を提供する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e43341e59e807fdc20033d909beadb9d582149b5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080641"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB のロールベースのアクセス制御

Azure Cosmos DB には、Azure Cosmos DB の一般的な管理シナリオに対応するロールベースのアクセス制御 (RBAC) が組み込まれています。 Azure Active Directory にプロファイルを持つ個人は、これらの RBAC ロールをユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てて、Azure Cosmos DB リソース上のリソースと操作へのアクセスを許可または拒否できます。 ロールの割り当ては、Azure Cosmos アカウント、データベース、コンテナー、およびオファー (スループット) へのアクセスを含む、コントロールプレーン アクセスのみに限定されています。

## <a name="built-in-roles"></a>組み込みのロール

Azure Cosmos DB でサポートされている組み込みのロールは次のとおりです。

|**組み込みのロール**  |**説明**  |
|---------|---------|
|[DocumentDB アカウント共同作成者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Azure Cosmos DB アカウントを管理できます。  |
|[Cosmos DB アカウント閲覧者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Cosmos DB アカウントのデータを読み取ることができます。        |
|[Cosmos バックアップ オペレーター](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Azure Cosmos データベースまたはコンテナーの復元要求を送信できます。       |
|[Cosmos DB オペレーター](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Azure Cosmos アカウント、データベース、およびコンテナーをプロビジョニングできますが、データへのアクセスに必要なキーにはアクセスできません。         |

> [!IMPORTANT]
> Azure Cosmos DB の RBAC サポートは、コントロール プレーン操作にのみ適用されます。 データ プレーン操作は、マスター キーまたはリソース トークンを使用してセキュリティで保護されています。 詳細については、「[Azure Cosmos DB のデータへのアクセスをセキュリティで保護する](secure-access-to-data.md)」を参照してください。

## <a name="identity-and-access-management-iam"></a>ID およびアクセス管理 (IAM)

Azure portal の **[アクセス制御 (IAM)]** ウィンドウは、Azure Cosmos リソースに対してロールベースのアクセス制御を構成するために使用されます。 ロールは、Active Directory 内のユーザー、グループ、サービス プリンシパル、およびマネージド ID に適用されます。 組み込みのロールまたはカスタム ロールは、個人とグループに使用できます。 次のスクリーンショットは、Azure portal のアクセス制御 (IAM) を使用した Active Directory 統合 (RBAC) を示しています。

![Azure Portal でのアクセス制御 (IAM) - データベースのセキュリティ](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>カスタム ロール

組み込みのロールに加えて、ユーザーは Azure で[カスタム ロール](../role-based-access-control/custom-roles.md)を作成し、それらのロールを Active Directory テナント内のすべてのサブスクリプションにわたるサービス プリンシパルに適用することもできます。 カスタム ロールによって、ユーザーは、カスタムのリソース プロバイダー操作セットを使用して RBAC ロール定義を作成できるようになります。 Azure Cosmos DB のカスタム ロールを構築するために使用できる操作の詳細については、「[Azure Cosmos DB のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure リソースのロールベースのアクセス制御 (RBAC) の概要](../role-based-access-control/overview.md)
- [Azure リソースのカスタム ロール](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB のデータへのアクセスをセキュリティで保護する](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
