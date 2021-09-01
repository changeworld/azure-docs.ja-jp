---
title: Azure Active Directory B2C を使用してサンプル Android アプリケーションで認証を構成する
description: Android アプリケーションで Azure Active Directory B2C を使用してユーザーをサインインおよびサインアップします。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b77ef28c5161c92bf8eb6c22cf62d5af41698441
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2021
ms.locfileid: "121778811"
---
# <a name="configure-authentication-in-a-sample-android-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用してサンプル Android アプリケーションで認証を構成する

この記事では、サンプルの Android アプリケーション (Kotlin および Java) を使用して、モバイル アプリに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法を説明します。

## <a name="overview"></a>概要

OpenID Connect (OIDC) は OAuth 2.0 を基盤とした認証プロトコルで、これを使用することで、アプリケーションにユーザーを安全にサインインさせることができます。 このモバイル アプリのサンプルでは、OpenId Connect の認証コード PKCE フローで [MSAL](../active-directory/develop/msal-overview.md) ライブラリを使用します。 MSAL ライブラリは、モバイル アプリへの認証と承認サポートの追加を簡略化する、Microsoft が提供しているライブラリです。 

サインイン フローでは、次の手順が実行されます。

1. ユーザーがアプリを開き、**サインイン** を選択します。
1. アプリがモバイル デバイスのシステム ブラウザーを開き、Azure AD B2C に対する認証要求を開始します。
1. ユーザーは[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)するか、[パスワードをリセット](add-password-reset-policy.md)するか、[ソーシャル アカウント](add-identity-provider.md)を使用してサインインします。
1. サインインに成功すると、Azure AD B2C からアプリに承認コードが返されます。
1. アプリによって次の処理が実行されます。
    1. 承認コードが、ID トークン、アクセス トークン、更新トークンと交換されます。
    1. ID トークン要求が読み取られます。
    1. 後で使用できるように、トークンがメモリ内キャッシュに格納されます。

### <a name="app-registration-overview"></a>アプリ登録の概要

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするには、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録します。  

- **モバイル アプリケーション** の登録により、アプリでは Azure AD B2C を使用してサインインできるようになります。 アプリの登録時に、"*リダイレクト URI*" を指定します。 リダイレクト URI は、Azure AD B2C での認証が完了した後にユーザーが Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、モバイル アプリを一意に識別する "*アプリケーション ID*" ("*クライアント ID*" とも呼ばれます) が生成されます。 たとえば、**App ID: 1** のような ID が生成されます。

- **Web API** を登録すると、アプリは保護された Web API を呼び出せるようになります。 この登録により、Web API のアクセス許可 (スコープ) が公開されます。 このアプリ登録プロセスによって、Web API を一意に識別する "*アプリケーション ID*" が生成されます。  たとえば、**App ID: 2** のような ID が生成されます。 モバイル アプリ (App ID: 1) のアクセス許可を Web API スコープ (App ID: 2) に付与します。 


次の図では、アプリの登録とアプリケーションのアーキテクチャについて説明します。

![Web API 呼び出しの登録とトークンが使用されているモバイル アプリ](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Web API の呼び出し

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>サインアウト

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>前提条件

次が実行されているコンピューター: 


- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 以上
- [Apache Maven](https://maven.apache.org/)
- [Android API Level 16](https://developer.android.com/studio/releases/platforms) 以上
- [Android studio](https://developer.android.com/studio)、または別のコード エディター


## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>手順 2: モバイル アプリケーションを登録する

この手順では、モバイル アプリと Web API アプリケーションの登録を作成し、Web API のスコープを指定します。

### <a name="21-register-the-web-api-app"></a>2.1 Web API アプリを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 Web API アプリのスコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 モバイル アプリを登録する

モバイル アプリ登録を作成するには、次の手順に従います。

1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、「*android-app1*」と入力します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[Public client/native (mobile & desktop)]\(パブリック クライアント/ネイティブ (モバイルとデスクトップ)\)** を選択してから、[URL] テキスト ボックスに次のいずれかの URI を入力します。
    - Kotlin サンプルの場合: `msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - Java サンプルの場合: `msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. **[登録]** を選択します。
1. アプリ登録が完了したら、 **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後の手順でモバイル アプリケーションを構成するときに使用します。

    ![モバイル アプリケーション ID を取得する](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 Web API に対するアクセス許可をモバイル アプリに付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>手順 3: Android モバイル アプリのサンプルを取得する

[Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip) または [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip) のいずれかのサンプルをダウンロードします。 サンプルの ZIP ファイルを作業フォルダーに抽出します。

または、GitHub からサンプル Android モバイル アプリケーションを複製します。 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```bash
git clone https://github.com/Azure-Samples/ms-identity-android-kotlin
```

#### <a name="java"></a>[Java](#tab/java)

```bash
git clone https://github.com/Azure-Samples/ms-identity-android-java
```

--- 


## <a name="step-4-configure-the-sample-web-api"></a>手順 4: サンプル Web API を構成する

このサンプルでは、モバイル アプリが Web API に対して使用できる、関連するスコープを持つアクセス トークンを取得します。 コードから Web API を呼び出すには、次の手順を実行します。

1. 既存の Web API を使用するか、新しいものを作成します。 詳細については、「[Azure AD B2C を使用した独自の WEB API での認証の有効化](enable-authentication-web-api.md)」を参照してください。
1. [Web API を呼び出す](enable-authentication-android-app.md#call-a-web-api)ようにサンプル コードを変更します。

## <a name="step-5-configure-the-sample-mobile-app"></a>手順 5: サンプル モバイル アプリを構成する

Android Studio またはその他のコード エディターでサンプル プロジェクトを開きます。  次に、`/app/src/main/res/raw/auth_config_b2c.json` ファイルを開きます。 

*auth_config_b2c.json* 構成ファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 モバイル アプリで、この情報を使用して、Azure AD B2C との信頼関係を確立し、ユーザーのサインインとサインアウトを行い、トークンを取得して、トークンを検証します。 

アプリ設定の次のプロパティを更新します。

|Key  |値  |
|---------|---------|
| [client_id](../active-directory/develop/msal-client-application-configuration.md#client-id) | [手順 2.3](#23-register-the-mobile-app) のモバイル アプリケーション ID。 | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | [手順 2.3](#23-register-the-mobile-app) のモバイル アプリケーション リダイレクト URI。 | 
| [authorities](../active-directory/develop/msal-client-application-configuration.md#authority)| 機関は、MSAL がトークンを要求できるディレクトリを示す URL です。 次の形式を使用します: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`。 `<your-tenant-name>` を Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)に置き換えます。 次に、`<your-sign-in-sign-up-policy>` を、ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシーに置き換えます。 | 


`B2CConfiguration` クラスを開き、次のクラス メンバーを更新します。

|Key  |値  |
|---------|---------|
| ポリシー| ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシーの一覧。|
| azureAdB2CHostName| Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分。 たとえば、「 `https://contoso.b2clogin.com` 」のように入力します。|
| tenantName| Azure AD B2C テナントの完全な[テナント名](tenant-management.md#get-your-tenant-name)。 たとえば、「 `contoso.onmicrosoft.com` 」のように入力します。|
| スコープ| [手順 2.4](#24-grant-the-mobile-app-permissions-for-the-web-api) で作成した Web API スコープ。|


## <a name="step-6-run-and-test-the-mobile-app"></a>手順 6: モバイル アプリを実行してテストする

1. プロジェクトをビルドして実行します。
1. ハンバーガーのアイコンを選択します。
    
    ![ハンバーガーのアイコンの選択方法を示すスクリーンショット。](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. **B2C モード** を選択します。

    ![B2C モードの選択方法を示すスクリーンショット。](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. **[RUN USER FLOW]\(ユーザー フローの実行\)** を選択します。

    ![サインイン フローの開始方法を示すスクリーンショット。](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. Azure AD B2C ローカル アカウントまたはソーシャル アカウントを使用してサインアップまたはサインインします。

1. 認証が成功すると、ナビゲーション バーに表示名が表示されます。

    ![Azure AD B2C アクセス トークンとユーザー ID。](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>次のステップ

* [独自の Android アプリケーションで認証を有効にする](enable-authentication-android-app.md)方法について学習する
* [Android アプリケーションで認証オプションを構成する](enable-authentication-android-app-options.md)
* [独自の Web API で認証を有効にする](enable-authentication-web-api.md)
