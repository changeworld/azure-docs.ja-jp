---
title: Azure Active Directory B2C を使用してサンプル iOS Swift アプリケーションで認証を構成する
description: iOS Swift アプリケーションで Azure Active Directory B2C を使用してユーザーをサインインおよびサインアップします。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 1e6a76b5a534bb127dfaaab8e787cb446430c8d6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779202"
---
# <a name="configure-authentication-in-a-sample-ios-swift-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用してサンプル iOS Swift アプリケーションで認証を構成する

この記事では、サンプルの [iOS Swift](https://developer.apple.com/swift/) アプリケーションを使用して、Azure Active Directory B2C (Azure AD B2C) 認証をモバイル アプリに追加する方法について説明します。

## <a name="overview"></a>概要

OpenID Connect (OIDC) は OAuth 2.0 を基盤とした認証プロトコルであり、アプリケーションにユーザーを安全にサインインさせるために利用できます。 このモバイル アプリのサンプルでは、OpenId Connect の認証コード PKCE フローで [MSAL](../active-directory/develop/msal-overview.md) ライブラリを使用します。 MSAL ライブラリは、モバイル アプリへの認証と承認サポートの追加を簡略化する、Microsoft が提供しているライブラリです。 

サインイン フローでは、次の手順が実行されます。

1. ユーザーがアプリを開き、 **[サインイン]** を選択します。
1. アプリによってモバイル デバイスのシステム ブラウザーが開き、Azure AD B2C に対する認証要求が開始されます。
1. ユーザーは[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)するか、[パスワードをリセット](add-password-reset-policy.md)するか、[ソーシャル アカウント](add-identity-provider.md)を使用してサインインします。
1. サインインに成功すると、Azure AD B2C からアプリに承認コードが返されます。
1. アプリによって次のアクションが実行されます。
    1. 承認コードを ID トークン、アクセス トークン、更新トークンと交換する。
    1. ID トークン要求を読み取る。
    1. 後で使用できるように、トークンをメモリ内キャッシュに格納する。

### <a name="app-registration-overview"></a>アプリ登録の概要

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするために、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録します。  

- **モバイル アプリケーション** の登録により、アプリでは Azure AD B2C を使用してサインインできるようになります。 アプリの登録時に、"*リダイレクト URI*" を指定します。 リダイレクト URI は、ユーザーが Azure AD B2C で認証された後に Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、モバイル アプリを一意に識別する "*アプリケーション ID*" ("*クライアント ID*" とも呼ばれます) が生成されます。 たとえば、**App ID: 1** です。

- **Web API** を登録すると、保護された Web API をアプリで呼び出すことができます。 この登録により、Web API のアクセス許可 (スコープ) が公開されます。 アプリの登録プロセスによって、Web API を一意に識別する "*アプリケーション ID*" が生成されます。  たとえば、**App ID: 2** です。 モバイル アプリ (App ID: 1) に Web API スコープ (App ID: 2) に対するアクセス許可を付与します。 


次の図では、アプリの登録とアプリケーションのアーキテクチャについて説明します。

![Web API、登録、トークンを含むモバイル アプリの説明図。](./media/configure-authentication-sample-ios-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Web API の呼び出し

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>サインアウト

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>前提条件

次が実行されているコンピューター: 

- [Xcode](https://developer.apple.com/xcode/) 13 以降。
- Swift および Objective-C Cocoa プロジェクト用の [CocoaPods](https://cocoapods.org/) 依存関係マネージャー。


## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>手順 2: モバイル アプリケーションを登録する

この手順では、モバイル アプリと Web API アプリケーションの登録を作成し、Web API のスコープを指定します。

### <a name="21-register-the-web-api-app"></a>2.1 Web API アプリを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 Web API アプリのスコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 モバイル アプリを登録する

モバイル アプリ登録を作成するには、こちらの手順に従います。

1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*iOs-app1* です。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[パブリック クライアント/ネイティブ (モバイルとデスクトップ)]** を選択して、「`msauth.com.microsoft.identitysample.MSALiOS://auth`」と入力します。
1. **[登録]** を選択します。
1. アプリ登録が完了したら、 **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後の手順でモバイル アプリケーションを構成するときに使用します。
    ![モバイル アプリケーション ID を取得する方法を示すスクリーンショット。](./media/configure-authentication-sample-ios-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 Web API に対するアクセス許可をモバイル アプリに付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>手順 3: サンプル Web API を構成する

このサンプルでは、モバイル アプリが Web API に対して使用できる、関連するスコープを持つアクセス トークンを取得します。  コードから Web API を呼び出すには、こちらの手順を実行します。

1. 既存の Web API を使用するか、新たに作成します。 詳細については、「[Azure AD B2C を使用した独自の WEB API での認証の有効化](enable-authentication-web-api.md)」を参照してください。
1. [Web API を呼び出す](enable-authentication-iOs-app.md#call-a-web-api)ようにサンプル コードを変更します。

Web API を構成したら、その Web API エンドポイントの URI をコピーします。 次の手順でその Web API エンドポイントを使用します。

> [!TIP]
> Web API がなくても、このサンプルを実行できます。 この場合、アプリでアクセス トークンが返されますが、Web API を呼び出すことはできません。 

## <a name="step-4-get-the-ios-mobile-app-sample"></a>手順 4: iOS モバイル アプリのサンプルを取得する

1. [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/archive/refs/heads/vNext.zip)するか、[GitHub リポジトリ](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)からサンプル Web アプリケーションをクローンします。 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/tree/vNext.git
    ``` 

1. [CocoaPods](https://cocoapods.org/) を使用して MSAL ライブラリをインストールします。 ターミナル ウィンドウで、プロジェクトのルート フォルダーに移動します。 このフォルダーには、`podfile` が格納されています。 次のコマンドを実行します。

    ```bash
    pod install
    ```

1. Xcode で `MSALiOS.xcworkspace` ワークスペースを開きます。



## <a name="step-5-configure-the-sample-mobile-app"></a>手順 5: サンプル モバイル アプリを構成する

`ViewController.swift` ファイルを開きます。 `ViewController` クラス メンバーには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 モバイル アプリでは、この情報を使用して Azure AD B2C との信頼関係を確立し、ユーザーのサインインとサインアウトを行い、トークンを取得して検証します。 

次のメンバーを更新します。

|Key  |値  |
|---------|---------|
|kTenantName| Azure AD B2C テナントの完全な[テナント名](tenant-management.md#get-your-tenant-name)。 たとえば、「 `contoso.onmicrosoft.com` 」のように入力します。|
|kAuthorityHostName|Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分。 たとえば、「 `contoso.b2clogin.com` 」のように入力します。|
|kClientID|[手順 2.3](#23-register-the-mobile-app) のモバイル アプリケーション ID。|
|kRedirectUri|[手順 2.3](#23-register-the-mobile-app) のモバイル アプリケーションのリダイレクト URI (`msauth.com.microsoft.identitysample.MSALiOS://auth`)。|
|kSignupOrSigninPolicy| [手順 1](#step-1-configure-your-user-flow) で作成したサインアップまたはサインイン ユーザー フローまたはカスタム ポリシー。|
|kEditProfilePolicy|[手順 1](#step-1-configure-your-user-flow) で作成したプロファイル編集ユーザー フローまたはカスタム ポリシー。|
|kGraphURI| (省略可能) [手順 3](#step-3-configure-the-sample-web-api) で作成した Web API エンドポイント。 たとえば、「 `https://contoso.azurewebsites.net/hello` 」のように入力します。|
| kScopes | [手順 2.4](#24-grant-the-mobile-app-permissions-for-the-web-api) で作成した Web API スコープ。| 



## <a name="step-6-run-and-test-the-mobile-app"></a>手順 6: モバイル アプリを実行してテストする

1. [接続されている iOS デバイスのシミュレーター](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device)を使用して、プロジェクトを作成して実行します。

1. **[サインイン]** を選択します。 次に、Azure AD B2C のローカルまたはソーシャル アカウントを使用してサインアップまたはサインインします。

    ![サインイン フローを開始する方法を示すスクリーンショット。](./media/configure-authentication-sample-ios-app/sign-in.png)

1. 認証が成功すると、ナビゲーション バーに表示名が表示されます。

    ![Azure AD B2C アクセス トークンとユーザー ID を示すスクリーンショット。](./media/configure-authentication-sample-ios-app/post-sign-in.png) 

## <a name="next-steps"></a>次のステップ

* [独自の iOS アプリケーションで認証を有効にする](enable-authentication-ios-app.md)方法について学習する
* [iOS アプリケーションで認証オプションを構成する](enable-authentication-ios-app-options.md)
* [ユーザー自身の Web API で認証を有効にする](enable-authentication-web-api.md)
