---
title: Azure Active Directory B2C を使用してサンプル iOS Swift アプリケーションで認証を構成する
description: この記事では、Azure Active Directory B2C を使用して iOS Swift アプリケーションでユーザーをサインインおよびサインアップさせる方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 07c41b55d494167bcf267d4af1bdd154c6958ee9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007864"
---
# <a name="configure-authentication-in-a-sample-ios-swift-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用してサンプル iOS Swift アプリケーションで認証を構成する

この記事では、サンプルの [iOS Swift](https://developer.apple.com/swift/) アプリケーションを使用して、Azure Active Directory B2C (Azure AD B2C) 認証をモバイル アプリに追加する方法について説明します。

## <a name="overview"></a>概要

OpenID Connect (OIDC) は、OAuth 2.0 を基盤にした認証プロトコルです。 OIDC を使用して、ユーザーを安全にアプリケーションにサインインさせることができます。 このモバイル アプリのサンプルでは、OIDC 認可コード Proof Key for Code Exchange (PKCE) フローで [Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md) を使用します。 MSAL は、モバイル アプリへの認証と認可サポートの追加を簡略化する、Microsoft 提供のライブラリです。 

サインイン フローでは、次の手順が実行されます。

1. ユーザーがアプリを開き、**[サインイン]** を選択します。
1. アプリによってモバイル デバイスのシステム ブラウザーが開き、Azure AD B2C に対する認証要求が開始されます。
1. ユーザーが[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)、[パスワードのリセット](add-password-reset-policy.md)、または[ソーシャル アカウント](add-identity-provider.md)を使用したサインインを行います。
1. ユーザーがサインインに成功すると、Azure AD B2C からアプリに認可コードが返されます。
1. アプリによって次のアクションが実行されます。
    1. 認可コードが、ID トークン、アクセス トークン、更新トークンと交換されます。
    1. ID トークン要求が読み取られます。
    1. 後で使用できるように、トークンがメモリ内キャッシュに格納されます。

### <a name="app-registration-overview"></a>アプリ登録の概要

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするために、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録します。  

- **モバイル アプリケーション** の登録により、アプリでは Azure AD B2C を使用してサインインできるようになります。 アプリの登録時に、″リダイレクト URI″ を指定します。 リダイレクト URI は、ユーザーが Azure AD B2C で認証を行った後、Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、モバイル アプリを一意に識別する "アプリケーション ID″ ("クライアント ID″ とも呼ばれる) が生成されます (たとえば "アプリ ID: 1″)。

- **Web API** を登録すると、保護された Web API をアプリで呼び出すことができます。 この登録により、Web API のアクセス許可 (スコープ) が公開されます。 アプリの登録プロセスによって、Web API を一意に識別する "アプリケーション ID″ が生成されます (たとえば "アプリ ID: 2″)。 モバイル アプリ (App ID: 1) のアクセス許可を Web API スコープ (App ID: 2) に付与します。 


アプリケーションの登録とアーキテクチャについて、次の図に示します。

![Web API 呼び出しの登録とトークンが使用されているモバイル アプリの図。](./media/configure-authentication-sample-ios-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Web API の呼び出し

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>サインアウト フロー

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>前提条件

次が実行されているコンピューター: 

- [Xcode 13 以降](https://developer.apple.com/xcode/)。
- Swift および Objective-C Cocoa プロジェクト用の [CocoaPods](https://cocoapods.org/) 依存関係マネージャー。


## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>手順 2: モバイル アプリケーションを登録する

モバイル アプリと Web API アプリケーションの登録を作成し、Web API のスコープを指定します。

### <a name="step-21-register-the-web-api-app"></a>手順 2.1: Web API アプリを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>手順 2.2: Web API アプリのスコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-mobile-app"></a>手順 2.3: モバイル アプリを登録する

モバイル アプリの登録を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. **[名前]** には、アプリケーションの名前を入力します (たとえば *iOs-app1*)。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、**[パブリック クライアント/ネイティブ (モバイルとデスクトップ)]** を選択して、URL ボックスに「`msauth.com.microsoft.identitysample.MSALiOS://auth`」と入力します。
1. **[登録]** を選択します。
1. アプリ登録が完了したら、 **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後でモバイル アプリケーションを構成するときに使用します。
    ![モバイル アプリケーション ID が強調表示されたスクリーンショット。](./media/configure-authentication-sample-ios-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-24-grant-the-mobile-app-permissions-for-the-web-api"></a>手順 2.4: Web API に対するアクセス許可をモバイル アプリに付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>手順 3: サンプル Web API を構成する

このサンプルでは、モバイル アプリが Web API に対して使用できる、関連するスコープを持つアクセス トークンを取得します。 コードから Web API を呼び出すには、次の手順を実行します。

1. 既存の Web API を使用するか、新しいものを作成します。 詳細については、「[Azure AD B2C を使用して独自の Web API で認証を有効にする](enable-authentication-web-api.md)」を参照してください。
1. [Web API を呼び出す](enable-authentication-iOs-app.md#step-63-call-a-web-api)ようにサンプル コードを変更します。
1. Web API を構成したら、その Web API エンドポイントの URI をコピーします。 次の手順でその Web API エンドポイントを使用します。

> [!TIP]
> Web API がなくても、このサンプルを実行できます。 この場合、アプリでアクセス トークンが返されますが、Web API を呼び出すことはできません。 

## <a name="step-4-get-the-ios-mobile-app-sample"></a>手順 4: iOS モバイル アプリのサンプルを取得する

1. [.zip ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/archive/refs/heads/vNext.zip)するか、[GitHub リポジトリ](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)からサンプル Web アプリをクローンします。 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/tree/vNext.git
    ``` 

1. [CocoaPods](https://cocoapods.org/) を使用して MSAL ライブラリをインストールします。 ターミナル ウィンドウで、プロジェクトのルート フォルダーに移動します。 このフォルダーには、*podfile* ファイルが格納されています。 次のコマンドを実行します。

    ```bash
    pod install
    ```

1. Xcode で `MSALiOS.xcworkspace` ワークスペースを開きます。



## <a name="step-5-configure-the-sample-mobile-app"></a>手順 5: サンプル モバイル アプリを構成する

*ViewController.swift* ファイルを開きます。 `ViewController` クラス メンバーに、使用している Azure AD B2C ID プロバイダーに関する情報が含まれています。 モバイル アプリでは、この情報を使用して Azure AD B2C との信頼関係を確立し、ユーザーのサインインとサインアウトを行い、トークンを取得して検証します。 

次のクラス メンバーを更新します。

|キー  |値  |
|---------|---------|
|kTenantName| Azure AD B2C テナントの完全な[テナント名](tenant-management.md#get-your-tenant-name) (例: `contoso.onmicrosoft.com`)。|
|kAuthorityHostName|Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分 (例: `contoso.b2clogin.com`)。|
|kClientID|[手順 2.3](#step-23-register-the-mobile-app) のモバイル アプリケーション ID。|
|kRedirectUri|[手順 2.3](#step-23-register-the-mobile-app) のモバイル アプリケーションのリダイレクト URI (`msauth.com.microsoft.identitysample.MSALiOS://auth`)。|
|kSignupOrSigninPolicy| [手順 1](#step-1-configure-your-user-flow) で作成したサインアップまたはサインイン ユーザー フローまたはカスタム ポリシー。|
|kEditProfilePolicy|[手順 1](#step-1-configure-your-user-flow) で作成したプロファイル編集ユーザー フローまたはカスタム ポリシー。|
|kGraphURI| (省略可能) [手順 3](#step-3-configure-the-sample-web-api) で作成した Web API エンドポイント (例: `https://contoso.azurewebsites.net/hello`)。|
| kScopes | [手順 2.4](#step-24-grant-the-mobile-app-permissions-for-the-web-api) で作成した Web API スコープ。|
| | | 



## <a name="step-6-run-and-test-the-mobile-app"></a>手順 6: モバイル アプリを実行してテストする

1. [接続されている iOS デバイスのシミュレーター](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device)を使用して、プロジェクトを作成して実行します。

1. **[サインイン]** を選択してから、Azure AD B2C のローカルまたはソーシャル アカウントを使用して、サインアップまたはサインインします。

    ![サインイン フローを開始する方法を示すスクリーンショット。](./media/configure-authentication-sample-ios-app/sign-in.png)

1. 認証が成功すると、ナビゲーション バーに表示名が表示されます。

    ![Azure AD B2C アクセス トークンとユーザー ID を示すスクリーンショット。](./media/configure-authentication-sample-ios-app/post-sign-in.png) 

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。
* [Azure AD B2C を使用して独自の iOS アプリケーションで認証を有効にする](enable-authentication-ios-app.md)
* [Azure AD B2C を使用して iOS アプリケーションで認証オプションを構成する](enable-authentication-ios-app-options.md)
* [Azure AD B2C を使用して独自の Web API で認証を有効にする](enable-authentication-web-api.md)
