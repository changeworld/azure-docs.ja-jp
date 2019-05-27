---
title: Microsoft Authentication Library (MSAL) を使用して Azure AD B2C と統合する方法を学習する
description: Microsoft Authentication Library (MSAL) を使用すると、アプリケーション開発者は、Azure AD B2C と統合し、セキュリティで保護された Web API を呼び出すためにトークンを取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、サード パーティの Web API、または、独自の Web API があります。
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6af78a593fb8fadb836d2dc4c02115d95a7f2712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546032"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Microsoft Authentication Library (MSAL) と Azure Active Directory B2C を統合する

Microsoft Authentication Library (MSAL) を使用すると、アプリケーション開発者は、[Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/) を使用してソーシャル ID およびローカル ID でユーザーを認証できます。 Azure Active Directory (Azure AD) B2C は、アプリケーション使用時の顧客のサインアップ、サインイン、プロファイル管理をカスタマイズして制御できる ID 管理サービスです。

Azure Active Directory (Azure AD) B2C を使用すると、アプリケーションのユーザー インターフェイス (UI) をカスタマイズしたり、ブランド名を付けて、一貫性のある操作性を顧客に与えることもできます。

このチュートリアルでは、Microsoft Authentication Library (MSAL) を使用して、Azure Active Directory (Azure AD) B2C と統合する方法について説明します。


## <a name="prerequisites"></a>前提条件

独自の [Azure AD B2C テナント](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)をまだ作成していない場合、すぐに作成してください。 既存の Azure AD B2C テナントを使用できます。 

## <a name="javascript"></a>JavaScript

以下の手順では、シングルページ アプリケーションでユーザーのサインアップ、サインイン、保護された Web API の呼び出しに Azure AD B2C を使用する方法が示されています。

### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録

認証を実装するには、まず、アプリケーションを登録する必要があります。 アプリを登録するには、詳細な手順について[アプリケーションの登録](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c)に従います。

### <a name="steps-2-download-applications"></a>手順 2:アプリケーションのダウンロード

ZIP ファイルをダウンロードするか、GitHub からサンプルを複製します。
>git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>手順 3:Authentication

1. サンプルの index.html ファイルを開きます。

2. アプリケーションの登録中に前に記録したアプリケーション ID とキーを使用して、サンプルを構成します。 次のコード行を変更し、値を実際のディレクトリと API の名前に置き換えます。

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

このチュートリアルで使用されている[ユーザー フロー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)の名前は、B2C_1_signupsignin1 です。 別のユーザー フロー名を使用している場合は、機関の値にそのユーザー フロー名を使用します。


### <a name="configure-application-to-use-b2clogincom"></a>`b2clogin.com` を使用するようにアプリケーションを構成する

Azure Active Directory (Azure AD) B2C アプリケーションへのサインアップおよびサインイン用に ID プロバイダーを設定する場合は、リダイレクト URL として `login.microsoftonline.com` の代わりに `b2clogin.com` を使用できます。

**`b2clogin.com`** つまり、 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` は次のために使用されます。

- Microsoft サービスによって Cookie ヘッダーで使用される領域が減ります。
- お使いの URL に、Microsoft への参照が含まれなくなりました。 たとえば、Azure AD B2C アプリケーションはおそらく login.microsoftonline.com を参照します


 'b2clogin.com' を使用するには、アプリケーションの構成を更新する必要があります。  

1. ValidateAuthority を更新します。**ValidateAuthority** プロパティを `false` に設定します。 **ValidateAuthority** が false に設定されると、b2clogin.com へのリダイレクトが許可されます。

次の例は、このプロパティを設定する方法を示しています。
```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> お使いの Azure AD B2C アプリケーションでは、おそらく、ユーザー フロー参照やトークン エンドポイントなどのいくつかの場所で login.microsoftonline.com が参照されています。 承認エンドポイント、トークン エンドポイント、および発行者が、your-tenant-name.b2clogin.com を使用するように更新されていることを確認してください。

JavaScript 用 Microsoft 認証ライブラリ プレビュー (msal.js) を使用してアクセス トークンを取得し、Azure AD B2C によってセキュリティ保護された API を呼び出す方法については、この [MSAL JS のサンプル](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c)に従ってください。

## <a name="next-steps"></a>次の手順

各項目の詳細情報

- [カスタム ポリシー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [ユーザー インターフェイスのカスタマイズ](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)