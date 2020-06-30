---
title: Azure リソースのマネージド ID
description: Azure リソースのマネージド ID の概要。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 05/20/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeeb7b2704474e030e00eda03e73fd523434a207
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976122"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID とは

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

クラウド開発者は、コードで Azure リソースにアクセスするための最も簡単で安全な方法を模索しているでしょう。 

この要件には Azure リソース用マネージド ID が役立ちます。それは、マネージド ID が次の機能を提供するためです。

- コード内に資格情報を含める必要性を**なくす**。
- 資格情報を自動的に**ローテーション**する。
- ID 管理に伴う作業を最小限に**減らす**。


## <a name="how-it-works"></a>しくみ 

マネージド ID をサポートするすべての Azure リソースは、トークンを取得することで、コード内に資格情報がなくてもデータを交換できます。 このプロセスは次のステップで構成されます。

 
1.  **有効にする** - リソースのマネージド ID を作成します。
2.  **アクセス権を付与する** - Azure RBAC を使用してリソースへのアクセスを許可します。
3.  **アクセスする** - 許可されたアクションを実行します。
4.  **無効にする** - マネージド ID を削除します。 

## <a name="managed-identity-types"></a>マネージド ID の種類

マネージド ID には、次の 2 種類があります。

- システム割り当てマネージド ID

- ユーザー割り当てマネージド ID


スタンドアロンの Azure リソースに対しては、**システム割り当ての**マネージド ID を有効にできます。 システム割り当てのマネージド ID では、ID 管理の観点から最も便利なサポートが提供されます。 1 回クリックするだけで、対象のリソースの ID の自動ライフサイクル管理を有効にすることができます。   

 ![システム割り当てマネージド ID](./media/overview/system-assigned.png)  

システム割り当てのマネージド ID ではスタンドアロン リソースに対して最も便利なソリューションが提供されますが、同じタスクのために Azure リソースのグループを管理する必要がある場合は状況が異なります。 このシナリオでは、ID を手動で作成し、このマスター ID を、グループ化する必要のあるすべての Azure リソースに割り当てる方が適切です。 この割り当ては、**ユーザー割り当て**マネージド ID と呼ばれます。 
  

## <a name="supported-services"></a>サポートされているサービス

Azure リソース用マネージド ID を使用して、Azure AD Authentication をサポートするサービスに対して認証を行うことができます。 Azure リソースのマネージド ID 機能をサポートする Azure サービスの一覧については、「[Services that support managed identities for Azure resources (Azure リソースのマネージド ID をサポートするサービス)](services-support-msi.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

以下のクイック スタートを使用して、Azure リソースのマネージド ID 機能を使ってみましょう。

* [Windows VM のシステム割り当てマネージド ID を使用して Resource Manager にアクセスする](tutorial-windows-vm-access-arm.md)
* [Linux VM のシステム割り当てマネージド ID を使用して Resource Manager にアクセスする](tutorial-linux-vm-access-arm.md)
