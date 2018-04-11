---
title: Azure Portal で B2B コラボレーション ユーザーを追加する - Azure Active Directory | Microsoft Docs
description: 管理者が Azure Active Directory (Azure AD) B2B コラボレーションを使用してパートナー組織からディレクトリにゲスト ユーザーを追加する方法を説明します。
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/02/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 34bd5b51089045c4cd20f29d179bb230e5e3fac2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する

グローバル管理者、またはいずれかの制限付き管理者ディレクトリ ロールを割り当てられたユーザーとして、Azure Portal を使用して B2B コラボレーション ユーザーを招待できます。 ゲスト ユーザーをディレクトリ、グループ、またはアプリケーションに招待できます。 これらのメソッドを使用してユーザーを招待すると、招待されたユーザーのアカウントが、"*ゲスト*" というユーザー タイプで Azure Active Directory (Azure AD) に追加されます。 次に、ゲスト ユーザーは、招待に応じてリソースにアクセスします。

## <a name="add-guest-users-to-the-directory"></a>ゲスト ユーザーをディレクトリに追加する

B2B コラボレーション ユーザーをディレクトリに追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3. **[管理]** で、**[ユーザーとグループ]** > **[すべてのユーザー]** を選択します。
4. **[新しいゲスト ユーザー]** を選択します。

   ![UI 内の新しいゲスト ユーザーの場所を示す](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. **[ゲストを招待する]** で、外部ユーザーの電子メール アドレスを入力します。 必要に応じて、ようこそメッセージを含めます。 例: 

   ![UI 内の新しいゲスト ユーザーの場所を示す](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. **[招待]** を選択して、招待をゲスト ユーザーに自動的に送信します。 **[通知]** 領域で、**[ユーザーが正常に招待されました]** メッセージを探します。 
 
招待を送信すると、ユーザー アカウントがディレクトリにゲストとして自動的に追加されます。


![ユーザー タイプ "ゲスト" の B2B ユーザーを示す](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>ゲスト ユーザーをグループに追加する
Azure AD 管理者として、B2B コラボレーション ユーザーをグループに手動で追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3. **[管理]** で、**[ユーザーとグループ]** > **[すべてのグループ]** を選択します。
4. グループを選択 (または **[新しいグループ]** をクリックしてグループを新規作成) します。 グループに B2B ゲスト ユーザーが含まれていることをグループの説明に含めることをお勧めします。
5. **[メンバー]** > **[メンバーの追加]** を選択します。 
6. 次のいずれかを実行します。
   - ゲスト ユーザーがディレクトリに既に存在する場合は、B2B ユーザーを探します。 ユーザーを選択し、**[選択]** をクリックしてユーザーをグループに追加します。
   - ゲスト ユーザーがディレクトリに存在しない場合は、**[招待]** を選択します。
   ![[招待] ボタンを追加してゲスト メンバーを追加する](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      **[ゲストを招待する]** で、電子メール アドレスと必要に応じて個人のメッセージを入力して、**[招待]** を選択します。 **[選択]** をクリックして、ユーザーをグループに追加します。

      招待が、招待されたユーザーに自動的に送信されます。 **[通知]** 領域で、**ユーザーが正常に招待された**ことを示すメッセージを探します。 

Azure AD B2B コラボレーションを使って動的グループを使用することもできます。 詳しくは、「[動的グループと Azure Active Directory B2B コラボレーション](active-directory-b2b-dynamic-groups.md)」を参照してください。

## <a name="add-guest-users-to-an-application"></a>ゲスト ユーザーをアプリケーションに追加する

Azure AD 管理者として B2B コラボレーション ユーザーをアプリケーションに追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3. **[管理]** で、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** を選択します。
4. ゲスト ユーザーを追加するアプリケーションを選択します。
5. **[管理]** で **[ユーザーとグループ]** を選択します。
6. **[ユーザーの追加]** を選択します。
7. **[割り当ての追加]** で **[ユーザーとグループ]** を選択します。
8. 次のいずれかを実行します。
   - ゲスト ユーザーがディレクトリに既に存在する場合は、B2B ユーザーを探します。 ユーザーを選択し、**[選択]** をクリックしてユーザーをアプリケーションに追加します。
   - ゲスト ユーザーがディレクトリに存在しない場合は、**[招待]** を選択します。
   ![[招待] ボタンを追加してゲスト メンバーを追加する](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      **[ゲストを招待する]** で、電子メール アドレスと必要に応じて個人のメッセージを入力して、**[招待]** を選択します。 **[選択]** をクリックして、ユーザーをアプリケーションに追加します。

      招待が、招待されたユーザーに自動的に送信されます。 **[通知]** 領域で、**ユーザーが正常に招待された**ことを示すメッセージを探します。

9. **[割り当ての追加]** で、**[Select Role]\(ロールの選択\)** をクリックし、選択したユーザーに適用するロールを選択して (該当する場合)、**[OK]** を選択します。
10. **[割り当て]** をクリックします。
 
## <a name="resend-invitations-to-guest-users"></a>招待をゲスト ユーザーに再送信する

ゲスト ユーザーがまだ招待に応じていない場合、招待を再送信できます。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3. **[管理]** で **[ユーザーとグループ]** を選択します。
4. **[すべてのユーザー]** を選択します。
5. ユーザー アカウントを選択します。
6. **[管理]** で、**[プロファイル]** を選択します。
7. ユーザーがまだ招待に応じていない場合は、**[招待の再送信]** オプションを使用できます。 このボタンを選択して再送信します。

   ![ユーザー プロファイルの [招待の再送信] オプション](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> もともとユーザーを特定のアプリに招待する招待を再送信した場合、新しい招待のリンクによりユーザーは最上位レベルのアクセス パネルに移動されます。

## <a name="next-steps"></a>次の手順

- Azure 以外の AD 管理者が B2B ゲスト ユーザーを追加する方法については、「[インフォメーション ワーカーが B2B コラボレーション ユーザーを Azure Active Directory に追加する方法](active-directory-b2b-iw-add-users.md)」をご覧ください。
- 招待メールについて詳しくは、「[B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)」を参照してください。
- 招待の引き換えプロセスについて詳しくは、「[B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)」を参照してください。


