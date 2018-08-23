---
title: Azure Active Directory の条件付きアクセスについての開発者ガイド
description: 開発者ガイドと Azure AD の条件付きアクセスのシナリオ
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
editor: PatAltimore
ms.author: celested
ms.reviewer: dadobali
ms.date: 07/19/2017
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: ab6936d62aac5502d70239bacfbfd15bd6b793ab
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142769"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory の条件付きアクセスについての開発者ガイド

Azure Active Directory (Azure AD) の条件付きアクセス機能は、アプリをセキュリティで保護し、サービスを保護するために使用できる方法の 1 つを提供します。 条件付きアクセスを使用することで、開発者や企業のお客様は、次のようなさまざまな方法でサービスを保護できます。

* 多要素認証
* Intune 登録されているデバイスのみに特定のサービスへのアクセスを許可します。
* ユーザーの場所と IP 範囲を制限します。

条件付きアクセスのすべての機能について詳しくは、「[Azure Active Directory の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)」をご覧ください。 

この記事では、Azure AD のアプリを構築している開発者が条件付きアクセスを使用する方法、および条件付きアクセス ポリシーが適用される可能性のある制御不能なリソースにアクセスした場合の影響について説明します。 また、On-Behalf-Of フロー、Web アプリ、Microsoft Graph へのアクセス、API の呼び出しに対し条件付きアクセスが与える影響についても説明します。

[シングル](quickstart-v1-integrate-apps-with-azure-ad.md)および[マルチテナント](howto-convert-app-to-be-multi-tenant.md) アプリおよび[一般的な認証パターン](authentication-scenarios.md)の知識を持っているユーザーを対象とします。

## <a name="how-does-conditional-access-impact-an-app"></a>条件付きアクセスがアプリに与える影響

### <a name="app-types-impacted"></a>アプリの種類が影響を受ける

最も一般的なケースは、条件付きアクセスによってアプリの動作が変更されない、または開発者からの変更を必要としない場合です。 アプリが間接的、またはサイレントでサービスのトークンを要求する特定の場合のみ、アプリが条件付きアクセス "チャレンジ" を処理するためにコードを変更する必要があります。 これは、対話型のサインインを要求するだけで実行できる場合があります。 

具体的には、次のシナリオでは、条件付きアクセス "問題" に対応するためコードが必要になります。 

* Microsoft Graph にアクセスするアプリ
* On-Behalf-Of フローを実行するアプリ
* 複数のサービスとリソースにアクセスするアプリ
* ADAL.js を使用するシングル ページ アプリケーション
* リソースを呼び出す Web Apps

条件付きアクセス ポリシーは、アプリに適用できますが、アプリがアクセスする Web API にも適用できます。 条件付きアクセス ポリシーの構成方法について詳しくは、「[クイック スタート: Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](../conditional-access/app-based-mfa.md)」をご覧ください。

シナリオによっては、企業のお客様は、条件付きアクセス ポリシーをいつでも適用/削除できます。 新しいポリシーが適用されたときにアプリの機能を継続するためには、"チャレンジ" 処理を実装する必要があります。 チャレンジ処理の例は次のとおりです。 

### <a name="conditional-access-examples"></a>条件付きアクセスの例

シナリオによっては、条件付きアクセスを処理するためにコードの変更が必要なものと、不要なものがあります。 これは、それぞれの違いをわかりやすく説明する、条件付きアクセスを使用した多要素認証のシナリオです。

* シングル テナントの iOS アプリを構築して、条件付きアクセス ポリシーを適用するとします。 アプリがユーザーのサインインを処理し、API へのアクセスは要求されません。 ユーザーがサインインすると、ポリシーが自動的に呼び出され、ユーザーは、多要素認証 (MFA) を実行する必要があります。 
* Microsoft Graph を使用して Exchange などのサービスにアクセスするマルチテナント Web アプリを作成するとします。 このアプリを採用する企業のお客様は、Exchange のポリシーを設定します。 Web アプリが MS Graph のトークンを要求するとき、そのアプリはポリシーへの準拠が求められません。 エンドユーザーは有効なトークンでサインインされます。 アプリが Microsoft Graph に対してこのトークンを使用して、Exchange データにアクセスしようとすると、```WWW-Authenticate``` ヘッダーで Web アプリに要求 "チャレンジ" が返されます。 アプリは新しい要求で ```claims``` を使用でき、エンドユーザーは条件に準拠するよう求められます。 
* 中間層サービスを使用して、ダウンストリーム API にアクセスするネイティブ アプリを作成するとします。 このアプリを使用する会社のお客様は、ダウンストリーム API にポリシーを適用します。 エンドユーザーがサインインすると、ネイティブ アプリケーションは中間層へのアクセスを要求し、トークンを送信します。 中間層では On-Behalf-Of フローが実行され、ダウンストリーム API へのアクセスが要求されます。 この時点では、"チャレンジ" 要求は、中間層に提示されます。 中間層では、条件付きアクセス ポリシーに準拠する必要があるネイティブのアプリケーションにチャレンジを送信します。

### <a name="complying-with-a-conditional-access-policy"></a>条件付きアクセス ポリシーへの準拠

その他のアプリケーション トポロジでは、セッションが確立されたときに条件付きアクセス ポリシーが評価されます。 条件付きアクセス ポリシーは、アプリやサービスの粒度に従って動作するため、実行しようとしているシナリオによって呼び出される点が大きく異なります。

アプリが条件付きアクセス ポリシーを使用してサービスにアクセスしようとすると、条件付きアクセスのチャレンジが発生する可能性があります。 このチャレンジは、Azure AD または Microsoft Graph からの応答に含まれる `claims` パラメーターにエンコードされています。 このチャレンジ パラメーターの例を次に示します。 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

開発者は、このチャレンジを取得して、Azure AD への新しい要求に追加できます。 この状態を渡すと、条件付きアクセス ポリシーに準拠するために必要な操作を実行するよう、エンドユーザーに対して求められます。 次のシナリオでは、エラーおよびパラメーターを抽出する方法について説明します。 

## <a name="scenarios"></a>シナリオ

### <a name="prerequisites"></a>前提条件

Azure AD の条件付きアクセスは、[Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis#choose-an-edition) に含まれている機能です。 ライセンス要件の詳細については、[ライセンスのないユーザーのレポート](../active-directory-conditional-access-unlicensed-usage-report.md)に関するページを参照してください。 開発者は、Azure AD Premium を含む Enterprise Mobility Suite に無料でサブスクリプションできる [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx) に参加できます。

### <a name="considerations-for-specific-scenarios"></a>特定のシナリオの考慮事項

次の情報は、これらの条件付きアクセス シナリオでのみ適用されます。

* Microsoft Graph にアクセスするアプリ
* On-Behalf-Of フローを実行するアプリ
* 複数のサービスとリソースにアクセスするアプリ
* ADAL.js を使用するシングル ページ アプリケーション

以下のセクションでは、より複雑な一般的なシナリオについて説明します。 基本的な運用原則では、条件付きアクセス ポリシーは、Microsoft Graph を通してアクセスされた場合を除いて、条件付きアクセス ポリシーが適用されるサービスのトークンが要求されたときに評価されます。

## <a name="scenario-app-accessing-microsoft-graph"></a>シナリオ: Microsoft Graph にアクセスするアプリ

このシナリオでは、Web アプリが Microsoft Graph へのアクセスを要求する方法について説明します。 この場合、条件付きアクセス ポリシーは、SharePoint、Exchange、または Microsoft Graph を通じてワークロードとしてアクセスされるその他のサービスに割り当てられます。 この例では、Sharepoint Online に条件付きアクセス ポリシーがあると仮定します。

![Microsoft Graph にアクセスするアプリのフロー ダイアグラム](./media/conditional-access-dev-guide/app-accessing-microsoft-graph-scenario.png)

アプリは、まず条件付きアクセスを使用せず、ダウンストリーム ワークロードにアクセスする必要がある Microsoft Graph に承認を要求します。 ポリシーが呼び出されることなく要求は成功し、アプリは Microsoft Graph のトークンを受信します。 この時点では、アプリは、要求されたエンドポイントに対して、ベアラー要求でアクセス トークンを使用できます。 ここで、アプリは、たとえば Microsoft Graph の Sharepoint Online のエンドポイントにアクセスする必要があります。`https://graph.microsoft.com/v1.0/me/mySite`

アプリは Microsoft Graph に有効なトークンを既に取得しているため、新しいトークンが発行されなくても新しい要求を実行できます。 この要求は失敗し、```WWW-Authenticate``` チャレンジ HTTP 403 アクセス禁止の形式で 要求チャレンジが Microsoft Graph から発行されます。

応答の例を次に示します。 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

要求チャレンジは ```WWW-Authenticate``` ヘッダーに含まれ、次の要求の要求パラメーターを抽出するために解析できます。 要求チャレンジを新しい要求に追加すると、Azure AD でユーザーがサインインしたときに条件付きアクセス ポリシーが評価され、アプリは条件付きアクセス ポリシーに準拠します。 Sharepoint Online のエンドポイントに同じ要求を繰り返しても成功します。

```WWW-Authenticate``` ヘッダーは一意の構造体を持つため、解析して、値を抽出するのは簡単ではありません。 次の方法は簡単で役に立ちます。

```csharp
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
```

要求チャレンジを処理するコード サンプルについては、ADAL .NET 用 [On-Behalf-Of コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)を参照してください。

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>シナリオ: On-Behalf-Of フローを実行するアプリ

このシナリオでは、ネイティブ アプリが Web サービス/API を呼び出す場合について説明します。 呼び出されたサービスは、[On-Behalf-Of フロー](authentication-scenarios.md#application-types-and-scenarios)でダウンストリーム サービスを呼び出します。 ここでは、ダウンストリーム サービス (Web API 2) に、条件付きアクセス ポリシーを適用し、サーバー/デーモン アプリケーションではなく、ネイティブ アプリケーションを使用しています。 

![On-Behalf-Of フローを実行するアプリのフロー ダイアグラム](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1 の最初のトークン要求では、Web API 1 がダウンストリーム API にアクセスしない場合もあるため、エンドユーザーに多要素認証は求められません。 Web API 1 で Web API 2 のユーザーの On-Behalf-Of トークンが要求されると、ユーザーは多要素認証されていないために、要求が失敗します。

Azure AD は、いくつかの興味深いデータを HTTP 応答で返します。 

> [!NOTE]
> この場合は、多要素認証エラーの説明ですが、条件付きアクセスに関連するさまざまな `interaction_required` である可能性があります。 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Web API 1 では、エラー `error=interaction_required` をキャッチし、`claims` チャレンジをデスクトップ アプリケーションに返送します。 この時点では、デスクトップ アプリケーションは新しい `acquireToken()` 呼び出しを行い、追加のクエリ文字列パラメーターとして `claims` チャレンジを追加できます。 この新しい要求では、ユーザーは多要素認証を実行し、この新しいトークンを Web API 1 に送信し、On-Behalf-Of フローを完了する必要があります。

このシナリオを試すには、[.NET コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)を参照してください。 これは、Web API 1 から返された要求チャレンジをネイティブ アプリケーションに渡し、クライアント アプリケーション内で新しい要求を作成する方法を示しています。 

## <a name="scenario-app-accessing-multiple-services"></a>シナリオ: 複数のサービスにアクセスするアプリ

このシナリオでは、Web アプリが、1 つに条件付きアクセス ポリシーが割り当てられている 2 つのサービスにアクセスする場合について説明します。 アプリケーション ロジックによっては、Web アプリが両方の Web サービスにアクセスする必要のないパスが存在する場合があります。 このシナリオでは、トークンを要求する順序が、エンド ユーザー エクスペリエンスに重要な役割を果たします。

A と B の Web サービスがあり、Web サービス B に条件付きアクセス ポリシーが適用されているとします。 最初の対話型の認証要求では、両方のサービスの同意が必要ですが、条件付きアクセス ポリシーは必要ない場合もあります。 アプリが Web サービス B のトークンを要求すると、ポリシーが呼び出され、Web サービス A に対する後続の要求も成功します。

![複数のサービスにアクセスするアプリのフロー ダイアグラム](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

また、最初にアプリが Web サービス A のトークンを要求している場合、エンドユーザーは条件付きアクセス ポリシーを呼び出しません。 これにより、アプリケーション開発者は、エンド ユーザー エクスペリエンスを制御しながらも、条件付きアクセス ポリシーを常に強制的に呼び出す必要がなくなります。 アプリが後で Web サービス B のトークンを要求すると状況は少し複雑になります。この時点で、エンドユーザーは、条件付きアクセス ポリシーに準拠する必要があります。 アプリが `acquireToken` しようとすると、次のエラー (次の図参照) が発生する可能性があります。 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![新しいトークンを要求する複数のサービスにアクセスするアプリ](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

アプリが ADAL ライブラリを使用しており、トークンの取得に失敗した場合、常に対話形式で再試行されます。 この対話型の要求が発生すると、エンドユーザーには、条件付きアクセスに準拠する機会が与えられます。 これは、要求が `AcquireTokenSilentAsync` または `PromptBehavior.Never` でない限り該当し、この場合、アプリは対話型の ```AcquireToken``` 要求を実行し、エンドユーザーはポリシーに準拠する機会が与えられます。 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>シナリオ: シングル ページ アプリケーション (SPA) ADAL.js を使用する

このシナリオでは、ADAL.js を使用して条件付きアクセスで保護されている Web API を呼び出すシングル ページ アプリケーション (SPA) について説明します。 これは、単純なアーキテクチャですが、条件付きアクセスを開発するときに考慮すべき点がいくつかあります。

ADAL.js では、`login()`、`acquireToken(...)`、`acquireTokenPopup(…)`、および `acquireTokenRedirect(…)` トークンを取得する関数があります。 

* `login()` は対話型サイン イン要求を通じて ID トークンを取得しますが、(条件付きアクセスで保護されている Web API を含む) サービスへのアクセス トークンは取得しません。 
* その後、アクセス トークンのサイレント取得に `acquireToken(…)` が使用されます。この場合、どのような状況でも UI は表示されません。 
* `acquireTokenPopup(…)` と `acquireTokenRedirect(…)` の両方を対話形式でリソースのトークンを要求するために使用され、この場合、常にサインイン UI が表示されます。

Web API を呼び出すためにアクセス トークンが必要な場合は、アプリは `acquireToken(…)` を試行します。 トークンのセッションが期限切れか、あるいは条件付きアクセス ポリシーに準拠する必要がある場合は、*acquireToken* 関数が失敗してアプリは `acquireTokenPopup()` または `acquireTokenRedirect()` を使用します。

![ADAL を使用するシングル ページ アプリケーションのフロー ダイアグラム](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

条件付きアクセスのシナリオで紹介した例を見てみましょう。 エンドユーザーがサイトに到着し、セッションは開始されていません。 `login()` を呼び出し、多要素認証なしで ID トークンを取得します。 ユーザーがボタンを押し、これにより、アプリは Web API からデータを要求する必要があります。 アプリは `acquireToken()` の呼び出しを試行しますが、ユーザーがまだ多要素認証を実行しておらず、条件付きアクセス ポリシーに準拠する必要があるため、失敗します。

Azure AD は、次の HTTP 応答を返信します。 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

アプリは `error=interaction_required` をキャッチする必要があります。 アプリは、同じソースの `acquireTokenPopup()` または `acquireTokenRedirect()` を使用できます。 ユーザーは多要素認証の実行を求められます。 ユーザーが多要素認証を完了すると、アプリに、要求されたリソースの新しいアクセス トークンが発行されます。

このシナリオを試すには、[JS SPA On-Behalf- コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)を参照してください。 このコード サンプルでは、条件付きアクセス ポリシーと、このシナリオを説明するために、上記で JS SPA に登録された Web API が使用されます。 クレーム チャレンジを正しく処理し、Web API で使用できるアクセス トークンを取得する方法を示します。 または、Angular SPA については、一般的な [Angular.js コード サンプル](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)を参照してください。


## <a name="see-also"></a>関連項目

* 機能について詳しくは、「[Azure Active Directory の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)」をご覧ください。
* Azure AD コード サンプルについては、[Github リポジトリのコード サンプル](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)を参照してください。 
* ADAL SDK の詳細情報およびリファレンス ドキュメントにアクセスするには、[ライブラリ ガイド](active-directory-authentication-libraries.md)を参照してください。
* マルチテナント シナリオの詳細については、[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法](howto-convert-app-to-be-multi-tenant.md)を参照してください。
