---
title: チュートリアル - RBAC と Azure portal を使用してユーザーにアクセス権を付与する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) を使用すると、Azure portal でロールを割り当てることによって、ユーザーにアクセス許可を付与することができます。
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
ms.date: 11/30/2018
ms.author: rolyon
ms.openlocfilehash: 8caa5c3b33ac1b483429251e0c1256636c4ece1a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634474"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>チュートリアル: RBAC と Azure Portal を使用してユーザーにアクセス権を付与する

[ロールベースのアクセス制御 (RBAC)](overview.md) は、Azure に存在するリソースに対するアクセス権を管理するための手法です。 このチュートリアルでは、ユーザーに対してリソース グループ内で仮想マシンを作成および管理するアクセス許可を付与します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リソース グループをスコープとするユーザーのアクセス権の付与
> * アクセス権の削除

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( http://portal.azure.com ) にサインインします。

## <a name="create-a-resource-group"></a>リソース グループの作成

1. ナビゲーション リストで、**[リソース グループ]** を選択します。

1. **[Add]\(追加)** を選択し、**[リソース グループ]** ブレードを開きます。

   ![新しいリソース グループの追加](./media/quickstart-assign-role-user-portal/resource-group.png)

1. **[リソース グループ名]** に **rbac-resource-group** と入力します。

1. サブスクリプションと場所を選択します。

1. **[作成]** を選択してリソース グループを作成します。

1. **[更新]** を選択してリソース グループの一覧を更新します。

   新しいリソース グループがリソース グループの一覧に表示されます。

   ![リソース グループの一覧](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>アクセス権の付与

RBAC でアクセス権を付与するには、ロールの割り当てを作成します。

1. **[リソース グループ]** の一覧で、新しい **rbac-resource-group** リソース グループを選択します。

1. **[アクセス制御 (IAM)]** を選択します。

1. **[ロールの割り当て]** タブを選択して、ロールの割り当ての現在の一覧を表示します。

   ![リソース グループの [アクセス制御 (IAM)] ブレード](./media/quickstart-assign-role-user-portal/access-control.png)

1. **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ウィンドウを開きます。

   ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

   ![[ロールの割り当ての追加] ウィンドウ](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. **[ロール]** ドロップダウン リストで、**[仮想マシン共同作成者]** を選択します。

1. **[選択]** 一覧で、自分または別のユーザーを選択します。

1. **[保存]** を選択して、ロールの割り当てを作成します。

   しばらくすると、rbac-resource-group リソース グループ スコープにおける仮想マシン共同作成者ロールがユーザーに割り当てられます。

   ![仮想マシン共同作成者ロールの割り当て](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>アクセス権の削除

RBAC では、アクセス権を削除するにはロールの割り当てを削除する必要があります。

1. ロールの割り当ての一覧で、仮想マシン共同作成者ロールを持つユーザーの横にチェック マークを追加します。

1. **[削除]** を選択します。

   ![ロールの割り当ての削除メッセージ](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. ロールの割り当ての削除メッセージが表示されたら、**[はい]** をクリックします。

## <a name="clean-up"></a>クリーンアップ

1. ナビゲーション リストで、**[リソース グループ]** を選択します。

1. **rbac-resource-group** を選択してリソース グループを開きます。

1. **[リソース グループの削除]** を選択してリソース グループを削除します。

   ![Delete resource group](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. **[削除しますか]** ブレードにリソース グループ名 **rbac-resource-group** を入力します。

1. **[削除する]** を選択してリソース グループを削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: RBAC と PowerShell を使用してユーザーにアクセス権を付与する](tutorial-role-assignments-user-powershell.md)

