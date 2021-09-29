---
title: マネージド ID の更新アクティビティとサインイン アクティビティを表示する
description: マネージド ID に対して行われたアクティビティと、マネージド ID によって実行される認証を表示する詳細な手順
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03ab5ed10dcb9127c5b62850228d22222b4225be
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814416"
---
# <a name="view-update-and-sign-in-activities-for-managed-identities"></a>マネージド ID の更新アクティビティとサインイン アクティビティを表示する

この記事では、マネージド ID に対して実行された更新と、マネージド ID によって行われたサインイン試行を表示する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料アカウントを新規登録](https://azure.microsoft.com/free/)してください。

## <a name="view-updates-made-to-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID に対して行われた更新を表示する

この手順では、ユーザー割り当てマネージド ID に対して実行された更新を表示する方法を示します

1. Azure portal で **[アクティビティ ログ]** に移動します。

 ![Azure portal でアクティビティ ログを参照する](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

2. **[フィルターの追加]** 検索ピルを選択し、 **[操作]** をリストから選択します。

![検索フィルターの作成を開始する](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

3. **[操作]** ドロップダウン リストに、"ユーザー割り当て ID を削除する" と "UserAssignedIdentities を書き込む" という操作名を入力します。

![検索フィルターに操作を追加する](./media/how-to-view-managed-identity-activity/add-operations-to-search-filter.png)

4. 一致する操作が表示された場合は、1 つを選択して概要を表示します。

![操作の概要を表示する](./media/how-to-view-managed-identity-activity/view-summary-of-operation.png)

5. **[JSON]** タブを選択して操作に関する詳細情報を表示し、**プロパティ** ノードまでスクロールして、変更された ID に関する情報を表示します。

![操作の詳細を表示する](./media/how-to-view-managed-identity-activity/view-json-of-operation.png)

## <a name="view-role-assignments-added-and-removed-for-managed-identities"></a>マネージド ID に対して追加および削除されたロールの割り当てを表示する

 > [!NOTE] 
 > ロールの割り当ての変更を表示するマネージド ID のオブジェクト (プリンシパル) ID を使用して検索する必要があります

1. ロールの割り当ての変更を表示するマネージド ID を見つけます。 システム割り当てマネージド ID を探している場合は、リソースの下の **[ID]** 画面にオブジェクト ID が表示されます。 ユーザー割り当て ID を探している場合は、マネージド ID の **[概要]** ページにオブジェクト ID が表示されます。

ユーザー割り当て ID:

![ユーザー割り当て ID のオブジェクト ID を取得する](./media/how-to-view-managed-identity-activity/get-object-id-of-user-assigned-identity.png)

システム割り当て ID:

![システム割り当て ID のオブジェクト ID を取得する](./media/how-to-view-managed-identity-activity/get-object-id-of-system-assigned-identity.png)

2. [オブジェクト ID] をコピー します。
3. **[アクティビティ ログ]** を参照します。

 ![Azure portal でアクティビティ ログを参照する](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

4. **[フィルターの追加]** 検索ピルを選択し、 **[操作]** をリストから選択します。

![検索フィルターの作成を開始する](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

5. **[操作]** ドロップダウン リストに、"ロールの割り当ての作成" と "ロールの割り当ての削除" という操作名を入力します。

![検索フィルターにロールの割り当て操作を追加する](./media/how-to-view-managed-identity-activity/add-role-assignment-operations-to-search-filter.png)

6. 検索ボックスにオブジェクト ID を貼り付けます。結果は自動的にフィルター処理されます。

![オブジェクト ID で検索する](./media/how-to-view-managed-identity-activity/search-by-object-id.png)
 
7. 一致する操作が表示された場合は、1 つを選択して概要を表示します。
 
![マネージド ID のロールの割り当ての概要](./media/how-to-view-managed-identity-activity/summary-of-role-assignment-for-msi.png)

## <a name="view-authentication-attempts-by-managed-identities"></a>マネージド ID で認証の試行を表示する

1. **[Azure Active Directory]** を参照します。

![Active Directory を参照する](./media/how-to-view-managed-identity-activity/browse-to-active-directory.png)

2.  **[監視]** セクションから **[サインイン ログ]** を選択します。

![サインイン ログを選択する](./media/how-to-view-managed-identity-activity/sign-in-logs-menu-item.png)

3. **[マネージド ID のサインイン]** タブを選択します。

![マネージド ID のサインイン](./media/how-to-view-managed-identity-activity/msi-sign-ins.png)

4. これで、サインイン イベントがマネージド ID によってフィルター処理されます。

![マネージド ID のサインイン イベント](./media/how-to-view-managed-identity-activity/msi-sign-in-events.png) 

5.  Azure Active Directory 内で ID のエンタープライズ アプリケーションを表示するには、[マネージド ID] 列を選択します。
6.  Azure リソースまたはユーザー割り当てマネージド ID を表示するには、Azure portal の検索バーで名前を使用して検索します。

## <a name="next-steps"></a>次のステップ

* [Azure リソースのマネージド ID](./overview.md)
* [Azure アクティビティ ログ](../../azure-monitor/essentials/activity-log.md)
* [Azure Active Directory のサインイン ログ](../reports-monitoring/concept-sign-ins.md)