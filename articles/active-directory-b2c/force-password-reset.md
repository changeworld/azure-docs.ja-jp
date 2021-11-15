---
title: Azure AD B2C でパスワードの強制リセット フローを構成する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でパスワードの強制リセット フローを設定する方法を説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4c5802d9cd1fc9a7a41d38bc70e9d384882a1ea1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424327"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でパスワードの強制リセット フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

## <a name="overview"></a>概要

管理者は、ユーザーが自分のパスワードを忘れた場合に、ユーザーの[パスワードをリセット](manage-users-portal.md#reset-a-users-password)できます。 または、パスワードを強制的にリセットさせたいと考えています。 この記事では、こうしたシナリオでパスワードを強制的にリセットする方法について説明します。

管理者が Azure portal を使用してユーザーのパスワードをリセットすると、[forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性の値が `true` に設定されます。 [サインインとサインアップの過程](add-sign-up-and-sign-in-policy.md)で、この属性の値がチェックされます。 ユーザーがサインインを完了した後、属性が `true` に設定されている場合、ユーザーはパスワードをリセットする必要があります。 その後、この属性の値は `false` に戻ります。

![パスワードの強制リセット フロー](./media/force-password-reset/force-password-reset-flow.png)

パスワードのリセット フローは、パスワードが含まれる[電子メール アドレス](sign-in-options.md#email-sign-in)や[ユーザー名](sign-in-options.md#username-sign-in)を使ってサインインを行う Azure AD B2C のローカル アカウントに適用されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

この機能は現在、ユーザー フローでのみ使用できます。 セットアップ手順については、上の **[ユーザー フロー]** を選択してください。 カスタム ポリシーの場合は、初回ログオン時にパスワードを強制的にリセットの [GitHub サンプル](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset-first-logon)と以下の前提条件を使います。

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="configure-your-user-flow"></a>ユーザー フローを構成する

サインアップまたはサインイン ユーザー フローで **パスワードの強制リセット** 設定を有効にするには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. カスタマイズする ( **[推奨]** タイプの) サインアップおよびサインイン、またはサインイン ユーザー フロー を選択します。
1. 左側のメニューの **[設定]** で、 **[プロパティ]** を選択します。
1. **[Password configuration]\(パスワードの構成\)** で、 **[パスワードの強制リセット]** を選択します。
1. **[保存]** を選択します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. [Azure portal](https://portal.azure.com) にユーザー管理者またはパスワード管理者としてサインインします。 使用可能なロールについて詳しくは、[Azure Active Directory での管理者ロールの割り当て](../active-directory/roles/permissions-reference.md#all-roles)に関するページを参照してください。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー]** を選択します。 パスワードのリセットをテストするために使用するユーザーを検索して選択した後、 **[パスワードのリセット]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. テストする ( **[推奨]** タイプの) サインアップまたはサインイン ユーザー フローを選択します。
1. **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** を選択します。
1. パスワードをリセットしたユーザー アカウントでサインインします。
1. ここで、ユーザーのパスワードを変更する必要があります。 パスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、表示されます。

## <a name="force-password-reset-on-next-login"></a>次回ログイン時にパスワードを強制的にリセットする

次回ログイン時にパスワードを強制的にリセットするには、MS Graph の[ユーザー更新](/graph/api/user-update)操作を使用して、アカウント パスワード プロファイルを更新します。 次の例では、パスワード プロファイルの [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性を `true` に更新します。これにより、次回ログイン時にユーザーによるパスワードのリセットが強制されます。

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
"passwordProfile": {
  "forceChangePasswordNextSignIn": true
}
```

アカウント パスワード プロファイルが設定された時点で、この記事の説明に従って、パスワードを強制的にリセットするフローを構成する必要もあります。

## <a name="force-a-password-reset-after-90-days"></a>90 日後にパスワードを強制的にリセットする

管理者は、[MS Graph](microsoft-graph-operations.md) を使用して、ユーザーのパスワードの有効期限を 90 日に設定できます。 90 日後に、[forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 属性の値が自動的に `true` に設定されます。 90 日後にパスワードを強制的にリセットするには、ユーザーのプロファイルの[パスワード ポリシー](user-profile-attributes.md#password-policy-attribute)属性から `DisablePasswordExpiration` 値を削除します。

次の例は、パスワード ポリシーを `None` に更新しています。これにより、90 日後にパスワードのリセットが強制されます。

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "None"
}
```

強力な[パスワードの複雑さ](password-complexity.md)を無効にした場合は、パスワード ポリシーを [DisableStrongPassword](user-profile-attributes.md#password-policy-attribute) に更新します。

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "DisableStrongPassword"
}
```

パスワードの有効期限ポリシーが設定されている場合は、この記事の説明に従って、パスワードの強制リセット フローを構成する必要もあります。

### <a name="password-expiry-duration"></a>パスワードの有効期間

パスワードの有効期間の既定値は **90** 日です。 値を構成するには、Windows PowerShell の Azure Active Directory モジュールから [Set-MsolPasswordPolicy](/powershell/module/msonline/set-msolpasswordpolicy) コマンドレットを使用します。 このコマンドは、設定した日数の経過後にすべてのユーザーのパスワードの有効期限が切れるように、テナントを更新します。

::: zone-end

## <a name="next-steps"></a>次のステップ

[セルフサービス パスワード リセット](add-password-reset-policy.md)を設定します。
