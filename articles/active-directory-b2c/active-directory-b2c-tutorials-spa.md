---
title: チュートリアル - Azure Active Directory B2C を使用してアカウントによるシングルページ アプリの認証を有効にする | Microsoft Docs
description: シングルページ アプリケーション (JavaScript) で Azure Active Directory B2C を使用してユーザー ログインを提供する方法に関するチュートリアル。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: fffffbf7ce654c263976378da01f032599145a94
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591569"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C を使用してアカウントによるシングルページ アプリの認証を有効にする

このチュートリアルでは、シングルページ アプリケーション (SPA) で Azure Active Directory (Azure AD) B2C を使用してユーザーをサインインおよびサインアップする方法を紹介します。 Azure AD B2C を使用すると、アプリは、オープンな標準プロトコルを使用してソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対して認証することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントにシングルページ アプリケーションのサンプルを登録する。
> * ユーザーのサインアップ、サインイン、プロファイルの編集、パスワードのリセットに関するポリシーを作成する。
> * Azure AD B2C テナントを使用するようにサンプル アプリケーションを構成する。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 独自の [Azure AD B2C テナント](active-directory-b2c-get-started.md)を作成する。
* **ASP.NET および Web 開発**のワークロードと共に、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールする。
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 以降
* [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>シングルページ アプリの登録

Azure Active Directory から[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)を受け取ることができるように、アプリケーションをテナントに[登録](../active-directory/develop/developer-glossary.md#application-registration)しておく必要があります。 アプリの登録によって、テナント内のアプリの[アプリケーション ID](../active-directory/develop/developer-glossary.md#application-id-client-id) が作成されます。 

Azure AD B2C テナントの全体管理者として、[Azure Portal](https://portal.azure.com/) にログインします。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal のサービス一覧から **[Azure AD B2C]** を選択します。 

2. B2C の設定で、**[アプリケーション]** をクリックし、**[追加]** をクリックします。 

    テナントにサンプル Web アプリを登録するには、以下の設定を使用します。
    
    ![新しいアプリの追加](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | My sample single page app | 使用者がアプリの機能を把握できる**名前**を入力します。 | 
    | **Web アプリ/Web API を含める** | [はい] | シングルページ アプリの場合は **[はい]** を選択します。 |
    | **暗黙的フローを許可する** | [はい] | アプリでは [OpenID Connect サインイン](active-directory-b2c-reference-oidc.md)が使用されるため、**[はい]** を選択します。 |
    | **応答 URL** | `http://localhost:6420` | 応答 URL は、アプリが要求したトークンを Azure AD B2C が返すエンドポイントです。 このチュートリアルでは、サンプルはローカル (localhost) で実行され、ポート 6420 でリッスンします。 |
    | **ネイティブ クライアントを含める** | いいえ  | これはネイティブ クライアントではなくシングルページ アプリであるため、[いいえ] を選択します。 |
    
3. **[作成]** をクリックしてアプリを登録します。

登録されたアプリは、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧からシングルページ アプリを選択します。 登録済みのシングルページ アプリのプロパティ ウィンドウが表示されます。

![シングルページ アプリのプロパティ](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

**[アプリケーション クライアント ID]** をメモします。 この ID はアプリを一意に識別するため、このチュートリアルの後半でアプリを構成する際に必要になります。

## <a name="create-policies"></a>ポリシーの作成

Azure AD B2C ポリシーでは、ユーザー ワークフローを定義します。 たとえば、サインイン、サインアップ、パスワードの変更、プロファイルの編集は一般的なワークフローです。

### <a name="create-a-sign-up-or-sign-in-policy"></a>サインアップまたはサインイン ポリシーを作成する

Web アプリにアクセスしてサインインするためにユーザーのサインアップを実行するには、**サインアップまたはサインイン ポリシー**を作成します。

1. Azure AD B2C ポータル ページから、**[サインアップまたはサインイン ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    ![サインアップまたはサインイン ポリシーの追加](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SiUpIn | ポリシーの**名前**を入力します。 ポリシー名の先頭には **B2C_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **B2C_1_SiUpIn** を使用します。 | 
    | **ID プロバイダー** | 電子メールのサインアップ | ユーザーを一意に識別するために使用される ID プロバイダー。 |
    | **サインアップ属性** | 表示名、郵便番号 | サインアップ中にユーザーから収集する属性を選択します。 |
    | **アプリケーション要求** | 表示名、郵便番号、新規ユーザー、ユーザーのオブジェクト ID | [アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)に含める[要求](../active-directory/develop/developer-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

### <a name="create-a-profile-editing-policy"></a>プロファイル編集ポリシーを作成する

ユーザーが自身でユーザー プロファイル情報をリセットできるようにするには、**プロファイルの編集ポリシー**を作成します。

1. Azure AD B2C ポータルページから、**[プロファイルの編集ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SiPe | ポリシーの**名前**を入力します。 ポリシー名の先頭には **B2C_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **B2C_1_SiPe** を使用します。 | 
    | **ID プロバイダー** | ローカル アカウント サインイン | ユーザーを一意に識別するために使用される ID プロバイダー。 |
    | **プロファイル属性** | 表示名、郵便番号 | ユーザーがプロファイルの編集中に変更できる属性を選択します。 |
    | **アプリケーション要求** | 表示名、郵便番号、ユーザーのオブジェクト ID | プロファイル編集が成功した後に[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)に含める[要求](../active-directory/develop/developer-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

### <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する

アプリケーションでパスワードのリセットを有効にするには、**パスワードのリセット ポリシー**を作成する必要があります。 このポリシーでは、パスワードのリセット時のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンの内容を記述します。

1. Azure AD B2C ポータル ページから、**[パスワードのリセット ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SSPR | ポリシーの**名前**を入力します。 ポリシー名の先頭には **B2C_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **B2C_1_SSPR** を使用します。 | 
    | **ID プロバイダー** | Reset password using email address (電子メール アドレスを使用してパスワードをリセットする) | これは、ユーザーを一意に識別するために使用される ID プロバイダーです。 |
    | **アプリケーション要求** | ユーザーのオブジェクト ID | パスワードのリセットが成功した後に[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)に含める[要求](../active-directory/develop/developer-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

## <a name="update-single-page-app-code"></a>シングルページ アプリのコードの更新

アプリの登録とポリシーの作成が完了したら、Azure AD B2C テナントを使用するようアプリを構成する必要があります。 このチュートリアルでは、GitHub からダウンロードできるサンプル SPA JavaScript アプリを構成します。 

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)するか、GitHub からサンプル Web アプリを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
このサンプル アプリでは、シングルページ アプリでユーザーのサインアップ、サインイン、保護された Web API の呼び出しに Azure AD B2C をどのように使用できるかを示します。 テナントでアプリ登録を使用し、作成したポリシーを構成するには、アプリを変更する必要があります。 

アプリの設定を変更するには、次の手順に従います。

1. Node.js シングルページ アプリのサンプルの `index.html` ファイルを開きます。
2. Azure AD B2C テナントの登録情報でサンプルを構成します。 次のコード行を変更します。

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    このチュートリアルで使用されているポリシー名は、**B2C_1_SiUpIn** です。 別のポリシー名を使用している場合は、`authority` 値でそのポリシー名を使用してください。

## <a name="run-the-sample"></a>サンプルを実行する

1. Node.js コマンド プロンプトを起動します。
2. Node.js サンプルが含まれているディレクトリに変更します。 例: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`。
3. 次のコマンドを実行します。
    ```
    npm install && npm update
    node server.js
    ```

    コンソール ウィンドウには、アプリがホストされている場所のポート番号が表示されます。
    
    ```
    Listening on port 6420...
    ```

4. ブラウザーを使用してアドレス `http://localhost:6420` に移動し、アプリを表示します。

このサンプル アプリでは、サインアップ、サインイン、プロファイルの編集、パスワードのリセットがサポートされています。 このチュートリアルでは、アプリを使用するためのサインアップをユーザーがメール アドレスを使って行う方法に焦点を当てています。 その他のシナリオについては、ご自身でお試しください。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. **[Login]** をクリックして、SPA アプリのユーザーとしてサインアップします。 これは、前の手順で定義した **B2C_1_SiUpIn** ポリシーを使用します。

2. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、**[今すぐサインアップ]** リンクをクリックします。 

3. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ポリシーで定義されているユーザーのパスワードと要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。 

    ![サインアップ ワークフロー](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. **[作成]** をクリックして、Azure AD B2C テナントにローカル アカウントを作成します。

これで、ユーザーはメール アドレスを使用してサインインし、SPA アプリを使用できるようになりました。

> [!NOTE]
> ログイン後、このアプリでは "アクセス許可が不十分です" というエラーが表示されます。 デモ テナントからリソースにアクセスしようとしているため、このエラーが発生します。 アクセス トークンは Azure AD テナントのみに有効であるため、API 呼び出しは許可されていません。 次のチュートリアルに進み、テナント用に保護された Web API を作成してください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他の Azure AD B2C チュートリアルを試す場合は、Azure AD B2C テナントを使用できます。 不要になったら、[Azure AD B2C テナントを削除する](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)ことができます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure AD B2C テナントを作成し、ポリシーを作成して、Azure AD B2C テナントを使用するようサンプル シングルページ アプリを更新する方法について学習しました。 保護されている Web API の登録、構成、呼び出しをデスクトップ アプリから実行する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> 
