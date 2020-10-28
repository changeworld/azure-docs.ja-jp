---
title: トークンの構成 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でトークンの有効期間と互換性の設定を構成する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67bc9d6b35d4841999721a00592a6bbe23bff10f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340225"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でトークンを構成する

この記事では、Azure Active Directory B2C (Azure AD B2C) で[トークンの有効期間と互換性](tokens-overview.md)を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

[ユーザー フローを作成](tutorial-create-user-flows.md)して、ユーザーがアプリケーションにサインアップおよびサインインできるようにします。

## <a name="configure-jwt-token-lifetime"></a>JWT トークンの有効期間を構成する

トークンの有効期間は任意のユーザー フローで構成できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー (ポリシー)]** を選択します。
5. あらかじめ作成しておいたユーザー フローを開きます。
6. **[プロパティ]** を選択します。
7. **[トークンの有効期間]** で、アプリケーションのニーズに合わせて次のプロパティを調整します。

    ![Azure portal のトークンの有効期間プロパティの設定](./media/configure-tokens/token-lifetime.png)

8. **[保存]** をクリックします。

> [!NOTE]
> PKCE での承認コード フローを使用するシングルページ アプリケーションには、24 時間有効な更新トークンが常に存在しています。 「[ブラウザーでの更新トークンのセキュリティへの影響](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)」で詳細情報を参照してください。

## <a name="configure-jwt-token-compatibility"></a>JWT トークンの互換性を構成する

1. **[ユーザー フロー (ポリシー)]** を選択します。
2. あらかじめ作成しておいたユーザー フローを開きます。
3. **[プロパティ]** を選択します。
4. **[トークンの互換性設定]** で、アプリケーションのニーズに合わせて次のプロパティを調整します。

    ![Azure portal のトークンの互換性プロパティの設定](./media/configure-tokens/token-compatibility.png)

5. **[保存]** をクリックします。

## <a name="provide-optional-claims-to-your-app"></a>アプリに省略可能な要求を提供する

アプリケーション要求は、アプリケーションに返される値です。 ユーザー フローを更新して、目的の要求を含めます。

1. **[ユーザー フロー (ポリシー)]** を選択します。
1. あらかじめ作成しておいたユーザー フローを開きます。
1. **[アプリケーション クレーム]** を選択します。
1. アプリケーションに返信する要求と属性を選択します。
1. **[保存]** をクリックします。


## <a name="next-steps"></a>次のステップ

[アクセス トークンを要求](access-tokens.md)方法の詳細を確認します。



