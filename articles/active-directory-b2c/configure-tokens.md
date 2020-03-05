---
title: トークンの構成 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でトークンの有効期間と互換性の設定を構成する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189619"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でトークンを構成する

この記事では、Azure Active Directory B2C (Azure AD B2C) で[トークンの有効期間と互換性](tokens-overview.md)を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

[ユーザー フローを作成](tutorial-create-user-flows.md)して、ユーザーがアプリケーションにサインアップおよびサインインできるようにします。

## <a name="configure-token-lifetime"></a>トークンの有効期間を構成する

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

## <a name="configure-token-compatibility"></a>トークンの互換性を構成する

1. **[ユーザー フロー (ポリシー)]** を選択します。
2. あらかじめ作成しておいたユーザー フローを開きます。
3. **[プロパティ]** を選択します。
4. **[トークンの互換性設定]** で、アプリケーションのニーズに合わせて次のプロパティを調整します。

    ![Azure portal のトークンの互換性プロパティの設定](./media/configure-tokens/token-compatibility.png)

5. **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ

[アクセス トークンを使用する](access-tokens.md)方法の詳細を学習してください。



