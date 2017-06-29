---

title: "Azure Active Directory に新しいユーザーを追加する | Microsoft Docs"
description: "Azure Active Directory で新しいユーザーを追加する方法やユーザー情報を変更する方法を説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: curtand;jeffsta
ms.reviewer: jeffsta
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 5f1f28b6e0135fbd76fa5f6664f457f259dd9dd4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017



---
# <a name="add-new-users-to-azure-active-directory"></a>Azure Active Directory に新しいユーザーを追加する
> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-users-create-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-create-users.md)
>
>

この記事では、Azure Active Directory (Azure AD) に組織内の新しいユーザーを追加する方法について説明します。 

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

   ![[ユーザーとグループ] を開く](./media/active-directory-users-create-azure-portal/create-users-user-management.png)
3. **[ユーザーとグループ]** ブレードで、**[すべてのユーザー]** を選択し、**[追加]** をクリックします。

   ![[追加] をクリックする](./media/active-directory-users-create-azure-portal/create-users-add-command.png)
4. **名前**や**ユーザー名**など、ユーザーの詳細を入力します。 ユーザー名のドメイン名の部分は、既定の初期ドメイン名の "foo.onmicrosoft.com"、または検証済みの非フェデレーション ドメイン名 ("contoso.com" など) である必要があります。
5. このプロセスの完了後、ユーザーに提供できるように、生成されたユーザー パスワードをコピーするか、メモしておきます。
6. 必要に応じて、ユーザーの **[プロファイル]** ブレード、**[グループ]** ブレード、または **[Directory role (ディレクトリ ロール)]** ブレードを開き、情報を入力します。 ユーザーおよび管理者のロールの詳細については、「 [Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。
7. **[ユーザー]** ブレードで **[作成]** をクリックします。
8. ユーザーがサインインできるように、新しいユーザーに生成されたパスワードを安全に配布します。

### <a name="next-steps"></a>次のステップ
* [外部ユーザーの追加](active-directory-users-create-external-azure-portal.md)
* [新しい Azure Portal でのユーザーのパスワードのリセット](active-directory-users-reset-password-azure-portal.md)
* [ユーザーの勤務先情報の変更](active-directory-users-work-info-azure-portal.md)
* [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
* [Azure AD でのユーザーの削除](active-directory-users-delete-user-azure-portal.md)
* [Azure AD でのロールへのユーザーの割り当て](active-directory-users-assign-role-azure-portal.md)

