---
title: Azure Active Directory の別のグループからグループを追加または削除する方法 | Microsoft Docs
description: Azure Active Directory を使用して別のグループからグループを追加または削除する方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: c28fe5ef226fac993fde221b16bfa875ba4845ca
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579770"
---
# <a name="how-to-add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>方法: Azure Active Directory を使用して別のグループからグループを追加または削除する
この記事は、Azure Active Directory を使用して別のグループからグループを追加および削除するのに役立ちます。

>[!Note]
>親グループを削除しようとする場合は、[グループとそのメンバーを更新または削除する方法](active-directory-groups-delete-group.md)に関するページをご覧ください。

## <a name="add-a-group-as-a-member-to-another-group"></a>メンバーとしてグループを別のグループに追加する
メンバー グループ (サブグループ) と親グループを作成して、既存のグループを別の既存のグループに追加できます。 メンバー グループには親グループの属性とプロパティが継承され、構成時間を節約できます。

### <a name="to-add-a-group-as-a-member-to-another-group"></a>メンバーとしてグループを別のグループに追加するには

1. ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、**[グループ]** を選択します。

3. **[グループ - すべてのグループ]** ページで、別のグループのメンバーになるグループを検索して選択します。 この演習では、**[MDM policy - West]\(MDM ポリシー - 西部\)** グループを使用します。

    >[!Note]
    >グループはメンバーとして、一度に 1 つのグループにのみ追加できます。 さらに、**[グループの選択]** ボックスでは、入力内容とユーザー名またはデバイス名の一部との一致に基づいて表示がフィルター処理されます。 ただし、ワイルドカード文字はサポートされません。

    ![[MDM policy - West]\(MDM ポリシー - 西部\) グループが選択された [グループ - すべてのグループ] ページ](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. **[MDM policy - West - Group memberships]\(MDM ポリシー - 西部 - グループ メンバーシップ\)** ページで、**[グループ メンバーシップ]**、**[追加]** の順に選択し、ご自分のグループをメンバーにする対象グループを検索して、**[選択]** を選びます。 この演習では、**[MDM policy - All org]\(MDM ポリシー - すべての組織\)** グループを使用します。

    現在、**[MDM policy - West]\(MDM ポリシー - 西部\)** グループは、[MDM policy - All org]\(MDM ポリシー - すべての組織\) グループのプロパティと構成をすべて継承して、**[MDM policy - All org]\(MDM ポリシー - すべての組織\)** グループのメンバーになっています。

    ![グループを別のグループに追加してグループ メンバーシップを作成する](media/active-directory-groups-membership-azure-portal/add-group-membership.png)

5. **[MDM policy - West - Group memberships]\(MDM ポリシー - 西部 - グループ メンバーシップ\)** ページを表示して、グループとメンバー リレーションを確認します。

    ![親グループを表示している [MDM policy - West - Group memberships]\(MDM ポリシー - 西部 - グループ メンバーシップ\) ページ](media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. グループとメンバー リレーションシップのより詳細な表示については、グループ名 (**[MDM policy - All org]\(MDM ポリシー - すべての組織\)**) を選択して、**[MDM policy - West]\(MDM ポリシー - 西部\)** ページの詳細を確認します。

    ![メンバーとグループの両方の詳細を表示している [グループ メンバーシップ] ページ](media/active-directory-groups-membership-azure-portal/group-membership-review.png)

## <a name="remove-a-member-group-from-another-group"></a>他のグループからメンバー グループを削除する
別のグループから既存のメンバー グループを削除できます。 しかし、メンバーシップを削除すると、ご自分のユーザーに継承された属性とプロパティもすべて削除されます。

### <a name="to-remove-a-member-group-from-another-group"></a>別のグループからメンバー グループを削除するには
1. **[グループ - すべてのグループ]** ページで、別のグループのメンバーとして削除されるグループを検索して選択します。 この演習では、もう一度 **[MDM ポリシー - 西部]** グループを使用します。

2. **[MDM policy - West]\(MDM ポリシー - 西部\)** 概要ページで、**[グループ メンバーシップ]** を選択します。

    ![[MDM policy - West]\(MDM ポリシー - 西部\) 概要ページ](media/active-directory-groups-membership-azure-portal/group-membership-overview.png)

3. **[MDM policy - West - Group memberships]\(MDM ポリシー - 西部 - グループ メンバーシップ\)** ページから **[MDM policy - All org]\(MDM ポリシー - すべての組織\)** グループを選択して、**[MDM policy - West]\(MDM ポリシー - 西部\)** ページの詳細から **[削除]** を選択します。

    ![メンバーとグループの両方の詳細を表示している [グループ メンバーシップ] ページ](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)


## <a name="additional-information"></a>追加情報
次の記事は、Azure Active Directory に関する追加情報を示します。

- [グループとメンバーを表示する](active-directory-groups-view-azure-portal.md)

- [基本的なグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md)

- [グループからメンバーを追加または削除する](active-directory-groups-members-azure-portal.md)

- [グループの設定を編集する](active-directory-groups-settings-azure-portal.md)

- [グループによるユーザーへのライセンスの割り当て](../users-groups-roles/licensing-groups-assign.md)
