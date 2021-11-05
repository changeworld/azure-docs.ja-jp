---
title: 資格情報を使用する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory で Azure 資格情報を使用する方法について説明します。
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: synapse
ms.openlocfilehash: a588bf8a02c6683143cff8096ee6e03c6b120234
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092586"
---
# <a name="credentials-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse における資格情報

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="prerequisites"></a>前提条件

ユーザーは、マネージド ID オペレーター (Azure RBAC) ロール、またはユーザー割り当てマネージド ID を資格情報として構成するための **Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action** RBAC アクションを持つカスタム ロールを持っている必要があります。 Synapse で資格情報を作成して使用するには、追加の RBAC が必要です。 [詳細については、こちらを参照してください](../synapse-analytics/security/synapse-workspace-synapse-rbac-roles.md)。

## <a name="using-credentials"></a>資格情報を使用する

ユーザー割り当てマネージド ID とサービス プリンシパルを含めることができ、さらに Azure Active Directory (AAD) 認証対応のリンクされたサービスで使用できるシステム割り当てマネージド ID を一覧表示することができる資格情報が導入されました。 これは、すべての AAD ベースの資格情報を統合して管理するのに役立ちます。  

次に示すのは、リンクされたサービスで認証に **ユーザー割り当てマネージド ID** を使用するための一般的な手順です。 

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1. Azure で作成したユーザー割り当てマネージド ID をお持ちでない場合は、まず Azure portal の [[マネージド ID]](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities) ページで作成します。

1. Azure portal、SDK、PowerShell、REST API を使用して、ユーザー割り当てマネージド ID をデータ ファクトリ インスタンスに関連付けます。 次のスクリーンショットでは、Azure portal (データ ファクトリ ブレード) を使用してユーザー割り当てマネージド ID を関連付けています。

   :::image type="content" source="media/credentials/uami-azure-portal.png" alt-text="Azure portal を使用してユーザー割り当てマネージド ID を関連付ける方法を示すスクリーンショット。":::

1. データ ファクトリのユーザー インターフェイスで、対話形式で **資格情報** を作成します。 手順 1 でデータ ファクトリに関連付けたユーザー割り当てマネージド ID を選択できます。 

   :::image type="content" source="media/credentials/create-new-credential.png" alt-text="新しい資格情報を作成する画面のスクリーンショット。":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="新しい資格情報の構成画面のスクリーンショット。":::

1. リンク サービスを新しく作成し、認証で **[ユーザー割り当てマネージド ID]** を選択します

   :::image type="content" source="media/credentials/create-new-linked-service.png" alt-text="ユーザー割り当てマネージド ID 認証を使用した、新しいリンクされたサービスを示すスクリーンショット。":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="ユーザー割り当てマネージド ID と資格情報が選択された新しいリンク サービスの構成を示すスクリーンショット。":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

1. Azure で作成したユーザー割り当てマネージド ID をお持ちでない場合は、まず Azure portal の [[マネージド ID]](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities) ページで作成します。

1. Azure portal、SDK、PowerShell、REST API を使用して、ユーザー割り当てマネージド ID をワークスペースに関連付けます。 次のスクリーンショットでは、Azure portal ([ID] ブレード) を使用してユーザー割り当てマネージド ID を関連付けています。

   :::image type="content" source="media/credentials/synapse-uami-azure-portal.png" alt-text="Azure portal を使用してユーザー割り当てマネージド ID を関連付ける方法を示すスクリーンショット。":::

1. Synapse Studio で対話形式で **資格情報** を作成します。 手順 1 でワークスペースに関連付けたユーザー割り当てマネージド ID を選択できます。

   :::image type="content" source="media/credentials/synapse-create-new-credential.png" alt-text="新しい資格情報を作成する画面のスクリーンショット。":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="新しい資格情報の構成画面のスクリーンショット。":::

1. リンク サービスを新しく作成し、認証で **[ユーザー割り当てマネージド ID]** を選択します

   :::image type="content" source="media/credentials/synapse-create-new-linked-service.png" alt-text="ユーザー割り当てマネージド ID 認証を使用した、新しいリンクされたサービスを示すスクリーンショット。":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="ユーザー割り当てマネージド ID と資格情報が選択された新しいリンク サービスの構成を示すスクリーンショット。":::

---

> [!NOTE] 
> 上記のアクションには、SDK/PowerShell/REST API を使用できます。

## <a name="next-steps"></a>次のステップ

- [管理対象 ID](data-factory-service-identity.md)

マネージド ID を使用する状況と方法を紹介する次のトピックをご覧ください。

- [Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)
- [Azure リソース認証用のマネージド ID を使用して Azure Data Lake Store との間でデータを双方向にコピーする](connector-azure-data-lake-store.md)

データ ファクトリのマネージド ID の基になっている Azure リソースのマネージド ID の詳細については、[Azure リソースのマネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関する記事をご覧ください。
