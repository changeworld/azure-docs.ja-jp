<properties
	pageTitle="Azure Active Directory B2C プレビュー: サポート | Microsoft Azure"
	description="Azure Active Directory B2C のサポート要求を提出する方法"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: Azure Active Directory B2C のサポート要求を提出する方法

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Azure ポータルで Azure Active Directory (AD) B2C のサポート要求を提出するには、次の手順に従います。

1. [この手順に従って、Azure ポータルで B2C 機能ブレードに移動します。](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)
2. B2C テナントを、Azure サブスクリプションが関連付けられている別のテナントに切り替えます。通常、移動先のテナントは、従業員のテナント、または Azure サブスクリプションにサインアップしたときに作成された既定のテナントです。Azure サブスクリプションと Azure AD ディレクトリの関係について詳しくは、[こちらの記事](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad)をご覧ください。

    ![サポート - ディレクトリの切り替え](./media/active-directory-b2c-support/support-switch-dir.png)

3. ディレクトリを切り替えた後、**[ヘルプとサポート]** をクリックします。

    ![サポート - サポート](./media/active-directory-b2c-support/support-support.png)

4. [こちらの記事](http://blogs.msdn.com/b/mast/archive/2013/10/24/windows-azure-technical-support-for-msdn-technet-or-mpn-users-and-partners.aspx)で説明されている手順に従って、Azure AD B2C に対するサポート要求を提出します。以下の説明に従って手順を完了します。

    - **[要求の種類]** は **[技術]** です。
	- **[リソース]** は **[Active Directory]** です。
	- **[問題カテゴリ]** ブレードで、**[問題の種類]** として **[B2C プレビュー]** を選択し、適切な **[カテゴリ]** を選択します。
	- **[説明]** ブレードで、問題の詳細を説明します。**[リソース]** テキスト ボックスに、B2C テナントの名前 (例: contosob2c.onmicrosoft.com) を入力します。

5. サポート要求を送信した後は、スタート画面の **[ヘルプとサポート]** をクリックした後 **[サポート要求の管理]** をクリックして、監視できます。

## 既知の問題点: B2C テナントのコンテキストでのサポート要求の提出

上で説明した手順 2 を実行せず、自分の B2C テナントのコンテキストでサポート要求を作成しようとすると、次のエラーが表示されます。

> [AZURE.IMPORTANT]
B2C テナントで新しい Azure サブスクリプションにサインアップしないでください。

![サポート - サブスクリプションなし](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=AcomDC_0211_2016-->