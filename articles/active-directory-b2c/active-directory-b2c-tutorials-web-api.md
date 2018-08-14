---
title: チュートリアル - Azure Active Directory B2C を使用して Web アプリから ASP.NET Web API へのアクセスを許可する | Microsoft Docs
description: Active Directory B2C を使用して ASP.NET Web API を保護し、ASP.NET Web アプリからそれを呼び出す方法に関するチュートリアル
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 01/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 469a3662b5bc4db467dde3285d557ac8bbae368e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39609091"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-from-a-web-app-using-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C を使用して Web アプリから ASP.NET Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory (Azure AD) B2C で保護された Web API リソースを ASP.NET Web アプリから呼び出す方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントに Web API を登録する
> * Web API のスコープを定義および構成する
> * Web API に対するアクセス許可をアプリに付与する
> * Azure AD B2C を使用して Web API を保護するようにサンプル コードを更新する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [ASP.NET Web アプリでユーザー認証に Azure Active Directory B2C を使用するチュートリアル](active-directory-b2c-tutorials-web-app.md)を完了する。
* **ASP.NET および Web 開発**のワークロードと共に、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールする。

## <a name="register-web-api"></a>Web API の登録

Web API リソースは、Azure Active Directory からの[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token)を表す[クライアント アプリケーション](../active-directory/develop/developer-glossary.md#client-application)で[保護されたリソース要求](../active-directory/develop/developer-glossary.md#resource-server)を受け取って処理できるように、テナントに登録しておく必要があります。 登録により、テナント内に[アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/developer-glossary.md#application-object)が確立されます。 

1. Azure AD B2C テナントの全体管理者として、[Azure Portal](https://portal.azure.com/) にログインします。

2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、**[ディレクトリの切り替え]** を選択します。

    ![ディレクトリの切り替え](./media/active-directory-b2c-tutorials-web-api/switch-directories.png)

3. テナントが含まれるディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/active-directory-b2c-tutorials-web-api/select-directory.png)

4. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。 これで、前のチュートリアルで作成したテナントが使用されます。

5. **[アプリケーション]** を選択し、**[追加]** を選択します。

    テナントにサンプル Web API を登録するには、以下の設定を使用します。
    
    ![新しい API の追加](./media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | My Sample Web API | 開発者が Web API を把握できる**名前**を入力します。 |
    | **Web アプリ/Web API を含める** | [はい] | Web API の場合は **[はい]** を選択します。 |
    | **暗黙的フローを許可する** | [はい] | API では [OpenID Connect サインイン](active-directory-b2c-reference-oidc.md)が使用されるため、**[はい]** を選択します。 |
    | **応答 URL** | `https://localhost:44332` | 応答 URL は、API が要求したトークンを Azure AD B2C が返すエンドポイントです。 このチュートリアルでは、サンプル Web API はローカル (localhost) で実行され、ポート 44332 でリッスンします。 |
    | **アプリケーション ID/URI** | myAPISample | この URI は、テナント内の API を一意に識別します。 これにより、テナントごとに複数の API を登録することができます。 [スコープ](../active-directory/develop/developer-glossary.md#scopes)は、保護された API リソースへのアクセスを統制し、App ID URI ごとに定義されます。 |
    | **ネイティブ クライアント** | いいえ  | これはネイティブ クライアントではなく Web API のため、[いいえ] を選択します。 |
    
6. **[作成]** をクリックして API を登録します。

登録された API は、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧から Web API を選択します。 Web API のプロパティ ウィンドウが表示されます。

![Web API のプロパティ](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

**[アプリケーション クライアント ID]** をメモします。 この ID は API を一意に識別するため、このチュートリアルの後半で API を構成する際に必要になります。

Azure AD B2C に Web API を登録すると、信頼関係が定義されます。 API が B2C に登録されるため、API は他のアプリケーションから受け取る B2C アクセス トークンを信頼できるようになりました。

## <a name="define-and-configure-scopes"></a>スコープの定義と構成

[スコープ](../active-directory/develop/developer-glossary.md#scopes)を使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば Web API のユーザーが、読み取りと書き込みの両方のアクセス権限を持つ場合もあれば、読み取りアクセス権限しか持たない場合もあります。 このチュートリアルでは、スコープを使用して、Web API の読み取りアクセス許可と書き込みアクセス許可を定義します。

### <a name="define-scopes-for-the-web-api"></a>Web API のスコープの定義

登録された API は、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧から Web API を選択します。 Web API のプロパティ ウィンドウが表示されます。

**[公開済みスコープ (プレビュー)]** をクリックします。

API のスコープを構成するには、次のエントリを追加します。 

![Web API で定義されているスコープ](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Setting      | 推奨値  | 説明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **スコープ** | Hello.Read | Hello への読み取りアクセス |
| **スコープ** | Hello.Write | Hello への書き込みアクセス |

**[Save]** をクリックします。

公開済みスコープを使用すると、クライアント アプリに Web API へのアクセス許可を付与することができます。

### <a name="grant-app-permissions-to-web-api"></a>Web API へのアクセス許可をアプリに付与する

保護された Web API をアプリから呼び出すには、その API へのアクセス許可をアプリに付与する必要があります。 このチュートリアルでは、[ASP.NET Web アプリでユーザー認証に Azure Active Directory B2C を使用するチュートリアル](active-directory-b2c-tutorials-web-app.md)で作成した Web アプリを使用します。 

1. Azure Portal で、サービスの一覧から **[Azure AD B2C]** を選択し、**[アプリケーション]** をクリックして登録済みアプリの一覧を表示します。

2. アプリの一覧から **[My Sample Web App]** を選択し、**[API アクセス (プレビュー)]**、**[追加]** の順にクリックします。

3. **[API の選択]** ボックスの一覧で、登録した Web API **[My Sample Web API]** を選択します。

4. **[スコープの選択]** ボックスの一覧で、Web API の登録で定義したスコープを選択します。

    ![アプリのスコープの選択](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Click **OK**.

**My Sample Web App** は、保護された **My Sample Web API** を呼び出すために登録されています。 ユーザーは、この Web アプリを使用するために Azure AD B2C で[認証](../active-directory/develop/developer-glossary.md#authentication)を行います。 Web アプリは、保護された Web API にアクセスするために、Azure AD B2C から[承認付与](../active-directory/develop/developer-glossary.md#authorization-grant)を取得します。

## <a name="update-code"></a>コードの更新

Web API が登録され、スコープを定義したら、Azure AD B2C テナントを使用するように Web API コードを構成する必要があります。 このチュートリアルでは、サンプル Web API を構成します。 

サンプル Web API は、前提条件のチュートリアルである [ASP.NET Web アプリでユーザー認証に Azure Active Directory B2C を使用するチュートリアル](active-directory-b2c-tutorials-web-app.md)でダウンロードしたプロジェクトに含まれています。 前提条件のチュートリアルを完了していない場合は、先に進む前にこれを完了してください。

サンプル ソリューションには 2 つのプロジェクトがあります。

**Web アプリのサンプル アプリ (TaskWebApp):** タスク リストを作成および編集するための Web アプリ。 この Web アプリでは、**サインアップまたはサインイン** ポリシーを使用して、メール アドレスでユーザーをサインアップまたはサインインします。

**Web API のサンプル アプリ (TaskService):** タスク リストの作成、読み取り、更新、削除機能をサポートする Web API。 この Web API は Azure AD B2C によって保護されており、Web アプリによって呼び出されます。

サンプルの Web アプリと Web API では、各プロジェクトの Web.config ファイルでアプリの設定として構成値を定義します。

Visual Studio で **B2C-WebAPI-DotNet** ソリューションを開きます。

### <a name="configure-the-web-app"></a>Web アプリを構成する

1. **TaskWebApp** プロジェクトの **Web.config** を開きます。

2. API をローカルで実行するには、**api:TaskServiceUrl** に localhost の設定を使用します。 次のように Web.config を変更します。 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. API の URI を構成します。 これは、Web アプリが API 要求を行うために使用する URI です。 また、要求されたアクセス許可を構成します。

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Web API を構成する

1. **TaskService** プロジェクトの **Web.config** を開きます。

2. テナントを使用するように API を構成します。

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. クライアント ID を、API 用に登録されたアプリケーション ID に設定します。

    ```C#
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. サインアップおよびサインイン ポリシーの作成時に生成された名前で、ポリシーの設定を更新します。

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_SiUpIn" />
    ```

5. ポータルで作成したものと一致するようにスコープの設定を構成します。

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>サンプルを実行する

**TaskWebApp** プロジェクトと **TaskService** プロジェクトの両方を実行する必要があります。 

1. ソリューション エクスプローラーでソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。 
2. **[マルチ スタートアップ プロジェクト]** を選択します。
3. 両方のプロジェクトの **[アクション]** を **[開始]** に変更します。
4. [OK] をクリックして構成を保存します。
5. **F5** キーを押して両方のアプリを実行します。 各アプリケーションは、それぞれ別のブラウザー タブで開かれます。`https://localhost:44316/` は Web アプリです。
    `https://localhost:44332/` は Web API です。

6. Web アプリで、メニュー バーナーのサインアップ/サインイン リンクをクリックして、Web アプリにサインアップします。 [Web アプリのチュートリアル](active-directory-b2c-tutorials-web-app.md)で作成したアカウントを使用します。 
7. サインインしたら、**To Do リスト**のリンクをクリックして To Do リスト アイテムを作成します。

To Do リスト アイテムを作成すると、Web アプリは To Do リスト アイテムを生成するよう Web API に要求します。 保護されている Web アプリは、Azure AD B2C テナントで保護された Web API を呼び出しています。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他の Azure AD B2C チュートリアルを試す場合は、Azure AD B2C テナントを使用できます。 不要になったら、[Azure AD B2C テナントを削除する](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)ことができます。

## <a name="next-steps"></a>次の手順

この記事では、Azure AD B2C でスコープを登録および定義することで ASP.NET Web API を保護する方法について説明しました。 コードのチュートリアルなど、このシナリオの開発に関する詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C のサインアップ、サインイン、プロファイル編集、パスワード リセットを使用する ASP.NET Web アプリケーションの構築](active-directory-b2c-devquickstarts-web-dotnet-susi.md)