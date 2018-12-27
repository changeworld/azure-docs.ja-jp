---
title: Azure Active Directory においてユーザーのパスワードをリセットする方法 | Microsoft Docs
description: Azure Active Directory を使用してユーザーのパスワードをリセットする方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 3803808ab589c3d880111421f650141f0d829f0b
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736093"
---
# <a name="how-to-reset-a-users-password-using-azure-active-directory"></a>方法: Azure Active Directory を使用してユーザーのパスワードをリセットする
パスワードを忘れた場合、ユーザーがデバイスからロックアウトされた場合、またはユーザーがパスワードを受け取っていない場合、管理者はユーザーのパスワードをリセットできます。

>[!Note]
>ご利用の Azure AD テナントがユーザーのホーム ディレクトリでない場合、管理者はユーザーのパスワードをリセットすることができません。 つまり、ユーザーが別の組織からのアカウント、Microsoft アカウント、または Google アカウントを使用して組織にサインインしている場合、管理者はそれらのパスワードをリセットすることはできません。<br><br>ユーザーが権限ソースを Windows Server Active Directory としている場合、管理者はパスワード ライトバックを有効にした場合にのみパスワードをリセットすることができます。<br><br>ユーザーが権限ソースを外部の Azure AD としている場合、管理者はパスワードをリセットすることができません。 パスワードをリセットできるのは、該当するユーザーまたは外部の Azure AD の管理者のみとなります。

## <a name="to-reset-a-password"></a>パスワードをリセットするには

1. [Azure portal](https://portal.azure.com/) に全体管理者、ユーザー管理者、またはパスワード管理者としてサインインします。 使用可能なロールについて詳しくは、[Azure Active Directory での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md#available-roles)に関するページを参照してください。

2. **[Azure Active Directory]**、**[ユーザー]** の順に選択し、リセットを必要としているユーザーを検索して選択し、**[パスワードのリセット]** を選択します。

    **[パスワードのリセット]** オプションを含む **[Alain Charon - プロファイル]** ページが表示されます。

    ![[パスワードのリセット] オプションが強調表示されているユーザーのプロファイル ページ](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. **[パスワードのリセット]** ページで、**[パスワードのリセット]** を選択します。

    ユーザー用に一時パスワードが自動生成されます。

4. そのパスワードをコピーして、ユーザーに付与します。 ユーザーは次のサインイン プロセス中にパスワードを変更するように求められます。

    >[!Note]
    >一時パスワードに期限はありません。 次回ユーザーがサインインすると、一時パスワードが生成されてから経過している時間にかかわらず、パスワードは引き続き機能します。

## <a name="next-steps"></a>次の手順
ユーザーのパスワードをリセットしたら、次の基本的な手順を行うことができます。

- [ユーザーを追加または削除する](add-users-azure-active-directory.md)

- [ユーザーにロールを割り当てる](active-directory-users-assign-role-azure-portal.md)

- [プロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)

- [基本的なグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md)

または、委任の割り当て、ポリシーの使用、ユーザー アカウントの共有など、より複雑なユーザー シナリオを実行することもできます。 他の使用可能なアクションについて詳しくは、「[Azure Active Directory のユーザー管理のドキュメント](../users-groups-roles/index.yml)」をご覧ください。
