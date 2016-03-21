<properties
	pageTitle="Azure Active Directory でカスタム ドメインにユーザーを割り当てる | Microsoft Azure"
	description="Azure Active Directory のカスタム ドメインにユーザー アカウントを設定する方法です。"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="curtand;jeffsta"/>

# カスタム ドメインにユーザーを割り当てる

カスタム ドメインを Azure Active Directory に追加した後は、それらの認証を開始できるように、このドメインのユーザー アカウントを追加する必要があります。

## 企業ネットワークのディレクトリから同期したユーザー

既にオンプレミスの Active Directory と Azure Active Directory の間の接続を設定してある場合は、同期することで、アカウントを設定できます。オンプレミスの Active Directory と Azure Active Directory を同期する方法の詳細については、「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。

## クラウドで追加され管理されるユーザー

既存のユーザー アカウントのドメインを変更するには次の手順を実行します。

1.  グローバルの admin またはユーザーの admin であるアカウントを使用して Azure クラシック ポータルを開きます。

2.  ディレクトリを開きます。

3.  **[ユーザー]** タブをクリックします。

4.  一覧からユーザーを選択します。

5.  ユーザーのドメインを変更し、**[保存]** を選択します。

この操作は、[Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) または [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) を使用しても実行できます。

## 新しいユーザーを作成する場合は、カスタム ドメインを選択します

1.  グローバルの admin またはユーザーの admin であるアカウントを使用して Azure クラシック ポータルを開きます。

2.  ディレクトリを開きます。

3.  **[ユーザー]** タブをクリックします。

4.  コマンド バーで、**[追加]** を選択します。

5.  ユーザー名を追加する場合は、ドメインの一覧からカスタム ドメインを選択します。

6.  [**保存**] を選択します。

## 次のステップ

- [カスタム ドメイン名を使用してユーザーのサインインを簡略化する](active-directory-add-domain.md)
- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
- [Azure Active Directory に対するカスタム ドメイン名の追加と確認](active-directory-add-domain-add-verify-general.md)
- [カスタム ドメイン名の DNS レジストラーを変更する](active-directory-add-domain-change-registrar.md)
- [Azure Active Directory からカスタム ドメインを削除する](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->