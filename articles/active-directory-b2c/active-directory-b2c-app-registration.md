---
title: Azure Active Directory B2C へのアプリケーションの登録 | Microsoft Docs
description: Azure Active Directory B2C にアプリケーションを登録する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fcebada4ef10c3e0bb298e9308d66ecb37247832
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999245"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C にアプリケーションを登録する

コンシューマーのサインアップおよびサインインを受け付ける[アプリケーション](active-directory-b2c-apps.md)を作成するには、最初にアプリケーションを Azure AD B2C テナントに登録する必要があります。 この記事を読むと、Azure Active Directory (Azure AD) B2C テナントに数分でアプリケーションを登録できるようになります。 完了すると、Azure AD B2C テナントで使用するアプリケーションが登録されます。

## <a name="prerequisites"></a>前提条件

「[Azure Active Directory B2C テナントを作成する](tutorial-create-tenant.md)」に書かれている手順に従って独自のテナントを取得します。

アプリケーションの種類に基づいて次の手順を選択する

- [Web アプリケーションの登録](#register-a-web-application)
- [Web API の登録](#register-a-web-api)
- [モバイル/ネイティブ アプリケーションの登録](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>Web アプリケーションの登録

1. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
2. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
3. **[アプリケーション]** を選択し、**[追加]** を選択します。
4. アプリケーションの名前を入力します。 たとえば、「*testapp1*」と入力します。
5. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、**[はい]** を選択します。
6. **[応答 URL]** には、ご使用のアプリが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 たとえば、ローカルでリッスンするようにこれを `https://localhost:44316` に設定します。 ポート番号がわからない場合は、プレースホルダーの値を入力し、後で変更します。
7. **Create** をクリックしてください。

### <a name="create-a-client-secret"></a>クライアント シークレットの作成

アプリケーションが Azure AD B2C によってセキュリティ保護されている Web API を呼び出す場合は、アプリケーション シークレットを作成する必要があります。

1. **[キー]**、**[キーの生成]** の順に選択します。 
2. **[保存]** を選択し、キーを参照します。 **[アプリ キー]** の値をメモしておきます。 アプリケーションのコード内では、この値をアプリケーション シークレットとして使用します。
3. **[API アクセス]**、**[追加]** の順に選択し、Web API とスコープ (アクセス許可) を選択します。

## <a name="register-a-web-api"></a>Web API の登録

1. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
2. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
3. **[アプリケーション]** を選択し、**[追加]** を選択します。
4. アプリケーションの名前を入力します。 たとえば、「*testapp2*」と入力します。
5. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、**[はい]** を選択します。
6. **[応答 URL]** には、ご使用のアプリが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 たとえば、ローカルでリッスンするようにこれを `https://localhost:44316` に設定します。 ポート番号がわからない場合は、プレースホルダーの値を入力し、後で変更します。
7. **[アプリケーション ID URI]** には、ご使用の Web API で使用される ID を入力します。 ドメインを含んだ完全な識別子 URI が自動的に生成されます。 たとえば、「 `https://contosotenant.onmicrosoft.com/api` 」のように入力します。
8. **Create** をクリックしてください。
9. 必要に応じて **[公開済みスコープ]** を選択してスコープを追加します。 既定で `user_impersonation` のスコープは定義済みです。 `user_impersonation` のスコープにより他のアプリケーションが、サインイン ユーザーの代わりにこの API にアクセスできるようになります。 希望する場合、`user_impersonation` のスコープは削除できます。

## <a name="register-a-mobile-or-native-application"></a>モバイル/ネイティブ アプリケーションの登録

1. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
2. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
3. **[アプリケーション]** を選択し、**[追加]** を選択します。
4. アプリケーションの名前を入力します。 たとえば、「*testapp3*」と入力します。
5. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** の場合、**[いいえ]** を選択します。
6. **[ネイティブ クライアントを含める]** の場合、**[はい]** を選択します。
7. **[リダイレクト URI]** には、[カスタム スキームを含めたリダイレクト URI を入力します](active-directory-b2c-apps.md)。 正常に機能するリダイレクト URI を選択し、アンダースコアなどの特殊文字は含めないようにしてください。
8. **Create** をクリックしてください。

### <a name="create-a-client-secret"></a>クライアント シークレットの作成

アプリケーションが Azure AD B2C によってセキュリティ保護されている Web API を呼び出す場合は、アプリケーション シークレットを作成する必要があります。

1. **[キー]**、**[キーの生成]** の順に選択します。 
2. **[保存]** を選択し、キーを参照します。 **[アプリ キー]** の値をメモしておきます。 アプリケーションのコード内では、この値をアプリケーション シークレットとして使用します。
3. **[API アクセス]**、**[追加]** の順に選択し、Web API とスコープ (アクセス許可) を選択します。

## <a name="next-steps"></a>次の手順

アプリケーションでアクセス トークンを使用して API にアクセス許可を付与する方法の詳細について、[アクセス トークンの要求](active-directory-b2c-access-tokens.md)に関する記事で学習します。
