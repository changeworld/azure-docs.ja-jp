<properties
	pageTitle="Office 365 と Azure サブスクリプション全体で 1 つの Azure AD テナントを共有する | Microsoft Azure"
	description="Office 365 Azure AD テナントとそのユーザーを Azure サブスクリプションと共有する方法、またはその逆の方法について説明します"
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
	ms.date="06/20/2016"
	ms.author="cjiang"/>

# 既存の Office 365 アカウントを Azure サブスクリプションに使用する (またはその逆)
シナリオ: 既に Office 365 サブスクリプションがあり、Azure サブスクリプションを開始するに当たって既存の Office 365 ユーザー アカウントを Azure サブスクリプションに使用したい場合。または、既に Azure サブスクライバーであり、既存の Azure Active Directory のユーザー用に Office 365 サブスクリプションを取得したい場合。いずれの方法も簡単です。この記事ではその方法について説明します。

> [AZURE.NOTE] この記事は、Enterprise Agreement (EA) のお客様には適用されません。

## クイック ガイド

- 既に Office 365 サブスクリプションがあり、Azure にサインアップする場合、**[組織のアカウントを使用してサインインする]** オプションを使用すると、引き続き現在の Office 365 アカウントで Azure にサインアップできます。[詳細な手順](#s1)を参照してください。
- 既に Azure サブスクリプションがあり、Office 365 サブスクリプションを取得する場合は、Azure アカウントで Office 365 にサインインし、サインアップ手順を続行します。手順を完了すると、Office 365 サブスクリプションは、Azure サブスクリプションが属している同じ Azure Active Directory に追加されます。[詳細な手順](#s2)を参照してください。

>[AZURE.NOTE] Office 365 サブスクリプションを取得するには、サインインに使用するアカウントが、Azure AD テナントでグローバル管理者または課金管理者ディレクトリ ロールのメンバーである必要があります。[Azure Active Directory のロールの説明を参照してください](#how-to-know-your-role-in-your-azure-active-directory)。

サブスクリプションをアカウントに追加するときの処理内容については、後述する[背景情報](#background-information)を参照してください。

## 詳細な手順
<a id="s1"></a>
### シナリオ 1: Azure を購入する Office 365 ユーザー プラン
このシナリオでは、Office 365 サブスクリプションがある Kelley Wall というユーザーが、Azure にサブスクライブしようとしています。その他に、Jane と Tricia という 2 人のアクティブ ユーザーがいます。Kelley のアカウントは admin@contoso.onmicrosoft.com です。

![office365-users-admin-center.png](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Azure にサインアップするには、次の手順を実行します。

1. [Azure.com](https://azure.microsoft.com/) で Azure にサインアップします。**[無料で試す]** をクリックします。次のページで、**[今すぐ開始]** をクリックします。

	![azure-signup-try-free](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. **[組織のアカウントを使用してサインインする]** をクリックします。

	![sign-in-to-azure](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Office 365 アカウントでサインインします。この例では Kelly の Office 365 アカウントです。

	![sign-in-with-org-account](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. 情報を入力し、サインアップ プロセスを完了します。

	![azure-sign-up-fill-information](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

5. **[サービスの管理を開始する]** をクリックすると完了です。

	![azure-start-managing-my-service](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

以上で設定は完了です。Azure ポータルに、同じディレクトリで表示されるユーザーと同じユーザーが表示されます。確認する手順は次のとおりです。

1. 上のスクリーンショットの **[サービスの管理を開始する]** をクリックします。
2. **[参照]** をクリックし、**[Active Directory]** をクリックします。

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. **[ユーザー]** をクリックします。

	![azure-portal-ad-users-tab](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. 想定どおりに Kelley を含む全ユーザーの一覧が表示されます。

	![azure-portal-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### シナリオ 2: Azure ユーザーが Office 365 を購入する

このシナリオの Kelley Wall は、アカウント admin@contoso.onmicrosoft.com の Azure サブスクリプションがあるユーザーです。Kelley は Office 365 にサブスクライブし、Azure と同じ既存のディレクトリを使用したいと考えています。

>[AZURE.NOTE] Office 365 サブスクリプションを取得するには、サインインに使用するアカウントが、Azure AD テナントでグローバル管理者または課金管理者ディレクトリ ロールのメンバーである必要があります。[Azure Active Directory のロールの説明を参照してください](#how-to-know-your-role-in-your-azure-active-directory)。

![azure-portal-settings-subscription](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![azure-portal-ads-users](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Office 365 にサブスクライブするには、次の手順を実行します。

1. [Office 365 製品ページ](https://products.office.com/business)にアクセスし、適切なプランを選択します。
2. プランを選択すると、次のページが表示されます。フォームには入力しないでください。ページの右上にある **[サインイン]** をクリックします。

	![office-365-trial-page](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. アカウントの資格情報でサインインします。この例では Kelly のアカウントです。

	![office-365-sign-in](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. **[今すぐ試す]** をクリックします。

	![office-365-confirm-your-order](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. 注文の受領書ページで、**[続行]** をクリックします。

	![office-365-order-receipt](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

以上で設定は完了です。Office 365 管理センターに、アクティブ ユーザーに表示される Contoso ディレクトリと同じユーザーが表示されます。確認する手順は次のとおりです。

1. Office 365 管理センターを開きます。
2. **[ユーザー]** を展開し、**[アクティブ ユーザー]** をクリックします。

	![office-365-admin-center-users](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### Azure Active Directory のロールを確認する方法

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[参照]** をクリックし、**[Active Directory]** をクリックします。

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. **[ユーザー]** をクリックします。

	![azure-portal-default-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. ユーザーをクリックします。この例では、Kelley Wall です。
5. **[組織のロール]** フィールドに注目してください。

	![azure-portal-user-identity](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## 背景情報
Office 365 と Azure は、ユーザーとサブスクリプションの管理に Azure Active Directory (AAD) サービスを使用しています。Active Directory は、ユーザーとサブスクリプションをグループ化できるコンテナーとして考えてみてください。Microsoft Azure と Office 365 サブスクリプションに同じユーザー アカウントを使用するには、サブスクリプションを同じディレクトリに作成する必要があります。

- サブスクリプションは、他の場所ではなく、ディレクトリに作成されます。
- ユーザーは、他の場所ではなく、ディレクトリに属します。
- サブスクリプションは、サブスクリプションを作成したユーザーのディレクトリにあります。そのため、Office 365 サブスクリプションの作成に同じアカウントを使用すると、Office 365 サブスクリプションは、Azure サブスクリプションの同じアカウントに関連付けられます。

![background-information](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

詳細については、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](./active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

**注:**

- Azure サブスクリプションの所有者は、ディレクトリ内の各ユーザーです。
- Office 365 サブスクリプションの所有者は、ディレクトリ自体です。ディレクトリ内のユーザーは、必要なアクセス許可を持っている場合、そのサブスクリプションで操作できます。

##次のステップ
関連したシナリオが紹介されています。過去に別々に購入した Azure サブスクリプションと Office 365 サブスクリプションがあるとき、Azure サブスクリプションから Office 365 テナントにアクセスできるようにするにはどうすればよいのでしょうか。具体的な方法については、「[Office 365 テナントの Azure サブスクリプションへの関連付け](billing-add-office-365-tenant-to-azure-subscription.md)」を参照してください。

<!---HONumber=AcomDC_0803_2016-->