---
title: Azure Active Directory でユーザーのエクスペリエンスからアプリケーションを非表示にする | Microsoft Docs
description: Azure Active Directory アクセス パネルまたは Office 365 ランチャーでユーザーのエクスペリエンスからアプリケーションを非表示にする方法
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
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 55f80396df4cbfe7d0a16a6a5066b68aadc0bdd3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368920"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Azure Active Directory でユーザーのエクスペリエンスからアプリケーションを非表示にする

アプリケーションをユーザーのアクセス パネルまたは Office 365 ランチャーに表示したくない場合は、このアプリケーション タイルを非表示にするオプションがあります。  ユーザーのアプリ ランチャーからアプリケーションを非表示にするには、次の 2 つのオプションが使用可能です。

- ユーザーのアクセス パネルと Office 365 アプリ ランチャーからサード パーティ製アプリケーションを非表示にする。
- ユーザーのアクセス パネルからすべての Office 365 アプリケーションを非表示にする。

アプリケーションを非表示にしても、ユーザーはそのアプリケーションにアクセス許可を持っていますが、アプリケーション ランチャーには表示されなくなります。 エンタープライズ アプリを管理するには、適切なアクセス許可が必要です。また、ディレクトリの全体管理者である必要があります。


## <a name="hiding-an-application-from-users-end-user-experiences"></a>ユーザーのエンド ユーザー エクスペリエンスからアプリケーションを非表示にする
アクセス パネルからアプリケーションを非表示にするには、状況に応じて次の手順を使用できます。

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>ユーザーのアクセス パネルと O365 アプリケーション ランチャーでサード パーティ アプリを非表示にする方法
次の手順を使って、ユーザーのアクセス パネルと Office 365 アプリケーション ランチャーからアプリケーションを非表示にします。

1.  ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2.  **[すべてのサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3.  **[Azure Active Directory - *<ディレクトリ名>*]** 画面 (つまり管理対象のディレクトリの Azure AD 画面) で、**[エンタープライズ アプリケーション]** を選択します。
![エンタープライズ アプリケーション](./media/hide-application-from-user-portal/app1.png)
4.  **[エンタープライズ アプリケーション]** 画面で、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5.  **[エンタープライズ アプリケーション - すべてのアプリケーション]** 画面で、アプリケーションを選択します。</br>
![エンタープライズ アプリケーション](./media/hide-application-from-user-portal/app2.png)
6.  ***[<アプリケーション名>]*** 画面 (タイトルが選択したアプリケーション名の画面) で、[プロパティ] を選択します。
7.  **[*<アプリケーション名>* - プロパティ]** 画面で、**[ユーザーに表示しますか?]** に **[はい]** を選択します。
![エンタープライズ アプリケーション](./media/hide-application-from-user-portal/app3.png)
8.  **[保存]** をクリックします。

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>ユーザーのアクセス パネルから Office 365 アプリケーションを非表示にする方法

アクセス パネルからすべての Office 365 アプリケーションを非表示にするには、次の手順を使用します。 これらのアプリは、Office 365 ポータルには引き続き表示されます。

1.  ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2.  **[すべてのサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3.  **[Azure Active Directory - *<ディレクトリ名>*]** 画面 (つまり管理対象のディレクトリの Azure AD 画面) で、**[ユーザー設定]** を選択します。
4.  **[ユーザー設定]** 画面の **[エンタープライズ アプリケーション]** で、**[Users can only see Office 365 apps in the Office 365 portal]\(ユーザーは Office 365 ポータルに Office 365 アプリのみを表示できる\)** の **[はい]** を選択します。

![エンタープライズ アプリケーション](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>次の手順
* [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)

