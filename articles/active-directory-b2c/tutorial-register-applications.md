---
title: チュートリアル - Azure Active Directory B2C でアプリケーションを登録する | Microsoft Docs
description: Azure portal を使用して Azure Active Directory B2C でアプリケーションを登録する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 511e1e9f29e6ae7602a977819f5295f76236595d
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248724"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C でアプリケーションを登録する

[アプリケーション](active-directory-b2c-apps.md)が Azure Active Directory (Azure AD) B2C と対話できるようにするには、管理しているテナントに登録する必要があります。 このチュートリアルでは、Azure portal を使用してアプリケーションを登録する方法を示します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Web アプリケーションの登録
> * Web API の登録
> * モバイル/ネイティブ アプリケーションの登録

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

独自の [Azure AD B2C テナント](tutorial-create-tenant.md)をまだ作成していない場合、すぐに作成してください。 既存のテナントを使用できます。

## <a name="register-a-web-application"></a>Web アプリケーションの登録

1. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。

    ![サブスクリプション ディレクトリに切り替える](./media/tutorial-register-applications/switch-directories.png)

2. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
3. **[アプリケーション]** を選択し、**[追加]** を選択します。

    ![[アプリケーションの追加]](./media/tutorial-register-applications/add-application.png)

4. アプリケーションの名前を入力します。 たとえば、*webapp1* とします。
5. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、**[はい]** を選択します。
6. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 たとえば、`https://localhost:44316` でローカルにリッスンするように設定できます。ポート番号がわからない場合は、プレースホルダーの値を入力し、後で変更できます。 テスト用には、`https://jwt.ms` に設定して、検査のためにトークンの内容が表示されるようにすることができます。 このチュートリアルでは、`https://jwt.ms` に設定します。 

    応答 URL は `https` スキームで始まる必要があり、すべての応答 URL 値で 1 つの DNS ドメインを共有する必要があります。 たとえば、アプリケーションの応答 URL が `https://login.contoso.com` の場合、URL `https://login.contoso.com/new` のように追加することができます。 または、`https://new.login.contoso.com` のように、`login.contoso.com` の DNS サブドメインを参照することもできます。 アプリケーションに `login-east.contoso.com` と `login-west.contoso.com` の応答 URL を設定する場合は、これらの応答 URL を `https://contoso.com`、`https://login-east.contoso.com`、`https://login-west.contoso.com` の順に追加する必要があります。 後の 2 つの応答 URL を追加できるのは、これらが 1 つ目の `contoso.com` という応答 URL のサブドメインであるためです。

7. **Create** をクリックしてください。

    ![アプリケーションのプロパティを設定する](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>クライアント シークレットの作成

お客様のアプリケーションでコードをトークンと交換する場合は、アプリケーション シークレットを作成する必要があります。

1. **[キー]**、**[キーの生成]** の順に選択します。

    ![キーを生成する](./media/tutorial-register-applications/generate-keys.png)

2. **[保存]** を選択し、キーを参照します。 **アプリ キー** の値をメモしておきます。 アプリケーションのコード内では、この値をアプリケーション シークレットとして使用します。

    ![キーを保存する](./media/tutorial-register-applications/save-key.png)
    
3. **[API アクセス]**、**[追加]** の順に選択し、Web API とスコープ (アクセス許可) を選択します。

    ![API アクセスを構成する](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>Web API の登録

1. **[アプリケーション]** を選択し、**[追加]** を選択します。
3. アプリケーションの名前を入力します。 たとえば、*webapi1* とします。
4. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、**[はい]** を選択します。
5. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 たとえば、ローカルでリッスンするようにこれを `https://localhost:44316` に設定します。 ポート番号がわからない場合は、プレースホルダーの値を入力し、後で変更します。
6. **[アプリケーション ID URI]** には、ご使用の Web API で使用される ID を入力します。 ドメインを含んだ完全な識別子 URI が自動的に生成されます。 たとえば、「 `https://contosotenant.onmicrosoft.com/api` 」のように入力します。
7. **Create** をクリックしてください。
8. 作成した *webapi1* アプリケーションを選択した後、必要に応じて **[公開済みスコープ]** を選択してスコープを追加します。 既定で `user_impersonation` のスコープは定義済みです。 `user_impersonation` のスコープにより他のアプリケーションが、サインイン ユーザーの代わりにこの API にアクセスできるようになります。 希望する場合、`user_impersonation` のスコープは削除できます。

    ![公開済みスコープを設定する](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>モバイル/ネイティブ アプリケーションの登録

1. **[アプリケーション]** を選択し、**[追加]** を選択します。
2. アプリケーションの名前を入力します。 たとえば、*nativeapp1* とします。
3. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** の場合、**[いいえ]** を選択します。
4. **[ネイティブ クライアントを含める]** の場合、**[はい]** を選択します。
5. **[リダイレクト URI]** に、カスタム スキームを含めた有効なリダイレクト URI を入力します。 リダイレクト URI を選択する際には、2 つの重要な考慮事項があります。

    - **一意** - リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 `com.onmicrosoft.contoso.appname://redirect/path` の例では、`com.onmicrosoft.contoso.appname` はスキームです。 このパターンに従う必要があります。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーにはアプリケーションを選択する選択肢が与えられます。 ユーザーが不適切な選択を行った場合、サインインは失敗します。
    - **完全** - リダイレクト URI には、スキームとパスが必要です。 パスには、ドメインの後に少なくとも 1 つのスラッシュを含める必要があります。 たとえば、`//contoso/` は機能しますが、`//contoso` は失敗します。 リダイレクト URI にアンダースコアなどの特殊文字は含めないようにしてください。

6. **Create** をクリックしてください。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Web アプリケーションの登録
> * Web API の登録
> * モバイル/ネイティブ アプリケーションの登録

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 内にユーザー フローを作成する](tutorial-create-user-flows.md)