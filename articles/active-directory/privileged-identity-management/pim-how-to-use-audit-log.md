---
title: PIM での Azure AD ロールの監査レポートの表示 - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの監査履歴を表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a275b08beac842c7d435d77d6b4c1338e817fbc7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430102"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールの監査履歴を表示する

Privileged Identity Management (PIM) の監査履歴を使用すると、すべての特権ロールでの過去 30 日間におけるすべてのロール割り当てとアクティブ化を確認できます。 管理者、エンド ユーザー、同期アクティビティを含む Azure Active Directory (Azure AD) 組織内のアクティビティの完全な監査履歴を確認するには、[Azure Active Directory のセキュリティおよびアクティビティ レポート](../reports-monitoring/overview-reports.md)を使用できます。

## <a name="determine-your-version-of-pim"></a>PIM のバージョンを判断する

2019 年 11 月以降、Privileged Identity Management の Azure AD ロール部分は、Azure リソース ロールのエクスペリエンスに一致する新しいバージョンに更新されます。 これによって機能が追加され、[既存の API の変更](azure-ad-roles-features.md#api-changes)があります。 新しいバージョンのロールアウト中、この記事で実行する手順は、現在お使いになっている Privileged Identity Management のバージョンによって異なります。 このセクションの手順に従って、お使いになっている Privileged Identity Management のバージョンを確認してください。 Privileged Identity Management のバージョンを確認したら、この記事に記載されている手順のうち、そのバージョンに一致するものを選択することができます。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)のロールであるユーザーで [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。 概要ページの上部にバナーが表示されている場合は、この記事の **[新しいバージョン]** タブの指示に従ってください。 それ以外の場合は、 **[以前のバージョン]** タブの指示に従ってください。

    ![Azure AD ロールの新しいバージョン](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="previous-versiontabprevious"></a>[以前のバージョン](#tab/previous)

## <a name="view-audit-history"></a>監査履歴を表示する

Azure AD ロールの監査履歴を表示するには、次の手順に従います。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[ディレクトリ ロールの監査履歴]** を選択します。

    監査履歴に応じて、棒グラフ、アクティブ化の合計数、1 日当たりの最大アクティブ化数、および 1 日当たりの平均アクティブ化数が表示されます。

    ![ディレクトリ ロールの監査履歴](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    ページの下部に、使用可能な監査履歴内の各アクションに関する情報を含む表が表示されます。 列には次の意味があります。

    | 列 | [説明] |
    | --- | --- |
    | Time | アクションが発生した時刻。 |
    | 要求元 | ロールのアクティブ化または変更を要求したユーザー。 値が **[Azure システム]** の場合は、Azure 監査履歴で詳細を確認できます。 |
    | アクション | 要求元によって実行されたアクション。 アクションには、Assign、Unassign、Activate、Deactivate、または AddedOutsidePIM が含まれます。 |
    | メンバー | ロールをアクティブ化しているユーザー、またはロールに割り当てられているユーザー。 |
    | Role | ユーザーによって割り当てられたかアクティブ化されたロール。 |
    | 理由 | アクティブ化中に、[理由] フィールドに入力されたテキスト。 |
    | 有効期限 | アクティブ化されたロールが期限切れになる時刻。 資格のあるロールの割り当てにのみ適用されます。 |

1. 履歴を並べ替えるには、 **[時間]** 、 **[アクション]** 、および **[ロール]** の各ボタンを使用できます。

## <a name="filter-audit-history"></a>監査履歴をフィルター処理する

1. 監査履歴ページの上部にある **[フィルター]** ボタンをクリックします。

    **[グラフ パラメーターの更新]** ウィンドウが表示されます。

1. **[時間の範囲]** で、時間の範囲を選択します。

1. **[Roles]\(ロール\)** で、表示するロールを示すチェック ボックスをオンにします。

    ![グラフ パラメーター ウィンドウを更新する](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. **[完了]** を選択して、フィルター処理された監査履歴を表示します。

# <a name="new-versiontabnew"></a>[新しいバージョン](#tab/new)

Azure AD ロールの監査履歴を表示するには、次の手順に従います。

## <a name="view-resource-audit-history"></a>リソースの監査履歴を表示する

リソースの監査では、自分の Azure AD ロールに関連付けられているすべてのアクティビティを確認できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[リソースの監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![フィルターを使用したリソースの監査の一覧](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>自分の監査を表示する

自分の監査を使用すると、個人のロール アクティビティを表示できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. 監査履歴を表示するリソースを選択します。

1. **[自分の監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![現在のユーザーに対する監査の一覧](media/azure-pim-resource-rbac/my-audit-time.png)

---

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールのアクティビティおよび監査履歴を表示する](azure-pim-resource-rbac.md)
