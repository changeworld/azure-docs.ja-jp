---
title: "Azure Active Directory のパスワードのリセット | Microsoft Docs"
description: "管理者が Azure Active Directory でユーザーのパスワードのリセットを行います。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: bea082081e3f3f52ba78188903a9536fe9de9392
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Azure Active Directory でのユーザー パスワードのリセット

管理者は、ユーザーがパスワードを忘れた場合やロックアウトされた場合などに、ユーザーのパスワードをリセットすることが必要な場合があります。 ここでは、ユーザーのパスワードをリセットする手順を説明します。

## <a name="how-to-reset-the-password-for-a-user"></a>ユーザーのパスワードをリセットする方法

1. ユーザーのパスワードをリセットするためのディレクトリのアクセス許可を持つアカウントで [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
2. **[Azure Active Directory]** > **[ユーザーとグループ]** > **[すべてのユーザー]** を選択します。
3. パスワードをリセットするユーザーを選択します。
2. 選択したユーザーで、**[パスワードのリセット]** を選択します。

    ![Azure AD のユーザー プロファイルからユーザーのパスワードをリセットする](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. **[パスワードのリセット]** で、**[パスワードのリセット]** を選びます。
7. 一時的なパスワードが表示されるので、このパスワードをユーザーに提供します。 ユーザーは、次回のログイン時にパスワードの変更を求められます。 

   > [!NOTE]
   > この一時的なパスワードには有効期限がないため、ユーザーがログインし、パスワードの変更を強制されるまで有効です。 

## <a name="next-steps"></a>次のステップ
* [ユーザーの追加](active-directory-users-create-azure-portal.md)
* [ユーザーへの管理者ロールの割り当て](active-directory-users-assign-role-azure-portal.md)
* [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
* [Azure AD でのユーザーの削除](active-directory-users-delete-user-azure-portal.md)
