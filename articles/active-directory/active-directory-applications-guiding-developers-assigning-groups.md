---
title: 'Azure AD とアプリケーション: アプリケーションへのグループの割り当て | Microsoft Docs'
description: Azure アプリケーションへのグループの割り当てを実装する方法です。
services: active-directory
documentationcenter: ''
author: IHenkel
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2015
ms.author: inhenk

---
# Azure AD とアプリケーション: アプリケーションへのグループの割り当て
ユーザーとグループをアプリケーションに割り当てる前に、ユーザー割り当てを要求する必要があります。ユーザー割り当てを要求する方法については、[ユーザー割り当ての要求](active-directory-applications-guiding-developers-requiring-user-assignment.md)に関する記事をご覧ください。

この記事では、このアプリケーションに使用する Active Directory に既にグループが作成されていることを前提としています。

## アプリケーションへのグループの割り当て
1. 管理者アカウントを使用して、Azure ポータルにログインします。
2. メイン メニューの **[すべてのアイテム]** をクリックします。
3. アプリケーションに使用するディレクトリを選択します。
4. **[アプリケーション]** タブをクリックします。
5. このディレクトリに関連付けられているアプリケーションの一覧から、アプリケーションを選択します。
6. **[ユーザーとグループ]** タブをクリックします。
7. **[グループ]** ボックスの一覧を使用して、Active Directory 内のグループの一覧を絞り込みます。
8. グループを選択します。
9. **[割り当て]** をクリックします。
10. 確認を求めるメッセージが表示されたら、**[はい]** をクリックします。

## 次のステップ
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

<!---HONumber=AcomDC_0928_2016-->