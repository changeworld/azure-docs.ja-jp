---
title: Azure Active Directory でエンタープライズ アプリのユーザー サインインを無効にする | Microsoft Docs
description: Azure Active Directory で、ユーザーがサインインできないようにエンタープライズ アプリケーションを無効にする方法
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 39e926a392cbd87eff23e25d9708792ec7c40a2c
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368710"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリのユーザー サインインを無効にする
Azure Active Directory (Azure AD) で、ユーザーがサインインできないようにエンタープライズ アプリケーションを簡単に無効にすることができます。 エンタープライズ アプリを管理するには、適切なアクセス許可が必要です。また、ディレクトリの全体管理者である必要があります。

## <a name="how-do-i-disable-user-sign-ins"></a>ユーザー サインインを無効にする方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3. **[Azure Active Directory -  ***directoryname***]** ウィンドウ (管理対象のディレクトリの Azure AD ウィンドウ) で、**[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/disable-user-sign-in-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ウィンドウで、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ウィンドウで、アプリを選択します。
6. [***appname***] ウィンドウ (選択したアプリの名前がタイトルに含まれるページ) で、**[プロパティ]** を選択します。

    ![[すべてのアプリケーション] を選択する](./media/disable-user-sign-in-portal/select-app.png)
7. **[***appname*** - プロパティ]** ウィンドウで、**[ユーザーのサインインが有効になっていますか?]** の **[いいえ]** を選択します。
8. **[保存]** をクリックします。

## <a name="next-steps"></a>次の手順
* [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)
