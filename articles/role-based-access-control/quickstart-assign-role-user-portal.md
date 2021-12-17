---
title: チュートリアル:Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する - Azure RBAC
description: このチュートリアルでは、Azure portal と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセス権をユーザーに付与する方法について説明します。
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/15/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: 96064811bf0d82ab37a338adf3ed2ac4568c57f1
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069173"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>チュートリアル:Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する

[Azure ロールベースのアクセス制御 (Azure RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 このチュートリアルでは、ユーザーに対してリソース グループ内で仮想マシンを作成および管理するアクセス許可を付与します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リソース グループをスコープとするユーザーのアクセス権の付与
> * アクセス権の削除

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-resource-group"></a>リソース グループを作成する

1. ナビゲーション リストで、 **[リソース グループ]** をクリックします。

1. **[新規]** を クリックして、 **[リソース グループの作成]** ページを開 きます。

   ![新しいリソース グループを作成するページ。](./media/quickstart-assign-role-user-portal/resource-group.png)

1. サブスクリプションを選択します。

1. **[リソース グループ]** の名前フィールドに「**example-group**」または別の名前を入力します。

1. **[確認と作成]** をクリックし、 **[作成 ]** をクリックしてリソース グループを作成します。

1. **[更新]** をクリックしてリソース グループの一覧を更新します。

   新しいリソース グループがリソース グループの一覧に表示されます。

## <a name="grant-access"></a>アクセス権の付与

Azure RBAC でアクセス権を付与するには、Azure ロールを割り当てます。

1. **[リソース グループ]** の一覧で、新しい **example-group** リソース グループをクリックします。

1. ナビゲーション メニューで **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、ロールの割り当ての現在の一覧を表示します。

   ![リソース グループの [アクセス制御 (IAM)] ページ。](./media/shared/rg-role-assignments.png)


1. **[追加]**  >  **[ロールの割り当ての追加]** をクリックします。

   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[ロールの割り当ての追加] メニューが開いている [アクセス制御 (IAM)] ページ。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **[ロール]** タブ で、**Virtual Machine Contributor** ロールを選択 します。

    ![[ロール] タブが選択された [ロールの割り当ての追加] ページ。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **[メンバー]** タブで、自分または別のユーザーを選択します。

1. **[Review + assign]\(確認と割り当て\)** タブで、ロールの割り当ての設定を確認します。

1. **[Review + assign]\(確認と割り当て\)** をクリックしてロールを割り当てます。

   しばらくすると、example-group リソース グループ スコープにおける仮想マシン共同作成者ロールがユーザーに割り当てられます。

   ![仮想マシン共同作成者ロールの割り当て。](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>アクセス権を削除する

Azure RBAC でアクセス権を削除するには、ロールの割り当てを削除します。

1. ロールの割り当ての一覧で、仮想マシン共同作成者ロールを持つユーザーの横にチェック マークを追加します。

1. **[削除]** をクリックします。

   ![ロールの割り当ての削除メッセージ。](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. ロールの割り当ての削除メッセージが表示されたら、 **[はい]** をクリックします。

## <a name="clean-up"></a>クリーンアップ

1. ナビゲーション リストで、 **[リソース グループ]** をクリックします。

1. **example-group** をクリックしてリソース グループを開きます。

1. **[リソース グループの削除]** をクリックしてリソース グループを削除します。

1. **[削除しますか]** ペインにリソース グループ名 を入力して **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Azure PowerShell を使用して Azure リソースへのアクセス権をユーザーに付与する](tutorial-role-assignments-user-powershell.md)
