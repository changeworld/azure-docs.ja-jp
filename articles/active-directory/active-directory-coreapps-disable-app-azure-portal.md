---
title: Azure Active Directory プレビューでエンタープライズ アプリケーションのユーザー サインインを無効にする | Microsoft Docs
description: Azure Active Directory で、ユーザーがサインインできないようにエンタープライズ アプリケーションを無効にする方法
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Azure Active Directory プレビューでエンタープライズ アプリケーションのユーザー サインインを無効にする
Azure Active Directory (Azure AD) プレビューで、ユーザーがサインインできないようにエンタープライズ アプリケーションを簡単に無効にすることができます。プレビューの機能については、[こちらの記事](active-directory-preview-explainer.md)をご覧ください。 エンタープライズ アプリケーションを管理するには、適切なアクセス許可が必要です。現在のプレビューでは、ディレクトリの全体管理者である必要があります。

## ユーザー サインインを無効にする方法
1. ディレクトリの全体管理者であるアカウントで [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3. **[Azure Active Directory - *ディレクトリ名*]** ブレード (管理対象のディレクトリの Azure AD ブレード) で、**[Enterprise applications (エンタープライズ アプリケーション)]** を選択します。
   
    ![エンタープライズ アプリケーションを開く](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. **[Enterprise applications (エンタープライズ アプリケーション)]** ブレードで、**[すべてのアプリケーション]** を選択します。管理できるアプリケーションの一覧が表示されます。
5. **[Enterprise applications - All applications (エンタープライズ アプリケーション - すべてのアプリケーション)]** ブレードで、アプリケーションを選択します。
6. **[*アプリケーション名*]** ブレード (タイトルが選択したアプリケーションの名前であるブレード) で、**[プロパティ]** を選択します。
   
    ![[すべてのアプリケーション] を選択する](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. **[*アプリケーション名***** - プロパティ]** ブレードで、**[Enabled for users to sign-in? (ユーザーのサインインを有効にしますか?)]** の **[いいえ]** を選択します。
8. **[保存]** をクリックします。

## 次のステップ
* [自分のグループをすべて表示する](active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](active-directory-coreapps-remove-assignment-user-azure-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->