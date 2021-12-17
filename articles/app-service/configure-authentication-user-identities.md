---
title: AuthN または AuthZ のユーザー ID
description: App Service で組み込みの認証と承認を使用しているときにユーザー ID にアクセスする方法について説明します。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: ae483e4213ec17cfa3a4532fbf615a40c760ad6e
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230434"
---
# <a name="work-with-user-identities-in-azure-app-service-authentication"></a>Azure App Service 認証でユーザー ID を操作する

この記事では、[App Service の組み込みの認証と承認](overview-authentication-authorization.md)を使用しているときにユーザー ID を操作する方法について説明します。 

## <a name="access-user-claims-in-app-code"></a>アプリ コードでユーザー クレームにアクセスする

すべての言語フレームワークにおいて、App Service は、受信トークン (認証されたエンド ユーザーまたはクライアント アプリケーションからなど) 内のクレームを要求ヘッダーに挿入することにより、コードで使用できるようにします。 外部要求ではこれらのヘッダーの設定が許可されないので、これらのヘッダーは App Service によって設定されている場合にのみ存在します。 いくつかのヘッダーの例は次のとおりです。

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

任意の言語またはフレームワークで記述されたコードで、これらのヘッダーから必要な情報を取得できます。 

> [!NOTE]
> これらのヘッダーは、さまざまな言語フレームワークによって異なる形式 (小文字や先頭文字が大文字など) でアプリ コードに提供される可能性があります。

ASP.NET 4.6 アプリの場合、App Service は認証されたユーザーの要求で [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) を設定するので、標準の .NET コード パターン (`[Authorize]` 属性など) に従うことができます。 同様に、PHP アプリの場合、App Service は `_SERVER['REMOTE_USER']` 変数を設定します。 Java アプリの場合、要求には [Tomcat サーブレットからアクセスできます](configure-language-java.md#authenticate-users-easy-auth)。

[Azure Functions](../azure-functions/functions-overview.md) では、.NET コードの `ClaimsPrincipal.Current` は設定されていませんが、要求ヘッダーでユーザーの要求を検索することも、要求コンテキストまたはバインディング パラメーターを使用して `ClaimsPrincipal` オブジェクトを取得することもできます。 詳細については、[Azure Functions でのクライアント ID の操作](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)に関するセクションを参照してください。

.NET Core の場合、[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) では、現在のユーザーへの App Service 認証の設定がサポートされています。 詳細については、[、Microsoft.Identity.Web の Wiki](https://github.com/AzureAD/microsoft-identity-web/wiki/1.2.0#integration-with-azure-app-services-authentication-of-web-apps-running-with-microsoftidentityweb) を参照するか、[Microsoft Graph にアクセスする Web アプリに関するこちらのチュートリアル](./scenario-secure-app-access-microsoft-graph-as-user.md?tabs=command-line#install-client-library-packages)を参照してください。

## <a name="access-user-claims-using-the-api"></a>API を使用してユーザー クレームにアクセスする

アプリで[トークン ストア](overview-authentication-authorization.md#token-store)が有効になっている場合は、`/.auth/me` を呼び出して認証されたユーザーの追加の詳細を取得することもできます。 Mobile Apps サーバー SDK には、このデータを操作するためのヘルパー メソッドが用意されています。 詳細については、「[Azure Mobile Apps Node.js SDK の使用方法](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)」と「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:エンドツーエンドでのユーザーの認証と承認](tutorial-auth-aad.md)