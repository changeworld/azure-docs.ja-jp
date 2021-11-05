---
title: サブスクリプションを管理グループに整理し、Microsoft Defender for Cloud のユーザーにロールを割り当てる
description: Microsoft Defender for Cloud の管理グループに Azure サブスクリプションを整理し、組織内のユーザーにロールを割り当てる方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/11/2021
ms.custom: subject-rbac-steps, ignite-fall-2021
ms.author: memildin
ms.openlocfilehash: 9c53c626685b0035c659687454b7ad1f49acd81d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092465"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>サブスクリプションを管理グループに整理し、ユーザーにロールを割り当てる

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このページでは、Azure Active Directory テナントにリンクされているすべての Azure サブスクリプションにセキュリティ ポリシーを適用することで、組織の大規模なセキュリティ体制を管理する方法について説明します。

Azure AD テナントにリンクされているすべてのサブスクリプションのセキュリティ対策を把握するには、十分な読み取りアクセス許可を持つ Azure ロールをルート管理グループに割り当てる必要があります。

## <a name="organize-your-subscriptions-into-management-groups"></a>サブスクリプションを管理グループに整理する

### <a name="introduction-to-management-groups"></a>管理グループの概要

管理グループを使用すると、**一連のサブスクリプション** に対するアクセス、ポリシー、およびレポートを効率的に管理できると共に、ルート管理グループに対してアクションを実行することで、Azure 資産全体を効果的に管理することができます。 管理者は、管理グループ内にサブスクリプションを整理し、それらの管理グループに管理ポリシーを適用できます。 管理グループ内のすべてのサブスクリプションは、管理グループに適用されたポリシーを自動的に継承します。 

各 Azure AD テナントには、**ルート管理グループ** と呼ばれる 1 つの最上位管理グループがあります。 このルート管理グループは階層に組み込まれており、すべての管理グループとサブスクリプションはルート管理グループにまとめられます。 このグループにより、グローバル ポリシーと Azure ロールの割り当てをディレクトリ レベルで適用できます。 

ルート管理グループは、次のいずれかのアクションを実行したときに自動的に作成されます。 
- **管理グループ** を [Azure portal](https://portal.azure.com) で表示する。
- API 呼び出しを使用して管理グループを作成する。
- PowerShell を使用して管理グループを作成する。 PowerShell を使用する場合の手順については、「[リソースの整理と管理のための管理グループを作成する](../governance/management-groups/create-management-group-portal.md)」を参照してください。

管理グループは Microsoft Defender for Cloud をオンボードするのに必須のものではありませんが、管理グループを少なくとも 1 つ作成して、ルート管理グループが作成されるようにすることをお勧めします。 グループが作成されると、自分の Azure AD テナントの配下にあるすべてのサブスクリプションがそのグループにリンクされます。 

管理グループの概要について詳しくは、「[Organize your resources with Azure management groups](../governance/management-groups/overview.md)」(Azure 管理グループでリソースを整理する) の記事をご覧ください。

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Azure portal で管理グループを表示および作成する

1. [Azure portal](https://portal.azure.com) で上部のバーの検索ボックスを使用し、 **[管理グループ]** を検索して開きます。

    :::image type="content" source="./media/management-groups-roles/open-management-groups-service.png" alt-text="管理グループへのアクセス。":::

    管理グループの一覧が表示されます。

1. 管理グループを作成するには、 **[管理グループの追加]** を選択し、関連する詳細情報を入力して、 **[保存]** を選択します。

    :::image type="content" source="media/management-groups-roles/add-management-group.png" alt-text="Azure への管理グループの追加。":::

    - **[管理グループ ID]** は、この管理グループでコマンドを送信するために使用するディレクトリの一意識別子です。 この識別子は、このグループを識別するために Azure システム全体で使用されるため、作成後は編集できません。 
    - 表示名フィールドは、Azure Portal 内で表示される名前です。 管理グループの作成時には別の表示名は省略可能なフィールドで、いつでも変更できます。  


### <a name="add-subscriptions-to-a-management-group"></a>管理グループにサブスクリプションを追加する
管理者は、自分が作成した管理グループにサブスクリプションを追加できます。

1. Azure portal から、**管理グループ** を開き、サブスクリプションの管理グループを選択します。

    :::image type="content" source="./media/management-groups-roles/management-group-subscriptions.png" alt-text="サブスクリプションの管理グループを選択する。":::

1. グループのページが開いたら、 **[サブスクリプション]** を選択します。

1. サブスクリプション ページで **[追加]** を選択し、サブスクリプションを選択して **[保存]** を選択します。 スコープ内のすべてのサブスクリプションを追加するまで、この手順を繰り返します。

    :::image type="content" source="./media/management-groups-roles/management-group-add-subscriptions.png" alt-text="管理グループへのサブスクリプションの追加。":::

   > [!IMPORTANT]
   > 管理グループには、サブスクリプションと子管理グループの両方を含めることができます。 親管理グループに対する Azure ロールをユーザーに割り当てると、そのアクセス権は子管理グループのサブスクリプションによって継承されます。 親管理グループで設定されたポリシーは、子にも継承されます。 



## <a name="assign-azure-roles-to-other-users"></a>Azure ロールを他のユーザーに割り当てる

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Azure portal を使用してユーザーに Azure ロールを割り当てる: 

1. [Azure portal](https://portal.azure.com) で上部のバーの検索ボックスを使用し、 **[管理グループ]** を検索して開きます。

    :::image type="content" source="./media/management-groups-roles/open-management-groups-service.png" alt-text="管理グループへのアクセス。":::

    管理グループの一覧が表示されます。

1.  関連する管理グループを選択します。

1. **[アクセス制御 (IAM)]** を選択し、 **[ロールの割り当て]** タブを開き、 **[追加]**   >  **[ロールの割り当ての追加]** を選択します。

    :::image type="content" source="./media/management-groups-roles/add-user.png" alt-text="管理グループへのユーザーの追加。":::

1. **[ロールの割り当ての追加]** ページ で、関連するロールを選択します。

    :::image type="content" source="./media/management-groups-roles/add-role-assignment-page.png" alt-text="[ロールの割り当ての追加] ページ。":::

1. **[メンバー]** タブで **[+ メンバーの 選択]** を選択 し、関連するメンバーにロールを割り当てます。

1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。


### <a name="assign-azure-roles-to-users-with-powershell"></a>PowerShell を使用してユーザーに Azure ロールを割り当てる: 

1. [Azure PowerShell](/powershell/azure/install-az-ps)をインストールします。
2. 次のコマンドを実行します。 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. メッセージが表示されたら、グローバル管理者の資格情報でサインインします。 

    ![サインイン プロンプトのスクリーンショット。](./media/management-groups-roles/azurerm-sign-in.PNG)

4. 次のコマンドを実行して、閲覧者ロールのアクセス許可を付与します。

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. ロールを削除するには、次のコマンドを使用します。 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>昇格されたアクセス権を削除する 

Azure ロールがユーザーに割り当てられたら、テナント管理者は自身をユーザー アクセス管理者ロールから削除する必要があります。

1. [Azure Portal](https://portal.azure.com) または [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。

2. ナビゲーション一覧で **Azure Active Directory** を選択し､ **[プロパティ]** を選択します｡

3. **Azure リソースのアクセス管理** の下で、スイッチを **[いいえ]** に設定します。

4. 設定を保存するには、 **[保存]** を選択します。



## <a name="next-steps"></a>次のステップ

このページでは、サブスクリプションを管理グループに整理し、ユーザーにロールを割り当てる方法について説明しました。 関連情報については、以下をご覧ください。

- [Microsoft Defender for Cloud のアクセス許可](permissions.md)
