---
title: パスワードのリセット フローを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でパスワードのリセット フローを設定する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618628"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でパスワードのリセット フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>パスワードのリセット フロー

パスワード リセット ポリシーを使用すると、ユーザーは、忘れてしまった自分のパスワードをリセットできます。 パスワードのリセット フローでは、次の手順を実行します。 
1. サインアップおよびサインイン ページで、ユーザーは [パスワードを忘れた場合] リンクをクリックします 。 Azure AD B2C によって、アプリに AADB2C90118 エラー コードが返されます。 アプリでは、このエラー コードをパスワード リセット ポリシーを呼び出すことによって処理します。 
1. ユーザーは、時間が限定されたワン タイム パスコードを使用して電子メールを確認します。
1. 新しいパスワードを入力します。

![パスワードのリセット フロー](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>[前提条件]

まだそうしていない場合は、[Azure Active Directory B2C に Web アプリケーションを登録](tutorial-register-applications.md)します。

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>パスワードのリセット ユーザー フローを作成する

アプリケーションのユーザーが自分のパスワードをリセットできるようにするには、パスワード リセット ユーザー フローを使用します。

1. Azure AD B2C テナントの概要メニューで、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
1. **[ユーザー フローを作成する]** ページで、 **[パスワード リセット]** ユーザー フローを選択します。 
1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。
1. ユーザー フローの **[名前]** を入力します。 たとえば、「*passwordreset1*」と入力します。
1. **[ID プロバイダー]** で、 **[Reset password using email address]\(メール アドレスを使用してパスワードをリセットする\)** を有効にします。
2. [アプリケーション要求] で **[Show more]\(さらに表示\)** をクリックし、アプリケーションに戻される承認トークンで返される要求を選択します。 たとえば、 **[User's Object ID] (ユーザーのオブジェクト ID)** を選択します。
3. **[OK]** をクリックします。
4. **[作成]** をクリックして、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開き、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローの実行]** をクリックし、前に作成したアカウントの電子メール アドレスを確認し、 **[続行]** を選択します。
1. これで、ユーザーのパスワードを変更できるようになりました。 パスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する

カスタム ポリシーは、ユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 サインアップとサインイン、パスワードのリセット、プロファイル編集ポリシーなど、いくつかの事前に構築されたポリシーを含むスターター パックが用意されています。 詳細については、「[Azure AD B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」を参照してください。

::: zone-end

## <a name="next-steps"></a>次のステップ

[プロファイル編集フロー](add-profile-editing-policy.md)を設定します。