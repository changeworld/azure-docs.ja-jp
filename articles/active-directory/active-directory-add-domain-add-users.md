<properties
	pageTitle="Azure Active Directory でカスタム ドメインにユーザーを割り当てる | Microsoft Azure"
	description="Azure Active Directory のカスタム ドメインにユーザー アカウントを設定する方法です。"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="curtand;jeffsta"/>

# カスタム ドメインにユーザーを割り当てる

カスタム ドメインを Azure Active Directory に追加した後は、このドメインのユーザー アカウントを追加する必要があります。その後ユーザー アカウントの認証を開始できます。

## 企業ネットワークのディレクトリから同期したユーザー

既にオンプレミスの Active Directory と Azure Active Directory の間の接続を設定してある場合は、同期することで、アカウントを設定できます。オンプレミスの Active Directory と Azure Active Directory を同期する方法の詳細については、「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。

## クラウドで追加され管理されるユーザー

既存のユーザー アカウントのドメインを変更するには次の手順を実行します。

1.  グローバル管理者またはユーザー管理者であるアカウントを使用して Azure クラシック ポータルを開きます。

2.  ディレクトリを開きます。

3.  **[ユーザー]** タブをクリックします。

4.  一覧からユーザーを選択します。

5.  ユーザーのドメインを変更し、**[保存]** を選択します。

この操作は、[Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) または [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) を使用しても実行できます。

## 新しいユーザーを作成するときにカスタム ドメインを選択する

1.  グローバル管理者またはユーザー管理者であるアカウントを使用して Azure クラシック ポータルを開きます。

2.  ディレクトリを開きます。

3.  **[ユーザー]** タブをクリックします。

4.  コマンド バーで、**[追加]** を選択します。

5.  ユーザー名を追加するときに、ドメインの一覧からカスタム ドメインを選択します。

6.  [**保存**] を選択します。

## 次のステップ

-   [カスタム ドメイン名を使用してユーザーのサインインを省力化する](active-directory-add-domain.md)

-   [カスタム ドメイン名を管理する](active-directory-add-manage-domain-names.md)

-   [Azure AD のドメイン管理の概念を理解する](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0720_2016-->