---
title: Azure Security Center に対するテナント全体の可視性を確保する | Microsoft Docs
description: Azure Security Center 内におけるテナント全体の可視性の確保について説明します。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: terrylan
ms.openlocfilehash: 800ec83b3599dba716e7a4a015b9b8c1745a0975
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144569"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Azure Security Center に対するテナント全体の可視性を確保する
この記事では、いくつかのアクションを実行して、Azure Security Center が提供するメリットを最大化する方法について説明します。 これらのアクションを実行することで、自分の Azure Active Directory テナントにリンクされているすべての Azure サブスクリプションへの可視性を確保できると共に、複数のサブスクリプションに対してセキュリティ ポリシーを集合的に適用することで、組織のセキュリティ体制を広範囲にわたって効果的に管理することができます。

## <a name="management-groups"></a>管理グループ
Azure の管理グループを使用すると、一連のサブスクリプションに対するアクセス、ポリシー、およびレポートを効率的に管理できると共に、ルート管理グループに対してアクションを実行することで、Azure 資産全体を効果的に管理することができます。 各 Azure AD テナントには、ルート管理グループと呼ばれる 1 つの最上位管理グループがあります。 このルート管理グループは階層に組み込まれており、すべての管理グループとサブスクリプションはルート管理グループにまとめられます。 このグループにより、グローバル ポリシーと RBAC の割り当てをディレクトリ レベルで適用できます。 

ルート管理グループは、次のいずれかのアクションを実行したときに自動的に作成されます。 
1. [Azure Portal](https://portal.azure.com) の **[管理グループ]** に移動して、Azure の管理グループを使用するよう選択する。
2. API 呼び出しを通じて管理グループを作成する。
3. PowerShell を使用して管理グループを作成する。

管理グループの概要について詳しくは、「[Organize your resources with Azure management groups](../azure-resource-manager/management-groups-overview.md)」(Azure 管理グループでリソースを整理する) の記事をご覧ください。

## <a name="create-a-management-group-in-the-azure-portal"></a>Azure Portal で管理グループを作成する
管理者は、管理グループ内にサブスクリプションを整理し、それらの管理グループに管理ポリシーを適用できます。 管理グループ内のすべてのサブスクリプションは、管理グループに適用されたポリシーを自動的に継承します。 管理グループは Security Center をオンボードするのに必須のものではありませんが、管理グループを少なくとも 1 つ作成して、ルート管理グループが作成されるようにすることを強くお勧めします。 グループが作成されると、自分の Azure AD テナントの配下にあるすべてのサブスクリプションがそのグループにリンクされます。 PowerShell に関する操作説明と詳細については、「[リソースの整理と管理のための管理グループを作成する](../azure-resource-manager/management-groups-create.md)」をご覧ください。

 
1. [Azure ポータル](http://portal.azure.com)にサインインします。
2. **[すべてのサービス]** > **[管理グループ]** を選択します。
3. メイン ページで、**[New Management group]\(新しい管理グループ\)** を選択します。 

    ![メイン グループ](./media/security-center-management-groups/main.png) 
4.  [管理グループ ID] フィールドに入力します。 
    - **[管理グループ ID]** は、この管理グループでコマンドを送信するために使用するディレクトリの一意識別子です。 この識別子は、このグループを識別するために Azure システム全体で使用されるため、作成後は編集できません。 
    - 表示名フィールドは、Azure Portal 内で表示される名前です。 管理グループの作成時には別の表示名は省略可能なフィールドで、いつでも変更できます。  

      ![Create](./media/security-center-management-groups/create_context_menu.png)  
5.  **[保存]** を選びます。

### <a name="view-management-groups-in-the-azure-portal"></a>Azure Portal で管理グループを表示する
1. [Azure Portal](http://portal.azure.com) にサインインします。
2. 管理グループを表示するには、Azure メイン メニューの **[すべてのサービス]** を選択します。
3. **[全般 ]** の **[管理グループ]** を選択します。

    ![管理グループの作成](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>テナント レベルの可視性を確保し、ポリシーの割り当てを行えるようにする

Azure AD テナントに登録されているすべてのサブスクリプションのセキュリティ体制に対する可視性を確保するには、十分な読み取りアクセス許可を持った RBAC ロールがルート管理グループに対して割り当てられている必要があります。

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Azure Active Directory でグローバル管理者のアクセス権を昇格する
Azure Active Directory テナント管理者には、Azure サブスクリプションに対する直接のアクセス権はありません。 ただし、ディレクトリ管理者には、アクセス権のあるロールへと自身を昇格する権限があります。 Azure AD テナント管理者は、自身をルート管理グループ レベルのユーザー アクセス管理者へと昇格して、RBAC ロールの割り当てを行えるようにする必要があります。 PowerShell に関する操作説明と追加情報については、「[Azure Active Directory でグローバル管理者のアクセス権を昇格する](../role-based-access-control/elevate-access-global-admin.md)」をご覧ください。 


1. [Azure Portal](https://portal.azure.com) または [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。

2. ナビゲーション リストで **[Azure Active Directory]**、**[プロパティ]** の順にクリックします。

   ![Azure AD のプロパティ - スクリーンショット](./media/security-center-management-groups/aad-properties.png)

3. **[全体管理者は、Azure サブスクリプションと管理グループを管理できます]** のスイッチを **[はい]** に設定します。

   ![[全体管理者は、Azure サブスクリプションと管理グループを管理できます] - スクリーンショット](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - スイッチを **[はい]** に設定すると、全体管理者アカウント (現在ログインしているユーザー) は、ルート スコープ (`/`) にある Azure RBAC のユーザー アクセス管理者ロールに追加されます。これで、Azure AD テナントに関連付けられているすべての Azure サブスクリプションについて表示およびレポートできるアクセス権が付与されます。

   - **[いいえ]** にスイッチを設定すると、全体管理者アカウント (現在ログインしているユーザー) は Azure RBAC のユーザー アクセス管理者ロールから削除されます。 Azure AD テナントに関連付けられているすべての Azure サブスクリプションは表示できません。アクセス権が付与されている Azure サブスクリプションのみを表示および管理できます。

4. **[保存]** をクリックして設定を保存します。

    - この設定はグローバル プロパティではなく、現在ログインしているユーザーのみに適用されます。

5. 昇格したアクセス権で行う必要のあるタスクを実行します。 完了したら、スイッチを **[いいえ]** に戻します。

### <a name="open-or-refresh-security-center"></a>Security Center を開くか更新する
昇格されたアクセス権を取得できたら、Azure Security Center を開くか更新して、Azure AD テナントの配下にあるすべてのサブスクリプションに対する可視性が確保されていることを確認します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. サブスクリプション セレクターで、Security Center で表示するすべてのサブスクリプションを選択していることを確認します。
    ![サブスクリプション セレクターのスクリーンショット](./media/security-center-management-groups/subscription-selector.png)
1. Azure メイン メニューの **[すべてのサービス]** を選択し、**[Security Center]** を選択します。
2. **[概要]** には、サブスクリプションの対象範囲のグラフが表示されます。 
    ![サブスクリプションの対象範囲グラフのスクリーンショット](./media/security-center-management-groups/security-center-subscription-coverage.png)
3. **[カバレッジ]** をクリックして、対象となっているサブスクリプションの一覧を表示します。 
    ![サブスクリプションの対象範囲一覧のスクリーンショット](./media/security-center-management-groups/security-center-coverage.png)

### <a name="assign-rbac-roles-to-users"></a>RBAC ロールをユーザーに割り当てる
テナント管理者のアクセス権が昇格されると、テナント管理者は RBAC ロールをルート管理グループ レベルで関連ユーザーに割り当てられるようになります。 割り当てが推奨されるロールは、[**閲覧者**](../role-based-access-control/built-in-roles.md#reader)です。 このロールは、テナント レベルの可視性を提供するために必要です。 割り当てられたロールは、ルート管理グループの配下のすべての管理グループとサブスクリプションに自動的に反映されます。 RBAC ロールについて詳しくは、[利用可能なロール](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles)に関する記事をご覧ください。 

1. [Azure PowerShell](/powershell/azure/install-azurerm-ps)をインストールします。
2. 次のコマンドを実行します。 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. メッセージが表示されたら、グローバル管理者の資格情報でサインインします。 

    ![サインイン プロンプトのスクリーンショット](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. 次のコマンドを実行して、閲覧者ロールのアクセス許可を付与します。

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. ロールを削除するには、次のコマンドを使用します。 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>昇格されたアクセス権を削除する 
RBAC ロールがユーザーに割り当てられたら、テナント管理者は自身をユーザー アクセス管理者ロールから削除する必要があります。

1. [Azure Portal](https://portal.azure.com) または [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。

2. ナビゲーション リストで **[Azure Active Directory]**、**[プロパティ]** の順にクリックします。

3. **[全体管理者は、Azure サブスクリプションと管理グループを管理できます]** のスイッチを **[いいえ]** に設定します。

4. **[保存]** をクリックして設定を保存します。



## <a name="adding-subscriptions-to-a-management-groups"></a>管理グループへのサブスクリプションの追加
管理者は、自分が作成した管理グループにサブスクリプションを追加できます。 以下の手順は、テナント全体の可視性を確保し、ポリシーやアクセス権をグローバルに管理するための必須の手順ではありません。

1. **[管理グループ]** で、サブスクリプションの追加先となる管理グループを選択します。

    ![サブスクリプションを追加する管理グループの選択](./media/security-center-management-groups/management-group-subscriptions.png)

2. **[既存のものを追加します]** を選択します。

    ![既存のものを追加します](./media/security-center-management-groups/add-existing.png)

3. **[Add existing resource]\(既存のリソースの追加\)** でサブスクリプションを入力し、**[保存]** をクリックします。

4. 手順 1 ～ 3 を繰り返して、スコープ内のすべてのサブスクリプションを追加します。

 > [!NOTE]
 > 管理グループには、サブスクリプションと子管理グループの両方を含めることができます。 親管理グループに対する RBAC ロールをユーザーに割り当てると、そのアクセス権は子管理グループのサブスクリプションによって継承されます。 親管理グループで設定されたポリシーは、子にも継承されます。 

## <a name="next-steps"></a>次の手順
この記事では、Azure Security Center に対するテナント全体の可視性を確保する方法について学習しました。 Security Center の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)

