---
title: チュートリアル - Azure Active Directory B2C を使用してデスクトップ アプリから Node.js Web API へのアクセスを許可する | Microsoft Docs
description: Active Directory B2C を使用して Node.js Web API を保護し、.NET デスクトップ アプリからそれを呼び出す方法に関するチュートリアル。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 98c86f5613116dce5423aa9ca6a2ff43e5414592
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594782"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C を使用してデスクトップ アプリから Node.js Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory (Azure AD) B2C で保護された Node.js Web API リソースを Windows Presentation Foundation (WPF) デスクトップ アプリから呼び出す方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントに Web API を登録する
> * Web API のスコープを定義および構成する
> * Web API に対するアクセス許可をアプリに付与する
> * Azure AD B2C を使用して Web API を保護するようにサンプル コードを更新する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [デスクトップ アプリにおける Azure Active Directory B2C を使用したユーザーの認証に関するチュートリアル](active-directory-b2c-tutorials-desktop-app.md)を完了する。
* **.NET デスクトップ開発**と **ASP.NET および Web 開発**のワークロードと共に、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールする。
* [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Web API の登録

Web API リソースは、Azure Active Directory からの[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)を表す[クライアント アプリケーション](../active-directory/develop/developer-glossary.md#client-application)で[保護されたリソース要求](../active-directory/develop/developer-glossary.md#resource-server)を受け取って処理できるように、テナントに登録しておく必要があります。 登録により、テナント内に[アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/developer-glossary.md#application-object)が確立されます。 

Azure AD B2C テナントの全体管理者として、[Azure Portal](https://portal.azure.com/) にログインします。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal のサービス一覧から **[Azure AD B2C]** を選択します。

2. B2C の設定で、**[アプリケーション]** をクリックし、**[追加]** をクリックします。

    テナントにサンプル Web API を登録するには、以下の設定を使用します。
    
    ![新しい API の追加](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | My sample Node.js web API | 開発者が Web API を把握できる**名前**を入力します。 |
    | **Web アプリ/Web API を含める** | [はい] | Web API の場合は **[はい]** を選択します。 |
    | **暗黙的フローを許可する** | [はい] | API では [OpenID Connect サインイン](active-directory-b2c-reference-oidc.md)が使用されるため、**[はい]** を選択します。 |
    | **応答 URL** | `http://localhost:5000` | 応答 URL は、API が要求したトークンを Azure AD B2C が返すエンドポイントです。 このチュートリアルでは、サンプル Web API はローカル (localhost) で実行され、ポート 5000 でリッスンします。 |
    | **アプリケーション ID/URI** | demoapi | この URI は、テナント内の API を一意に識別します。 これにより、テナントごとに複数の API を登録することができます。 [スコープ](../active-directory/develop/developer-glossary.md#scopes)は、保護された API リソースへのアクセスを統制し、App ID URI ごとに定義されます。 |
    | **ネイティブ クライアント** | いいえ  | これはネイティブ クライアントではなく Web API のため、[いいえ] を選択します。 |
    
3. **[作成]** をクリックして API を登録します。

登録された API は、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧から Web API を選択します。 Web API のプロパティ ウィンドウが表示されます。

![Web API のプロパティ](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

**[アプリケーション クライアント ID]** をメモします。 この ID は API を一意に識別するため、このチュートリアルの後半で API を構成する際に必要になります。

Azure AD B2C に Web API を登録すると、信頼関係が定義されます。 API が B2C に登録されるため、API は他のアプリケーションから受け取る B2C アクセス トークンを信頼できるようになりました。

## <a name="define-and-configure-scopes"></a>スコープの定義と構成

[スコープ](../active-directory/develop/developer-glossary.md#scopes)を使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば、読み取りアクセスと書き込みアクセス両方を持つユーザーもいれば、読み取り専用アクセス許可を持つユーザーもいます。 このチュートリアルでは、Web API の読み取りおよび書き込みアクセス許可を定義します。

### <a name="define-scopes-for-the-web-api"></a>Web API のスコープの定義

登録された API は、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧から Web API を選択します。 Web API のプロパティ ウィンドウが表示されます。

**[公開済みスコープ (プレビュー)]** をクリックします。

API のスコープを構成するには、次のエントリを追加します。 

![Web API で定義されているスコープ](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Setting      | 推奨値  | 説明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **スコープ** | demo.read | デモ API への読み取りアクセス|

**[Save]** をクリックします。

公開済みスコープを使用すると、クライアント アプリに Web API へのアクセス許可を付与することができます。

### <a name="grant-app-permissions-to-web-api"></a>Web API へのアクセス許可をアプリに付与する

保護された Web API をアプリから呼び出すには、その API へのアクセス許可をアプリに付与する必要があります。 このチュートリアルでは、[デスクトップ アプリにおける Azure Active Directory B2C を使用したユーザーの認証に関するチュートリアル](active-directory-b2c-tutorials-desktop-app.md)で作成されたデスクトップ アプリを使用します。

1. Azure Portal で、サービスの一覧から **[Azure AD B2C]** を選択し、**[アプリケーション]** をクリックして登録済みアプリの一覧を表示します。

2. アプリの一覧から **[My Sample WPF App]** を選択し、**[API アクセス (プレビュー)]**、**[追加]** の順にクリックします。

3. **[API の選択]** ボックスの一覧で、登録した Web API **[My sample Node.js web API]** を選択します。

4. **[スコープの選択]** ボックスの一覧で、Web API の登録で定義したスコープを選択します。

    ![アプリのスコープの選択](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Click **OK**.

**My Sample WPF App** は、保護された **My sample Node.js web API** を呼び出すために登録されています。 ユーザーは、この WPF デスクトップ アプリを使用するために Azure AD B2C で[認証](../active-directory/develop/developer-glossary.md#authentication)を行います。 このデスクトップ アプリは、保護された Web API にアクセスするために、Azure AD B2C から[承認付与](../active-directory/develop/developer-glossary.md#authorization-grant)を取得します。

## <a name="update-web-api-code"></a>Web API コードの更新

Web API が登録され、スコープを定義したら、Azure AD B2C テナントを使用するように Web API コードを構成する必要があります。 このチュートリアルでは、GitHub からダウンロードできるサンプル Node.js Web アプリを構成します。 

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)するか、GitHub からサンプル Web アプリを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Node.js Web API サンプルでは、Passport.js ライブラリを使用して、Azure AD B2C で API 呼び出しを保護できるようにします。 

### <a name="configure-the-web-api"></a>Web API を構成する

1. Node.js Web API サンプルの `index.html` ファイルを開きます。
2. Azure AD B2C テナントの登録情報でサンプルを構成します。 次のコード行を変更します。

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>デスクトップ アプリを構成する

1. Visual Studio で、[デスクトップ アプリにおける Azure Active Directory B2C を使用したユーザーの認証に関するチュートリアル](active-directory-b2c-tutorials-desktop-app.md)の `active-directory-b2c-wpf` ソリューションを開きます。

## <a name="run-the-sample"></a>サンプルを実行する

Node.js Web API を実行します。

1. Node.js コマンド プロンプトを起動します。
2. Node.js サンプルが含まれているディレクトリに変更します。 例: `cd c:\active-directory-b2c-javascript-nodejs-webapi`。
3. 次のコマンドを実行します。
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
デスクトップ アプリを実行します。

1. **F5** キーを押してデスクトップ アプリを実行します。
2. [デスクトップ アプリにおける Azure Active Directory B2C を使用したユーザーの認証に関するチュートリアル](active-directory-b2c-tutorials-desktop-app.md)で使用したメール アドレスとパスワードでサインインします。
3. **[Call API]** ボタンをクリックします。 

デスクトップ アプリでは、Web API に要求を送信し、ログインしたユーザーの表示名を含む応答を取得します。 保護されているデスクトップ アプリは、Azure AD B2C テナント内の保護された Web API を呼び出しています。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他の Azure AD B2C チュートリアルを試す場合は、Azure AD B2C テナントを使用できます。 不要になったら、[Azure AD B2C テナントを削除する](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)ことができます。

## <a name="next-steps"></a>次の手順

この記事では、Azure AD B2C でスコープを登録および定義することで ASP.NET Web API を保護する方法について説明しました。 詳細については、利用可能な Azure AD B2C コード サンプルを参照してください。

> [!div class="nextstepaction"]
> [Azure AD B2C のコード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
