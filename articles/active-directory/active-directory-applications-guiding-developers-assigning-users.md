<properties
	pageTitle="Azure AD とアプリケーション: アプリケーションへのユーザーの割り当て | Microsoft Azure"
	description="Azure アプリケーションへのユーザーの割り当てを実装する方法です。"
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/09/2015"
	ms.author="inhenk"/>

# Azure AD とアプリケーション: アプリケーションへのユーザーの割り当て
ユーザーとグループをアプリケーションに割り当てる前に、ユーザー割り当てを要求する必要があります。ユーザー割り当てを要求する方法については、「[ユーザー割り当ての要求](active-directory-applications-guiding-developers-requiring-user-assignment.md)」を参照してください。

## アプリケーションへのユーザーの割り当て
1. 管理者アカウントを使用して、Azure ポータルにログインします。
2. メイン メニューの **[すべてのアイテム]** をクリックします。
3. アプリケーションに使用するディレクトリを選択します。
4. **[アプリケーション]** タブをクリックします。
5. このディレクトリに関連付けられているアプリケーションの一覧から、アプリケーションを選択します。
6. **[ユーザーとグループ]** タブをクリックします。
8. アプリケーションに割り当てるユーザーを選択します。
9. **[割り当て]** をクリックします。
10. 確認を求めるメッセージが表示されたら、**[はい]** をクリックします。

## 次のステップ
[AZURE.INCLUDE [guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

<!---HONumber=Oct15_HO3-->