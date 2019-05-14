---
title: 国内クラウドで Microsoft Authentication Library (MSAL) を使用する - Microsoft ID プラットフォーム
description: Microsoft Authentication Library (MSAL) を使用すると、アプリケーション開発者はセキュリティで保護された Web API を呼び出すためにトークンを取得することができます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、サード パーティの Web API、または、独自の Microsoft Graph が使用できます。 MSAL は、複数のアプリケーション アーキテクチャとプラットフォームをサポートします。
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080223"
---
# <a name="use-msal-in-national-cloud-environment"></a>国内クラウド環境で MSAL を使用する

[国内クラウド](authentication-national-cloud.md) は、物理的に分離された Azure のインスタンスです。 Azure のこれらのリージョンは、データの保存場所、主権、およびコンプライアンス要件が地理的な境界内で確実に守られるように設計されています。

Microsoft Authentication Library (MSAL) では、Microsoft の世界的なクラウドに加え、アプリケーション開発者が国内クラウド内で認証してセキュリティで保護された Web API を呼び出すためにトークンを取得することもできます。 これらの Web API には、Microsoft Graph またはその他の Microsoft API を使用できます。

グローバルなクラウドを含む Azure Active Directory (Azure AD) は、次の各国のクラウドにデプロイされています。  

- Azure US Government
- Azure China 21Vianet
- Azure Germany

このガイドでは、職場および学校のアカウントにサインインしてアクセス トークンを取得し、[米国政府機関向け Microsoft Cloud](https://azure.microsoft.com/global-infrastructure/government/) 環境で Microsoft Graph API を呼び出す方法を示します。

## <a name="prerequisites"></a>前提条件

開始する前に、これらの前提条件を満たしていることを確認します。

### <a name="choose-the-appropriate-identities"></a>適切な ID を選択する

[Azure Government](https://docs.microsoft.com/azure/azure-government/) アプリケーションでは、ユーザーの認証に Azure AD Government ID と Azure AD Public ID を使用できます。 これらの ID のいずれかを使用できるため、自分のシナリオにどの機関のエンドポイントを選択すべきかを理解して決定する必要があります。

- Azure AD Public:組織が Office 365 (Public または GCC) または別のアプリケーションをサポートする Azure AD Public テナントを既に持っている場合によく使用されます。
- Azure AD Government:組織が Office 365 (GCC High または DoD) をサポートする Azure AD Government テナントを既に持っている場合、または Azure AD Government で新しいテナントを作成する場合によく使用されます。

決定したら、特別な考慮事項は、アプリの登録をどこで実行するかです。 Azure Government アプリケーションに Azure AD Public ID を選択した場合は、Azure AD Public テナントでアプリケーションを登録する必要があります。

### <a name="get-an-azure-government-subscription"></a>Azure Government サブスクリプションを取得する

- Azure Government サブスクリプションを取得するには、「[Managing and connecting to your subscription in Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)」 (Azure Government でのサブスクリプションの管理と接続) を参照してください。
- Azure Government サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/global-infrastructure/government/request/)を作成してください。

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録

1. アプリケーションを登録するために、[Azure portal](https://portal.azure.us/) にサインインします。
    1. 他の国内クラウドの Azure portal エンドポイントを見つけるには、「[アプリ登録エンドポイント](authentication-national-cloud.md#app-registration-endpoints)」を参照してください。

1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://aka.ms/ra/ff) ページに移動します。
1. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの名前を入力します。
1. **[サポートされているアカウントの種類]** で、**[任意の組織のディレクトリ内のアカウント]** を選択します。
1. **[リダイレクト URI]** セクションで、**Web**プラットフォームを選択し、ご使用の Web サーバーに基づいてアプリケーションの URL に値を設定します。 Visual Studio と Node でリダイレクト URL を設定および取得する方法の手順については、次のセクションを参照してください。
1. 終了したら、**[登録]** を選択します。
1. アプリの **[概要]** ページで、**[Application (client) ID]\(アプリケーション (クライアント) ID\)** の値を書き留めます。
1. このチュートリアルでは、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。 登録済みのアプリケーションの左側のナビゲーション ウィンドウで、**[認証]** を選択します。
1. **[詳細設定]** の **[暗黙的な許可]** で、**[ID トークン]** と **[アクセス トークン]** の両方のチェック ボックスをオンにします。 このアプリではユーザーをサインインし、API を呼び出す必要があるため、ID トークンとアクセス トークンが必要になります。
1. **[保存]** を選択します。

### <a name="step-2--set-up-your-web-server-or-project"></a>手順 2:Web サーバーまたはプロジェクトの設定

- Node などのローカル Web サーバー向けの[プロジェクト ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)する。

  or

- [Visual Studio プロジェクトをダウンロードする](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。

次に、「[JavaScript SPA の構成](#step-4-configure-your-javascript-spa)」に進み、コード サンプルを構成してから実行します。

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>手順 3:ユーザーのサインインに Microsoft Authentication Library (MSAL) を使用する

[Javascript チュートリアル](tutorial-v2-javascript-spa.md#create-your-project)の手順に従ってプロジェクトを作成し、ユーザーがサインインするために Microsoft Authentication Library (MSAL) と統合します。

### <a name="step-4-configure-your-javascript-spa"></a>手順 4:JavaScript SPA の構成

プロジェクトの設定中に作成された `index.html` ファイルで、アプリケーション登録情報を追加します。 `index.html` ファイルの本文の `<script></script>` タグ内で、先頭に次のコードを追加します。

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

各値の説明:

- `Enter_the_Application_Id_here` - 登録したアプリケーションの**アプリケーション (クライアント) ID**。
- `Enter_the_Tenant_Info_Here` - 次のいずれかのオプションに設定します。
    - アプリケーションで**この組織のディレクトリ内のアカウント**がサポートされている場合は、この値を**テナント ID** または**テナント名** (例: contoso.microsoft.com) に置き換えます。
    - アプリケーションで **[任意の組織のディレクトリ内のアカウント]** がサポートされる場合は、この値を `organizations` に置き換えます。
    -  すべての国内クラウドの認証エンドポイントを見つけるには、「[Azure AD 認証エンドポイント](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)」を参照してください。

     > [!NOTE]
     > 個人用 Microsoft アカウント (MSA) のシナリオは、国内クラウドではサポートされません。
  
-   `graphEndpoint` - 米国政府機関向け Microsoft Cloud の Microsoft Graph エンドポイントです。
    -  すべての国内クラウドの Microsoft Graph エンドポイントを見つけるには、[国内クラウド内の Microsoft Graph エンドポイント](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)に関するページを参照してください。

## <a name="net"></a>.NET

MSAL .NET を使用すると、ユーザーにサインインして、トークンを取得し、国内クラウドで Microsoft Graph API を呼び出すことができます。

次のチュートリアルでは、OpenID Connect を使用する .NET Core 2.2 MVC Web アプリをビルドして、国内クラウドに属しているユーザーの組織でのユーザーの '職場や学校' のアカウントを使用して、ユーザーにサインインする方法を示します。

- ユーザーにサインインしてトークンを取得するには、[このチュートリアル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)に従います。
- Microsoft Graph API を呼び出すには、[このチュートリアル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)に従います。

## <a name="next-steps"></a>次の手順

各項目の詳細情報

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)