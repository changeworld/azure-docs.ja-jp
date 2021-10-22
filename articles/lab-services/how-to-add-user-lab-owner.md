---
title: Azure Lab Services でラボに他の所有者を追加する方法
description: この記事では、Azure Lab Services で管理者がユーザーをラボに所有者として追加する方法について説明します。
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 531910013284abbddd73c2247b5f65494a74ddf1
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181010"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Azure Lab Services で既存のラボに所有者を追加する方法
この記事では、管理者が既存のラボに所有者を追加する方法について説明します。

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>ラボ アカウントの閲覧者ロールにユーザーを追加する
1. **[ラボ アカウント]** ページに戻り、左側のメニューの **[すべてのラボ]** を選択します。
2. 所有者としてユーザーを追加する **ラボ** を選択します。 

    ![ラボを選ぶ ](./media/how-to-add-user-lab-owner/select-lab.png)  
1. ナビゲーション メニューで **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロールの割り当ての追加 (プレビュー)]** を選択します。

    ![[ロールの割り当ての追加] メニューが開いている [アクセス制御 (IAM)] ページ。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **[ロール]** タブで、 **[閲覧者]** ロールを選択します。

    ![[ロール] タブが選択された [ロールの割り当ての追加] ページ。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **[メンバー]** タブで、閲覧者ロールに追加したいユーザーを選択します。

1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。
## <a name="add-user-to-the-owner-role-for-the-lab"></a>ラボの所有者ロールにユーザーを追加する

> [!NOTE]
> ユーザーがラボの閲覧者のアクセス権のみを持っている場合、ラボは labs.azure.com で表示されません。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。


1. **[ラボ アカウント]** ページで、 **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロールの割り当ての追加 (プレビュー)]** を選択します。

    ![[ロールの割り当ての追加] メニューが開いている [アクセス制御 (IAM)] ページ。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **[ロール]** タブで **[所有者]** ロールを選択します。

    ![[ロール] タブが選択された [ロールの割り当ての追加] ページ。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **[メンバー]** タブで、所有者のロールに追加したいユーザーを選択します。

1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。


## <a name="next-steps"></a>次のステップ
[Lab Services ポータル](https://labs.azure.com)にログインするときに、ユーザーにラボが表示されることを確認します。
