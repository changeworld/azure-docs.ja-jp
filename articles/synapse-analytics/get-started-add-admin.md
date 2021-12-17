---
title: 'クイックスタート: 管理者の追加の概要'
description: このチュートリアルでは、ワークスペースに別の管理ユーザーを追加する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7a03715a89b5319e341d1704719b020e1b61ef1d
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219422"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Synapse ワークスペースに管理者を追加する

このチュートリアルでは、Synapse ワークスペースに管理者を追加する方法について説明します。 このユーザーは、ワークスペースを完全に制御できます。

## <a name="overview"></a>概要

これまで、このファースト ステップ ガイドでは、ワークスペースで "*あなた*" が実行するアクティビティに重点を置いてきました。 手順 1. でワークスペースを作成したあなたが Synapse ワークスペースの管理者となります。 ここでは、別のユーザー Ryan (`ryan@contoso.com`) を管理者にします。 完了すると、Ryan はあなたがワークスペースで実行できるすべての操作を実行できるようになります。

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: ワークスペースの所有者ロール

1. Azure portal を開き、Synapse ワークスペースを開きます。
1. 左側で、 **[アクセス制御 (IAM)]** を選択します。
1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。
1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | 所有者 |
    | アクセスの割り当て先 | User |
    | メンバー | ryan@contoso.com |

    ![Azure portal でロール割り当てページを追加します。](../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. **[保存]** を選択します。 
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC: ワークスペースの Synapse 管理者ロール

`ryan@contoso.com` を、ワークスペースの Synapse RBAC **Synapse 管理者** ロールに割り当てます。

1. Synapse Studio でワークスペースを開きます。
1. 左側で、 **[管理]** を選択して管理ハブを開きます。
1. **[セキュリティ]** で **[アクセス制御]** を選択します。
1. **[追加]** を選択します。
1. **[スコープ]** は **[ワークスペース]** に設定されたままにします。
1. `ryan@contoso.com` を、**Synapse 管理者** ロールに割り当てます。 
1. 次に、**[適用]** を選択します。
 
## <a name="azure-rbac-role-assignments-on-the-workspaces-primary-storage-account"></a>Azure RBAC: ワークスペースのプライマリ ストレージ アカウントでのロールの割り当て

1. Azure portal で、ワークスペースのプライマリ ストレージ アカウントを開きます。
1. 左側で、 **[アクセス制御 (IAM)]** を選択します。
1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。
1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | ロール 1 | 所有者 |
    | ロール 2| Azure Storage Blob データ共同作成者|
    | アクセスの割り当て先 | User |
    | メンバー | ryan@contoso.com |

    ![Azure portal でロール割り当てページを追加します。](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="dedicated-sql-pools-db_owner-role"></a>専用 SQL プール: db_owner ロール

`ryan@contoso.com` を、ワークスペース内の専用 SQL プールごとに **db_owner** に割り当てます。

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)
