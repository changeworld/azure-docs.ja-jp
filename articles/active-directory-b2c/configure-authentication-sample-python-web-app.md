---
title: Azure Active Directory B2C を使ってサンプル Python Web アプリケーションで認証を構成する
description: この記事では、Azure Active Directory B2C を使用して Python Web アプリケーションでサインインしたりユーザーをサインアップしたりする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 1466e62e50cac9d24616c9662f73ad23f3f6eea7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007919"
---
# <a name="configure-authentication-in-a-sample-python-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使ってサンプル Python Web アプリケーションで認証を構成する

この記事では、サンプルの Python Web アプリケーションを使用して、Web アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法を説明します。

## <a name="overview"></a>概要

OpenID Connect (OIDC) は、OAuth 2.0 を基盤にした認証プロトコルです。 OIDC を使用して、ユーザーを安全にアプリケーションにサインインさせることができます。 このサンプル Web アプリでは、[Python 用の Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-python) を使用します。 Python 用 MSAL を使用すると、Python Web アプリに対して簡単に、認証と承認のサポートを追加することができます。 

サインイン フローでは、次の手順が実行されます。

1. ユーザーが Web アプリにアクセスして **[サインイン]** を選択します。 
1. アプリによって認証要求が開始され、ユーザーが Azure AD B2C にリダイレクトされます。
1. ユーザーが[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md)、[パスワードのリセット](add-password-reset-policy.md)、または[ソーシャル アカウント](add-identity-provider.md)を使用したサインインを行います。
1. ユーザーがサインインに成功すると、Azure AD B2C はアプリに ID トークンを返します。
1. アプリは、承認コードと引き換えに ID トークンを取得し、その ID トークンを検証して要求を読み取った後、安全なページをユーザーに返します。


### <a name="sign-out"></a>サインアウト

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>前提条件

次が実行されているコンピューター: 

* [Visual Studio Code](https://code.visualstudio.com/) または別のコード エディター
* [Python](https://nodejs.org/en/download/) 2.7 以降または 3 以降 

## <a name="step-1-configure-your-user-flow"></a>手順 1: ユーザー フローを構成する

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>手順 2: Web アプリケーションを登録する

アプリケーションが Azure AD B2C でサインインできるようにするには、Azure AD B2C ディレクトリにアプリを登録します。 アプリを登録すると、アプリと Azure AD B2C の間に信頼関係が確立されます。  

アプリの登録時に、*リダイレクト URI* を指定します。 リダイレクト URI は、ユーザーが Azure AD B2C で認証を行った後、Azure AD B2C によってリダイレクトされるエンドポイントです。 アプリの登録プロセスによって、アプリを一意に識別する "*アプリケーション ID*" ("*クライアント ID*" とも呼ばれます) が生成されます。 アプリが登録されると、Azure AD B2C はアプリケーション ID とリダイレクト URI の両方を使用して認証要求を作成します。 

### <a name="step-21-register-the-app"></a>手順 2.1: アプリを登録する

Web アプリの登録を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. **[名前]** で、アプリケーションの名前を入力します (*webapp1* など)。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します。 
1. **[リダイレクト URI]** で、 **[Web]** を選択し、URL ボックスに「`http://localhost:5000/getAToken`」と入力します。
1. **[アクセス許可]** で、 **[Grant admin consent to openid and offline access permissions]\(OpenID とオフラインのアクセス許可に管理者の同意を与える\)** チェック ボックスをオンにします。
1. **[登録]** を選択します。
1. **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後で Web アプリケーションを構成するときに使用します。

    ![Web アプリ ID を記録するための Web アプリの [概要] ページのスクリーンショット。](./media/configure-authentication-sample-python-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-create-a-web-app-client-secret"></a>手順 2.2: Web アプリのクライアント シークレットを作成する

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]

## <a name="step-3-get-the-web-app-sample"></a>手順 3: Web アプリのサンプルを取得する

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)するか、GitHub からサンプル Web アプリケーションをクローンします。 

```bash
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

パスの合計文字長が 260 以下のフォルダーにサンプル ファイルを展開します。

## <a name="step-4-configure-the-sample-web-app"></a>手順 4: サンプル Web アプリを構成する

プロジェクトのルート ディレクトリで、次の操作を行います。

1. *app_config.py* ファイルの名前を *app_config.py.OLD* に変更します。
1. *app_config_b2c.py* ファイルの名前を *app_config.py* に変更します。 

*app_config.py* ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 次のアプリ設定のプロパティを更新します。 

|キー  |値  |
|---------|---------|
|`b2c_tenant`| Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分 (例: `contoso`)。|
|`CLIENT_ID`| [手順 2.1](#step-21-register-the-app) の Web API アプリケーション ID。|
|`CLIENT_SECRET`| [手順 2.2](#step-22-create-a-web-app-client-secret) で作成したクライアント シークレット。 実際には、コメントでも推奨されているように、セキュリティ強化の観点から、この値は環境変数に格納することを検討してください。 |
|`*_user_flow`|ユーザー フロー、または[手順 1](#step-1-configure-your-user-flow) で作成したカスタム ポリシー。|
| | |

最終的な構成ファイルは、次の Python コードのようになります。

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.
```

> [!IMPORTANT]
> コード スニペットのコメントにも注意書きがあるように、アプリケーション コードでは、"*シークレットをプレーンテキストに格納することは避けて*" ください。 コード サンプルでは変数をハードコーディングしていますが、これは "*あくまで便宜上*" の措置です。 環境変数を使用するか、Azure キー コンテナーのようなシークレット ストアを使用することを検討してください。


## <a name="step-5-run-the-sample-web-app"></a>手順 5: サンプル Web アプリを実行する

1. コンソールまたはターミナルから、サンプルが格納されているディレクトリに切り替えます。 次に例を示します。

    ```console
    cd ms-identity-python-webapp
    ```
1. 次のコマンドを実行して、PyPi から必要なパッケージをインストールし、ローカル コンピューターで Web アプリを実行します。

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    コンソール ウィンドウには、ローカルで稼働中のアプリケーションのポート番号が表示されます。

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on `http://localhost:5000/` (Press CTRL+C to quit)
    ```

 
1. ローカル コンピューターで実行されている Web アプリケーションを表示するために、`http://localhost:5000` に移動します。 

1. **[サインイン]** を選択します。

    ![Azure AD B2C でのサインインを示すスクリーンショット。](./media/configure-authentication-sample-python-web-app/web-app-sign-in.png)


1. サインインアップまたはサインイン プロセスを完了します。

1. 認証が成功すると、自分の表示名が次のように表示されます。 

    ![Web アプリのトークンの表示名要求を示すスクリーンショット。](./media/configure-authentication-sample-python-web-app/web-app-token-claims.png)


## <a name="step-6-call-to-a-web-api"></a>ステップ 6: Web API を呼び出す

アプリで Azure AD B2C を使用してサインインし、Web API を呼び出せるようにするには、Azure AD B2C ディレクトリに 2 つのアプリケーションを登録する必要があります。  

- [手順 2.](#step-2-register-a-web-application) で既に作成した **Web アプリケーション** (Python) 登録。 このアプリ登録により、Azure AD B2C を使用したアプリのサインインが可能となります。  アプリの登録プロセスによって、アプリを一意に識別する "*アプリケーション ID*" ("*クライアント ID*" とも呼ばれます) が生成されます。 たとえば、**アプリ ID: 1** などです。

- **Web API** を登録すると、保護された Web API をアプリで呼び出すことができます。 この登録により、Web API のアクセス許可 (スコープ) が公開されます。 アプリの登録プロセスによって、Web API を一意に識別する "*アプリケーション ID*" が生成されます (たとえば "*アプリ ID: 2*")。 アプリ (アプリ ID: 1) のアクセス許可を Web API スコープ (アプリ ID: 2) に付与します。  

次の図で、アプリの登録とアプリケーション アーキテクチャについて説明しています。

![Web API、登録、トークンを含む Web アプリの説明図。](./media/configure-authentication-sample-python-web-app/web-app-with-api-architecture.png) 

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="step-61-register-the-web-api-app"></a>手順 6.1: Web API アプリを登録する

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-62-configure-scopes"></a>手順 6.2: スコープを構成する

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-63-grant-the-web-app-permissions"></a>手順 6.3: Web アプリにアクセス許可を付与する

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

### <a name="step-64-configure-your-web-api"></a>手順 6.4: Web API を構成する

このサンプルでは、Web アプリが Web API に対して使用できる、適切なスコープを含んだアクセス トークンを取得します。 Web API をコードから呼び出す場合は、既存の Web API を使用するか、新しい Web API を作成します。 詳細については、「[Azure AD B2C を使用して独自の Web API で認証を有効にする](enable-authentication-web-api.md)」を参照してください。

### <a name="step-65-configure-the-sample-app-with-the-web-api"></a>手順 6.5: Web API を使用してサンプル アプリを構成する

*app_config.py* ファイルを開きます。 このファイルには、Azure AD B2C ID プロバイダーに関する情報が含まれています。 アプリ設定の次のプロパティを更新します。 

|Key  |値  |
|---------|---------|
|`ENDPOINT`| Web API の URI (例: `https://localhost:44332/hello`)。|
|`SCOPE`| 作成済みの Web API [スコープ](#step-62-configure-scopes)。|
| | |

最終的な構成ファイルは、次の Python コードのようになります。

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.

### More code here

# This is the API resource endpoint
ENDPOINT = 'https://localhost:44332' 


SCOPE = ["https://contoso.onmicrosoft.com/api/demo.read", "https://contoso.onmicrosoft.com/api/demo.write"] 
```

### <a name="step-66-run-the-sample-app"></a>手順 6.6: サンプル アプリを実行する

1. コンソールまたはターミナルから、サンプルが格納されているディレクトリに切り替えます。 
1. アプリを停止します。 その後、再実行します。
1. **[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** を選択します。

    ![Web API の呼び出し方法を示すスクリーンショット。](./media/configure-authentication-sample-python-web-app/call-web-api.png)

## <a name="step-7-deploy-your-application"></a>手順 7: アプリケーションをデプロイする 

運用アプリケーションでは、アプリ登録のリダイレクト URI は通常、アプリが実行されているパブリックにアクセス可能なエンドポイント (`https://contoso.com/getAToken` など) です。 

お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。 リダイレクト URI には、次の制限があります。

* 応答 URL は、スキーム `https` で始まる必要があります。
* 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 

## <a name="next-steps"></a>次のステップ
* [Azure AD B2C を使用して Python Web アプリの認証オプションを構成する](enable-authentication-python-web-app-options.md)方法をご覧ください。
