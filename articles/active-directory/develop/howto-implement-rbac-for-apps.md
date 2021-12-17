---
title: アプリでロールベースのアクセス制御を実装する
titleSuffix: Microsoft identity platform
description: アプリケーションにロールベースのアクセス制御を実装する方法について説明します。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/17/2021
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: ccbdbde261914e95470e37b0e2046a95e1fe5898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699198"
---
# <a name="implement-role-based-access-control-in-apps"></a>アプリでロールベースのアクセス制御を実装する

ロールベースのアクセス制御 (RBAC) を使用すると、ユーザーまたはグループは、アクセスできるリソースや、それらのリソースに対して実行できる操作、誰がどのリソースを管理するかに関する特定のアクセス許可を持つことができます。

一般に、リソースを保護するための RBAC の実装について説明するときは、Web アプリケーション、シングルページ アプリケーション (SPA)、または API のいずれかの保護を目指しています。  これは、アプリケーションまたは API 全体か、特定の領域、機能、または API メソッドのいずれかになります。

この記事では、アプリケーションに固有のロールベースのアクセス制御を実装する方法について説明します。  認可の基本の詳細については、「[承認の基本](./authorization-basics.md)」を参照してください。

## <a name="implementing-rbac-using-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームを使用した RBAC の実装

「[アプリケーション開発者向けのロールベースのアクセス制御](./custom-rbac-for-developers.md)」で説明したように、Microsoft ID プラットフォームを使用して RBAC を実装するには、次の 3 つの方法があります。

- **アプリ ロール** - アプリケーション内のロジックと組み合わせて[アプリケーションの登録でアプリ ロール機能](./howto-add-app-roles-in-azure-ad-apps.md#declare-roles-for-an-application)を使用して、受信したアプリ ロールの割り当てを解釈します。
- **グループ** - アプリケーション内のロジックと組み合わせて受信 ID のグループの割り当てを使用して、グループの割り当てを解釈します。 
- **カスタム データ ストア** - アプリケーション内のロジックを使用して、ロールの割り当てを取得して解釈します。

*アプリ ロール* は実装が最も簡単なため、これを使用することをお勧めします。 このアプローチは、Microsoft ID プラットフォームを利用するアプリの構築に使用される SDK によって直接サポートされています。 方法の選択方法の詳細については、「[アプローチの選択](./custom-rbac-for-developers.md#choosing-an-approach)」を参照してください。

この記事の残りの部分では、アプリ ロールを使用してアプリのロールを定義し、アプリケーション内で RBAC を実装する方法について説明します。

## <a name="defining-roles-for-your-application"></a>アプリケーションのロールを定義する

アプリケーションの RBAC を実装するための最初の手順は、アプリケーションで必要なロールを定義し、ユーザーまたはグループをそれらのロールに割り当てることです。  このプロセスの概要は、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](./howto-add-app-roles-in-azure-ad-apps.md)」に記載されています。 ロールを定義し、ユーザーまたはグループを割り当てたら、アプリケーションに送信されるトークン内のロールの割り当てにアクセスし、それに応じてアクションを実行できます。

## <a name="implementing-rbac-in-aspnet-core"></a>ASP.NET Core で RBAC を実装する 

ASP.NET Core では、ASP.NET Core Web アプリケーションまたは Web API に RBAC を追加することがサポートされます。  これで、ASP.NET Core *Authorize* 属性で[ロール チェック](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)を使用して RBAC を簡単に実装できます。 また、[ポリシーベースのロール チェック](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#policy-based-role-checks)に対して ASP.NET Core のサポートを使用することもできます。

### <a name="aspnet-core-mvc-web-application"></a>ASP.NET Core MVC Web アプリケーション 

ASP.NET Core MVC Web アプリケーションに RBAC を実装するのは簡単です。  多くの場合、*Authorize* 属性を使用して、特定のコントローラーまたはコントローラーでのアクションへのアクセスを許可するロールを指定する必要があります。 ASP.NET Core MVC アプリケーションに RBAC を実装するには、次の手順に従います。
1. 上の「*アプリケーションのロールを定義する*」で説明されているように、アプリのロールと割り当てへのアプリの登録を作成します。
1. 次のいずれかの手順を実行します。
    - **dotnet cli** を使用して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。  *SingleOrg* (シングルテナント認証の場合) または *MultiOrg* (マルチテナント認証の場合) のいずれかとともに *--auth* フラグを指定します。アプリの登録からの場合はクライアントで *--client-id* フラグ、Azure AD テナントからの場合はテナントで *--tenant-id* フラグを指定します。
 
      ```bash 
    
      dotnet new mvc --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID>  
    
      ```
      
    - Microsoft.Identity.Web および Microsoft.Identity.Web.UI ライブラリを既存の ASP.NET Core MVC プロジェクトに追加します。
 
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Microsoft.Identity.Web.UI 

      ```

   次に、「[クイック スタート: ASP.NET Core Web アプリに Microsoft サインインを追加する](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true)」で指定されている手順に従って、アプリケーションに認証を追加します。
1. [ロール チェックの追加](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)に関するページで説明されているように、コントローラー アクションに対するロール チェックを追加します。
1. 保護されている MVC ルートのいずれかへのアクセスを試して、アプリケーションをテストします。

### <a name="aspnet-core-web-api"></a>ASP.NET Core Web API

ASP.NET Core Web API で RBA を実装するには多くの場合、*Authorize* 属性を使用して、特定のコントローラーまたはコントローラーでのアクションへのアクセスを許可するロールを指定する必要があります。 ASP.NET Core Web API に RBAC を実装するには、次の手順に従います。
1. 上の「*アプリケーションのロールを定義する*」で説明されているように、アプリのロールと割り当てへのアプリの登録を作成します。
1. 次のいずれかの手順を実行します。
    - **dotnet cli** を使用して、新しい ASP.NET Core MVC Web API プロジェクトを作成します。  *SingleOrg* (シングルテナント認証の場合) または *MultiOrg* (マルチテナント認証の場合) のいずれかとともに *--auth* フラグを指定します。アプリの登録からの場合はクライアントで *--client-id* フラグ、Azure AD テナントからの場合はテナントで *--tenant-id* フラグを指定します。

      ```bash 
    
      dotnet new webapi --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID> 
    
      ```

    - Microsoft.Identity.Web および Swashbuckle.AspNetCore ライブラリを既存の ASP.NET Core Web API プロジェクトに追加します。
      
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Swashbuckle.AspNetCore 

      ```
    
   次に、「[クイック スタート: ASP.NET Core Web アプリに Microsoft サインインを追加する](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true)」で指定されている手順に従って、アプリケーションに認証を追加します。
1. [ロール チェックの追加](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)に関するページで説明されているように、コントローラー アクションに対するロール チェックを追加します。
1. クライアント アプリから API を呼び出します。  エンド ツー エンドのサンプルについては、[.NET Core Web API を呼び出して、アプリ ロールを使用してロールベースのアクセス制御を実装する、Angular シングルページ アプリケーション](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles)に関するページを参照してください。


## <a name="implementing-rbac-in-other-platforms"></a>他のプラットフォームでの RBAC の実装

### <a name="angular-spa-using-msalguard"></a>MsalGuard を使用する Angular SPA
Angular SPA に RBAC を実装するには、アプリケーション内に含まれる Angular ルートへのアクセスを認可するために、[msal-angular](https://www.npmjs.com/package/@azure/msal-angular) を使用する必要があります。  これは、[Angular シングルページ アプリケーションで、Microsoft ID プラットフォームを使用したユーザーのサインインと API の呼び出しを可能にする](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial#chapter-5-control-access-to-your-protected-api-using-app-roles-and-security-groups)に関するサンプルで説明されています。

> [!NOTE]
> クライアント側 RBAC の実装では、認可されていないアプリケーションによる機密性の高いリソースへのアクセスを防ぐために、サーバー側 RBAC と組み合わせる必要があります。

### <a name="nodejs-with-express-application"></a>Express アプリケーションを使用する Node.js
Express アプリケーションを使用して Node.js に RBAC を実装するには、アプリケーション内に含まれる Express ルートへのアクセスを認可するために MSAL を使用する必要があります。  これは、[Microsoft ID プラットフォームを利用して Node.js Web アプリでユーザーのサインインおよび API の呼び出しを可能にする](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial#chapter-4-control-access-to-your-app-using-app-roles-and-security-groups)に関するサンプルで説明されています。

## <a name="next-steps"></a>次のステップ

- 「[Microsoft ID プラットフォームでのアクセス許可と同意](./v2-permissions-and-consent.md)」で詳細を確認します。
- 「[アプリケーション開発者向けのロールベースのアクセス制御](./custom-rbac-for-developers.md)」で詳細を確認します。
