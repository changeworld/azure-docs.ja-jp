---
title: "Office 365 テナントを Azure サブスクリプションで使用する | Microsoft Docs"
description: "Office 365 ディレクトリ (テナント) を Azure サブスクリプションに追加して両者の関連付けを行う方法について説明します。"
services: 
documentationcenter: 
author: JiangChen79
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 36cd9eac7be2d99971d8d2e227cd4b761df82d08


---
# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Office 365 テナントの Azure サブスクリプションへの関連付け
過去に Azure サブスクリプションと Office 365 サブスクリプションを別々に購入したが、今後は Azure サブスクリプションから Office 365 テナントにアクセスするとします。これは簡単に実行できます。 この記事では、その方法について説明します。

> [!NOTE]
> この記事は、Enterprise Agreement (EA) のお客様には適用されません。
> 
> 

## <a name="quick-guidance"></a>クイック ガイド
Office 365 テナントを Azure サブスクリプションに関連付けるには、まず Azure アカウントを使用して Office 365 テナントを追加した後、その Office 365 テナントに Azure サブスクリプションを関連付けます。

## <a name="detailed-steps"></a>詳細な手順
このシナリオでは、ユーザー Kelley Wall が Azure アカウント kelley.wall@outlook.com. で Azure サブスクリプションを持っています。さらに、Kelley は、アカウント kelley.wall@contoso.onmicrosoft.com. で Office 365 サブスクリプションを持っています。現在、Kelley は、Azure サブスクリプションを使用して Office 365 テナントにアクセスすることを望んでいます。

![Azure Active Directory の状態のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Office 365 管理センターのアクティブ ユーザーのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>前提条件
関連付けを正常に動作させるには、次の前提条件を満たす必要があります。

* Azure サブスクリプションのサービス管理者の資格情報が必要です。 共同管理者では一部の手順を実行できません。
* Office 365 テナントの全体管理者の資格情報が必要です。
* サービス管理者の電子メール アドレスが Office 365 テナントに含まれていないことが必要です。
* サービス管理者の電子メール アドレスが、Office 365 テナントのどの全体管理者の電子メール アドレスとも異なっていることが必要です。
* 現在、Microsoft アカウントと組織アカウントの両方に該当する電子メール アドレスを使用している場合は、別の Microsoft アカウントを使用するように、Azure サブスクリプションのサービス管理者を一時的に変更してください。 新しい Microsoft アカウントは、 [Microsoft アカウントのサインアップ ページ](https://signup.live.com/)で作成できます。

サービス管理者を変更するには、次の手順を実行します。

1. [アカウント管理ポータル](https://account.windowsazure.com/subscriptions)にサインインします。
2. 変更するサブスクリプションを選択します。
3. **[サブスクリプション詳細の編集]** をクリックします。
   
    ![[サブスクリプション詳細の編集] が強調表示されている Azure サブスクリプション情報のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)
4. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。
   
    ![[サブスクリプションの編集] ダイアログ ボックスのスクリーン ショット](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Office 365 テナントを Azure サブスクリプションに関連付ける
Office 365 テナントを Azure サブスクリプションに関連付けるには、次の手順を実行します。

1. サービス管理者の資格情報で[Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. 左ウィンドウで、**[Active Directory]** を選択します。
   
   ![Azure Active Directory のエントリのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
   
   > [!NOTE]
   > Office 365 テナントが表示されていないことを確認してください。 表示されている場合は、次の手順を省略します。
   > 
   > 
   
   ![Azure Active Directory の既定のディレクトリのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)
3. Office 365 テナントを Azure サブスクリプションに追加します。
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新規]**  >  **[ディレクトリ]**  >  **[カスタム作成]** を選択します。
   
    ![Azure Active Directory のカスタム作成のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
    b. **[ディレクトリの追加]** ページの **[ディレクトリ]** で、**[既存のディレクトリの使用]** を選択します。 次に、**[サインアウトする準備ができました。]**を選択し、**[完了]** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png) を選択します。
   
    ![[既存のディレクトリの使用] のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
    c. サインアウト後、Office 365 テナントの全体管理者の資格情報でサインインします。
   
    ![Office 365 グローバル管理者のサインインのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
    d. **[続行]** をクリックします。
   
    ![検証のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
    e. **[今すぐサインアウト]** を選択します。
   
    ![[保存] アイコンのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
    f. サービス管理者の資格情報で[Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
   
    ![Azure サインイン ページのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
    g. ダッシュボードに Office 365 テナントが表示されます。
   
    ![ダッシュボードのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)
4. Azure サブスクリプションに関連付けられているディレクトリを変更します。
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[設定]**を選択します。
   
    ![Azure クラシック ポータルの設定アイコンのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
    b. Azure サブスクリプションを選択し、**[ディレクトリの編集]** をクリックします。
    ![Azure サブスクリプションのディレクトリの編集のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
    c. **[次へ]** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png) を選択します。
   
    ![[関連付けられたディレクトリの変更] のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
   > [!WARNING]
   > すべての共同管理者が削除されることを伝える警告が表示されます。
   > 
   > 
   
    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
   > [!WARNING]
   > また、 [ロールベースのアクセス制御 (RBAC)](active-directory/role-based-access-control-configure.md) の下でアクセス権が割り当て済みになっているユーザーもすべて、既存のリソース グループから削除されます。 ただし、表示される警告で言及されるのは共同管理者の削除だけです。
   > 
   > 
   
    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
    d. **[完了]** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png) を選択します。
5. これで、Office 365 の組織アカウントを共同管理者として Azure Active Directory テナントに追加できます。
   
    a. **[管理者]** タブを選択し、**[追加]** を選択します。
   
    ![Azure クラシック ポータルの設定の [管理者] タブのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
    b. Office 365 テナントの組織アカウントを入力し、対象の Azure サブスクリプションを選択してから、**[完了]** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png) をクリックします。
   
    ![Azure の [共同管理者の追加] ダイアログ ボックスのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
    c. **[管理者]** タブに戻ります。 組織アカウントが共同管理者として表示されていることを確認します。
   
    ![[管理者] タブのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
6. 次に、共同管理者でのアクセスをテストします。
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 アカウント管理ポータルをサインアウトします。
   
    b. [Azure Portal](https://portal.azure.com/) を開きます。
   
    c. Azure サインイン ページに **[組織のアカウントを使ってサインインしてください]**というリンクが表示されている場合は、そのリンクを選択します。 それ以外の場合、この手順はスキップしてください。
   
    ![Azure サインイン ページのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)
   
    d. 共同管理者の資格情報を入力し、**[サインイン]** を選択します。
   
    ![Azure サインイン ページのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>次のステップ
関連するシナリオは次のとおりです。

* 既に Office 365 サブスクリプションがあり、Azure サブスクリプションを開始するにあたって既存の Office 365 ユーザー アカウントを Azure サブスクリプションに使用したい場合。
* 既に Azure サブスクリプションがあり、既存の Azure Active Directory のユーザー用に Office 365 サブスクリプションを取得したい場合。

具体的な方法については、「 [既存の Office 365 アカウントを Azure サブスクリプションに使用する (またはその逆)](billing-use-existing-office-365-account-azure-subscription.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


