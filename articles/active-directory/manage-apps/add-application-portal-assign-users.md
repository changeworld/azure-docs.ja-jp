---
title: 'クイックスタート: ユーザー アカウントを作成して割り当てる'
titleSuffix: Azure AD
description: Azure Active Directory テナントにユーザー アカウントを作成して、アプリケーションに割り当てます。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 38be83db1c585a3660882ebdcca70a239a620f03
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549149"
---
# <a name="quickstart-create-and-assign-a-user-account"></a>クイックスタート: ユーザー アカウントを作成して割り当てる

このクイックスタートでは、Azure Active Directory 管理センターを使用して、Azure Active Directory (Azure AD) テナントにユーザー アカウントを作成します。 作成したアカウントは、テナントに追加したエンタープライズ アプリケーションに割り当てることができます。

このクイックスタートの手順をテストするには、非運用環境を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件

ユーザー アカウントを作成してエンタープライズ アプリケーションに割り当てるには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- [エンタープライズ アプリケーションを追加するクイックスタート](add-application-portal.md)の手順を完了していること。

## <a name="create-a-user-account"></a>ユーザー アカウントの作成

Azure AD テナントにユーザー アカウントを作成するには:

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動し、前提条件の一覧で示されているロールのいずれかを使用してサインインします。
1. 左側のメニューで、 **[ユーザー]** を選択します。
1. ペインの上部にある **[新しいユーザー]** を選択します。

    :::image type="content" source="media/add-application-portal-assign-users/new-user.png" alt-text="Azure AD テナントに新しいユーザー アカウントを追加する。":::
    
1. **[ユーザー名]** フィールドに、ユーザー アカウントのユーザー名を入力します。 たとえば、「 `contosouser1@contoso.com` 」のように入力します。 必ず `contoso.com` を自分のテナントのドメインの名前に変更してください。
1. **[名前]** フィールドに、アカウントのユーザーの名前を入力します。 たとえば、「 `contosouser1` 」のように入力します。
1. **[パスワードの自動生成]** はオンのままにして、 **[パスワードの表示]** を選択します。 [パスワード] ボックスに表示された値を書き留めます。
1. **[作成]** を選択します。

## <a name="assign-a-user-account-to-an-enterprise-application"></a>エンタープライズ アプリケーションにユーザー アカウントを割り当てる

エンタープライズ アプリケーションにユーザー アカウントを割り当てるには:

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)で **[エンタープライズ アプリケーション]** を選択し、ユーザー アカウントを割り当てるアプリケーションを見つけて選択します。 たとえば、前のクイックスタートで作成したアプリケーションの名前は **Azure AD SAML Toolkit 1** でした。
1. 左側のペインで、 **[ユーザーとグループ]** を選択し、 **[ユーザーまたはグループの追加]** を選択します。

    :::image type="content" source="media/add-application-portal-assign-users/assign-user.png" alt-text="Azure AD テナントのアプリケーションにユーザー アカウントを割り当てる。":::

1. **[割り当ての追加]** ペインで **[ユーザーとグループ]** の **[選択されていません]** を選択します。
1. アプリケーションに割り当てるユーザーを見つけて選択します。 たとえば、「 `contosouser1@contoso.com` 」のように入力します。
1. **[選択]** を選択します。
1. **[割り当ての追加]** ペインで、ペインの下部にある **[割り当て]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のクイックスタートを行う予定の場合は、作成したアプリケーションをそのままにします。 そうでない場合は、削除してテナントをクリーンアップしてもかまいません。 詳細については、[アプリケーションの削除](delete-application-portal.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

エンタープライズ アプリケーションのシングル サインオンをセットアップする方法について学習します。
> [!div class="nextstepaction"]
> [シングル サインオンの有効化](add-application-portal-setup-sso.md)
