<properties
	pageTitle="Office 365 テナントを Azure サブスクリプションで使用する | Microsoft Azure"
	description="Office 365 ディレクトリ (テナント) を Azure サブスクリプションに追加して両者の関連付けを行う方法について説明します。"
	services=""
	documentationCenter=""
	authors="jiangchen79"
	manager="mbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="cjiang"/>

# Office 365 テナントの Azure サブスクリプションへの関連付け
シナリオ: 過去に Azure サブスクリプションと Office 365 サブスクリプションを別々に購入したとします。このとき、Azure サブスクリプションから Office 365 テナントにアクセスできるようにするにはどうすればよいのでしょうか。難しいことは何もありません。この記事ではその方法について説明します。

> [AZURE.NOTE] この記事は、Enterprise Agreement (EA) のお客様には適用されません。

## クイック ガイド
Office 365 テナントを Azure サブスクリプションに関連付けるには、まず Azure アカウントを使用して Office 365 テナントを追加した後、その Office 365 テナントに Azure サブスクリプションを関連付けます。詳細な手順を参照してください。

## 詳細な手順
このシナリオの Kelley Wall は、アカウント kelly.wall@outlook.com の Azure サブスクリプションがあるユーザーです。Kelley は、kelley.wall@contoso.onmicrosoft.com というアカウントで Office 365 サブスクリプションも所有していて、Office 365 テナントに Azure サブスクリプションでアクセスしたいと考えています。

![msa-aad-status](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![office-365-user](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

**前提条件:**

- Azure サブスクリプションのサービス管理者の資格情報が必要となります。共同管理者では一部の手順を実行できません。
- Office 365 テナントの全体管理者の資格情報が必要となります。
- サービス管理者の電子メール アドレスが Office 365 テナントに含まれていないことが必要です。
- サービス管理者の電子メール アドレスが、Office 365 テナントのどの全体管理者の電子メール アドレスとも異なっていることが必要です。
- 現在、Microsoft アカウントと組織アカウントの両方に該当する電子メール アドレスを使用している場合は、別の Microsoft アカウントを使用するように、Azure サブスクリプションのサービス管理者を一時的に変更することをお勧めします。以降の作業手順で確認されている既知の制限が緩和されます。新しい Microsoft アカウントは、[Microsoft アカウントのサインアップ ページ](https://signup.live.com/)で作成できます。

	サービス管理者を変更するには、次の手順を実行します。

	1. [アカウント管理ポータル](https://account.windowsazure.com/subscriptions)にログオンします。
	2. 変更するサブスクリプションを選択します。
	3. 右側にある **[サブスクリプション詳細の編集]** をクリックします。

		![azure-edit-subscription-details](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

	4. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。

		![change-subscription-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

Office 365 テナントを Azure サブスクリプションに関連付けるには、次の手順を実行します。

1. 	サービス管理者の資格情報で[アカウント管理ポータル](https://account.windowsazure.com/subscriptions)にサインインします。
2.	左側のウィンドウの **[Active Directory]** をクリックします。

	![classic-portal-active-directory-entry](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

	> [AZURE.NOTE] Office 365 テナントが表示されていないことを確認してください。表示されている場合は、次の手順を省略します。

	![aad-tenant-default](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Office 365 テナントを Azure サブスクリプションに追加します。
	1. **[新規]**、**[ディレクトリ]**、**[カスタム作成]** の順にクリックします。

		![aad-custom-create](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

	2. **[ディレクトリの追加]** ページで **[ディレクトリ]** から **[既存のディレクトリの使用]** を選択し、**[サインアウトする準備ができました]** をオンにして **[完了]** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png) をクリックします。

		![add-directory-use-existing](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

	3. サインアウト後、Office 365 テナントの全体管理者の資格情報でサインインします。

		![sign-in-global-admin-office-365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

	4. **[続行]** をクリックします。

		![use-contoso-directory-azure-verify](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

	5. **[今すぐサインアウト]** をクリックします。

		![use-contoso-directory-azure-confirm-and-sign-out](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

	6. サービス管理者の資格情報で[アカウント管理ポータル](https://account.windowsazure.com/subscriptions)にサインインします。

		![azure-sign-in-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

	7. ダッシュボードに Office 365 テナントが表示されます。

		![office-365-tenant-appear-in-azure](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Azure サブスクリプションに関連付けられているディレクトリを変更します。

	1. **[設定]** をクリックします。

		![azure-classic-portal-settings-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

	2. Azure サブスクリプションをクリックし、**[ディレクトリの編集]** をクリックします。

		![azure-subscription-edit-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

	3. **[次へ]** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png) をクリックします。

		![azure-change-associated-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

		> [AZURE.WARNING] すべての共同管理者が削除されることを伝える警告が表示されます。

		![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

		>[AZURE.WARNING] また、[ロールベースのアクセス制御 (RBAC)](./active-directory/role-based-access-control-configure.md) の下でアクセス権が割り当て済みになっているユーザーもすべて、既存のリソース グループから削除されます。ただし、表示される警告で言及されるのは共同管理者の削除だけです。

		![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

	4. **[完了]** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png) をクリックします。

5. これで Office 365 の組織アカウントを共同管理者として AAD テナントに追加できます。

	1. **[管理者]** タブをクリックし、**[追加]** をクリックします。

		![azure-classic-portal-settings-administrators](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

	2. Office 365 テナントの組織アカウントを入力し、対象の Azure サブスクリプションをオンにして、**[完了]** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png) をクリックします。

		![azure-add-co-administrator](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

	3. **[管理者]** タブに戻ると、組織アカウントが共同管理者として表示されていることがわかります。

		![azure-co-administrator-added](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. 次に、共同管理者でのアクセスをテストします。

	1. アカウント管理ポータルをサインアウトします。
	2. [アカウント管理ポータル](https://account.windowsazure.com/subscriptions)または [Azure ポータル](https://portal.azure.com/)を開きます。
	3. Azure サインイン ページに **[組織のアカウントを使ってサインインしてください]** というリンクが表示されている場合は、そのリンクをクリックします。それ以外の場合、この手順はスキップしてください。

		![Azure-sign-in-with-orgid-link](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

	4. 共同管理者の資格情報を入力し、**[サインイン]** をクリックします。

		![Azure-sign-in-with-co-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## 次のステップ
関連するシナリオとして、既に Office 365 サブスクリプションがあり、Azure サブスクリプションが使用できる状態にあるものの、Azure サブスクリプションには既存の Office 365 ユーザー アカウントを使用したい、という場合があります。または、既に Azure サブスクライバーであり、既存の Azure Active Directory のユーザー用に Office 365 サブスクリプションを取得したい場合。具体的な方法については、「[既存の Office 365 アカウントを Azure サブスクリプションに使用する (またはその逆)](billing-use-existing-office-365-account-azure-subscription.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->