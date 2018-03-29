---
title: Azure Active Directory でエンタープライズ アプリのユーザー サインインを無効にする | Microsoft Docs
description: Azure Active Directory で、ユーザーがサインインできないようにエンタープライズ アプリケーションを無効にする方法
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: d4742f8d95f56d3d42ecd328e2faa140dec7d6e8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリのユーザー サインインを無効にする
Azure Active Directory (Azure AD) で、ユーザーがサインインできないようにエンタープライズ アプリケーションを簡単に無効にすることができます。 エンタープライズ アプリを管理するには、適切なアクセス許可が必要です。また、ディレクトリの全体管理者である必要があります。

## <a name="how-do-i-disable-user-sign-ins"></a>ユーザー サインインを無効にする方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3. **[Azure Active Directory -  ***directoryname***]** ウィンドウ (管理対象のディレクトリの Azure AD ウィンドウ) で、**[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ウィンドウで、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ウィンドウで、アプリを選択します。
6. [***appname***] ウィンドウ (選択したアプリの名前がタイトルに含まれるページ) で、**[プロパティ]** を選択します。

    ![[すべてのアプリケーション] を選択する](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. **[***appname*** - プロパティ]** ウィンドウで、**[ユーザーのサインインが有効になっていますか?]** の **[いいえ]** を選択します。
8. **[保存]** をクリックします。

## <a name="next-steps"></a>次の手順
* [自分のグループをすべて表示する](active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](active-directory-coreapps-remove-assignment-azure-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](active-directory-coreapps-change-app-logo-user-azure-portal.md)
