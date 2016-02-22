<properties
	pageTitle="Azure Active Directory でのカスタム ドメインの削除 | Microsoft Azure"
	description="Azure Active Directory でカスタム ドメインを削除する方法です。"
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
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>


# カスタム ドメイン名の削除

Azure AD のディレクトリで使用する必要がなくなったカスタム ドメイン名を削除することができます。たとえば、企業名が新しくなった場合や、異なる Azure AD のディレクトリを使用する場合など、いくつかのシナリオが考えられます。追加した後に名前を誤入力したと気付いた場合や、ドメインをフェデレーションするかどうか正しく値を設定しなかった場合などに、未確認のドメインを削除することもできます。

## 開始する前に

ドメイン名を削除する前に、次の情報に目を通しておくことをお勧めします。

- サインアップしたときにディレクトリ用に提供された元のドメイン名 contoso.onmicrosoft.com は削除できません。
- 関連付けられているサブドメインがある最上位ドメインは、サブドメインが削除されるまで削除できません。たとえば、corp.adatum.com や最上位ドメイン名を使用する別のサブドメインがある場合、adatum.com を削除することはできません。詳細については、サポート記事「[Office 365 からドメインを削除しようとすると "ドメインには関連付けられたサブドメインがあります" または "サブドメインを持つドメインは削除できません" というエラー メッセージが表示される ](https://support.microsoft.com/kb/2787792/)」を参照してください。
- ディレクトリの同期を有効にしている場合、 contoso.mail.onmicrosoft.com のように、ドメインはアカウントに自動的に追加されています。このドメイン名は削除できません。
- ドメイン名を削除するには、ドメインに関連付けられているすべてのユーザー アカウントまたは電子メール アカウントからドメイン名を削除しておく必要があります。すべてのアカウントを削除することも、ユーザー アカウントを一括編集して、ドメイン名情報と電子メール アドレスを変更することもできます。詳細については、「[Azure AD でユーザーを作成または編集する](active-directory-create-users.md)」をご覧ください。次の項目を必ず削除してください。

	-   ユーザー名または電子メール アドレス内にドメインが含まれるすべてのユーザー

	-   電子メール アドレスにドメイン名が含まれるメールが有効なすべてのグループ

	-   応答 URL の一部としてドメインが設定されているすべてのアプリケーション

- SharePoint Online サイト コレクションに使用されているドメイン名で SharePoint Online サイトをホストしている場合、ドメイン名を削除するにはサイト コレクションを削除しておく必要があります。

## ドメインを削除するには

1.  そのディレクトリのグローバル admin 権限を持つアカウントを使用して Azure クラシック ポータルにサインインします。

2.  ディレクトリを開き、**[ドメイン]** を選択します。

3.  ドメインを選択して、[削除] をクリックします。

## 次のステップ

- [カスタム ドメイン名を使用してユーザーのサインインを簡略化する](active-directory-add-domain.md)
- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
- [Azure Active Directory に対するカスタム ドメイン名の追加と確認](active-directory-add-domain-add-verify-general.md)
- [カスタム ドメインにユーザーを割り当てる](active-directory-add-domain-add-users.md)
- [カスタム ドメイン名の DNS レジストラーを変更する](active-directory-add-domain-change-registrar.md)

<!---HONumber=AcomDC_0211_2016-->