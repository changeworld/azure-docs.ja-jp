---
title: RBAC と Azure portal を使用してアクセスを管理する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) と Azure portal を使用してユーザー、グループ、アプリケーションのアクセス権を管理する方法を説明します。 具体的には、アクセス権の一覧表示、付与、削除などを取り上げます。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6544503353f085a9dd9b0fe7207bbf8d4e0bc9d0
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435619"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>RBAC と Azure portal を使用してアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](overview.md) は、Azure に存在するリソースに対するアクセス権を管理するための手法です。 この記事では、RBAC と Azure portal を使用してユーザー、グループ、アプリケーションのアクセス権を管理する方法を説明します。

## <a name="list-roles"></a>ロールの一覧表示

ロール定義とは、ロールの割り当てに使用するアクセス許可のコレクションを指します。 Azure には、[組み込みのロール](built-in-roles.md)が 60 種類以上用意されています。

1. Azure portal で、**[すべてのサービス]**、**[サブスクリプション]** の順に選択します。

1. サブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択します。

   ![ロールのオプション](./media/role-assignments-portal/list-subscription-access-control.png)

1. **[ロール]** を選択すると、組み込みのロールとカスタム ロールが一覧表示されます。

   ![ロールのオプション](./media/role-assignments-portal/roles-option.png)

   各ロールに割り当てられているユーザーとグループの数を確認できます。

   ![ロールの一覧表示](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>アクセス権の表示

アクセス権を管理するときは、どのユーザーがアクセス権を持っており、どのようなアクセス許可が認められていて、そのアクセス許可がどのようなレベルのものであるかを把握する必要があります。 アクセス権を一覧表示するには、ロールの割り当てを一覧表示します。

### <a name="list-role-assignments-for-a-subscription"></a>サブスクリプションのロールの割り当ての一覧表示

1. Azure portal で、**[すべてのサービス]**、**[サブスクリプション]** の順に選択します。

1. サブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択します。

    [アクセス制御 (IAM)] ブレードで、このサブスクリプションにアクセスできる人物と、そのロールを確認できます (IAM は、"ID およびアクセス管理" と呼ばれることもあります)。

    ![[アクセス制御 (IAM)] ブレード](./media/role-assignments-portal/subscription-access-control.png)

    RBAC モデルでは、従来のサブスクリプションの管理者と共同管理者がサブスクリプションの所有者として扱われます。


### <a name="list-role-assignments-for-a-resource-group"></a>リソース グループに対するロールの割り当ての一覧表示

1. ナビゲーション リストで、**[リソース グループ]** を選択します。

1. リソース グループを選択し、**[アクセス制御 (IAM)]** を選択します。

   [アクセス制御 (IAM)] ブレードで、このリソース グループにアクセスできる人物を確認できます。 スコープが **[このリソース]** のロールもあれば、別のスコープからスコープを **[(継承)]** しているロールもあることに注目してください。 アクセス権は、リソース グループに明示的に割り当てられる場合と、親サブスクリプションへの割り当てから継承される場合があります。

   ![リソース グループ](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

1. ナビゲーション リストで、**[Azure Active Directory]** を選択します。

1. **[ユーザー]** を選択して、**[すべてのユーザー]** を開きます。

   ![Azure Active Directory の [すべてのユーザー] ブレード](./media/role-assignments-portal/aad-all-users.png)

1. 一覧から個々のユーザーを選択します。

1. **[管理]** セクションで、**[Azure リソース]** を選択します。

   ![Azure Active Directory のユーザーの Azure リソース](./media/role-assignments-portal/aad-user-azure-resources.png)

   [Azure リソース] ブレードで、選択したユーザーのロールの割り当てを確認できます。 この一覧では、自分が読み取りアクセス許可を認められているリソースについてのみ、ロールの割り当てが表示されます。 たとえば、選択したユーザーに別のサブスクリプションのロールが割り当てられていても、そのサブスクリプションについて自分に読み取りアクセス許可が認められていない場合には、そのロールの割り当てが一覧に表示されることはありません。

## <a name="grant-access"></a>アクセス権の付与

RBAC でアクセス権を付与するには、ロールの割り当てを作成します。

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>サブスクリプションをスコープとするロールの割り当ての作成

1. Azure portal で、**[すべてのサービス]**、**[サブスクリプション]** の順に選択します。

1. サブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択すると、サブスクリプションをスコープとするロールの割り当ての現在の一覧が表示されます。

   ![リソース グループの [アクセス制御 (IAM)] ブレード](./media/role-assignments-portal/grant-subscription-access-control.png)

1. **[追加]** を選択して **[アクセス許可の追加]** ウィンドウを開きます。

   ロールを割り当てるためのアクセス許可がない場合には、**[追加]** オプションが表示されません。

   ![[アクセス許可の追加] ウィンドウ](./media/role-assignments-portal/add-permissions.png)

1. **[ロール]** ボックスの一覧で、**[仮想マシン共同作成者]** などのロールを選択します。

1. **[選択]** の一覧で、ユーザー、グループ、またはアプリケーションを選択します。 一覧にセキュリティ プリンシパルが表示されない場合には、**[選択]** ボックスに表示名、メール アドレス、オブジェクト識別子を入力してディレクトリを検索します。

1. **[保存]** を選択して、ロールの割り当てを作成します。

   何分か待つと、セキュリティ プリンシパルにサブスクリプションをスコープとするロールが割り当てられます。

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>リソース グループをスコープとするロールの割り当ての作成

1. ナビゲーション リストで、**[リソース グループ]** を選択します。

1. リソース グループを選択します。

1. **[アクセス制御 (IAM)]** を選択すると、リソース グループをスコープとするロールの割り当ての現在の一覧が表示されます。

   ![リソース グループの [アクセス制御 (IAM)] ブレード](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. **[追加]** を選択して **[アクセス許可の追加]** ウィンドウを開きます。

   ロールを割り当てるためのアクセス許可がない場合には、**[追加]** オプションが表示されません。

   ![[アクセス許可の追加] ウィンドウ](./media/role-assignments-portal/add-permissions.png)

1. **[ロール]** ボックスの一覧で、**[仮想マシン共同作成者]** などのロールを選択します。

1. **[選択]** の一覧で、ユーザー、グループ、またはアプリケーションを選択します。 一覧にセキュリティ プリンシパルが表示されない場合には、**[選択]** ボックスに表示名、メール アドレス、オブジェクト識別子を入力してディレクトリを検索します。

1. **[保存]** を選択して、ロールの割り当てを作成します。

   何分か待つと、セキュリティ プリンシパルにリソース グループをスコープとするロールが割り当てられます。

## <a name="remove-access"></a>アクセス権の削除

RBAC では、アクセス権を削除するにはロールの割り当てを削除する必要があります。

### <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

1. 削除するロールの割り当てが存在するサブスクリプション、リソース グループ、またはリソースの **[アクセス制御 (IAM)]** ブレードを開きます。

1. ロールの割り当ての一覧で、ロールの割り当てを削除するセキュリティ プリンシパルの隣にチェックマークを追加します。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **[削除]** を選択します。

   ![ロールの割り当ての削除メッセージ](./media/role-assignments-portal/remove-role-assignment.png)

1. ロールの割り当ての削除メッセージが表示されたら、**[はい]** をクリックします。

継承されたロールの割り当ては削除できません。 そのようなロールの割り当てを削除する場合には、ロールの割り当てが作成されたスコープで削除する必要があります。 **[スコープ]** 列の **[継承済み]** の横に、このロールが割り当てられているリソースへのリンクが表示されています。 その一覧に表示されているスコープにアクセスして、ロールの割り当てを削除してください。

## <a name="other-tools-to-manage-access"></a>その他のアクセス管理ツール

Azure ポータル以外のツールでも Azure RBAC コマンドを使用したアクセス管理とロールの割り当てを実行できます。 詳細については、次のリンクを参照してください。

* [Azure PowerShell](role-assignments-powershell.md)
* [Azure CLI](role-assignments-cli.md)
* [REST API](role-assignments-rest.md)

## <a name="next-steps"></a>次の手順

* [クイック スタート: RBAC と Azure portal を使用してユーザーにアクセス権を付与する](quickstart-assign-role-user-portal.md)
* [チュートリアル: RBAC と Azure PowerShell を使用してユーザーにアクセス権を付与する](tutorial-role-assignments-user-powershell.md)
* [組み込みのロール](built-in-roles.md)
