---
title: Azure AD 条件付きアクセス認証コンテキストの開発者ガイド
description: Azure AD 条件付きアクセスの認証コンテキストの開発者ガイドとシナリオ
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: CelesteDG
ms.reviewer: kkrishna
ms.workload: identity
ms.custom: aaddev
ms.openlocfilehash: 48e9598acac2739b7663fcd9a46006208d44783c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459503"
---
# <a name="developers-guide-to-conditional-access-authentication-context"></a>条件付きアクセスの認証コンテキストの開発者ガイド

[条件付きアクセス](../conditional-access/overview.md)は、あらゆるアプリ、つまり新旧、プライベート、パブリック、オンプレミス、マルチクラウドのアプリへのアクセスに対してポリシーを適用できるようにするゼロ トラスト コントロール プレーンです。 [条件付きアクセス認証コンテキスト](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)を使用して、それらのアプリ内でさまざまなポリシーを適用できます。 

条件付きアクセス認証コンテキスト (認証コンテキスト) を使用すると、単にアプリ レベルだけではなく、機密データとアクションに詳細なポリシーを適用できます。 ユーザーの摩擦をできるだけ少なくし、ユーザーの生産性とリソースの安全性を向上させながら、最小限の特権アクセスのためにゼロ トラスト ポリシーを調整できます。 現在、これは、[OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html) を使用するアプリケーションで利用でき、高価値のトランザクションや従業員の個人データの表示など、機密性の高いリソースを保護するために会社で開発された認証用に使用されます。

Azure AD 条件付きアクセス エンジンの新しい認証コンテキスト機能を使用して、アプリケーションとサービス内からステップアップ認証の要求をトリガーします。 開発者は、アプリケーション内からのエンド ユーザーによる MFA など、高度でより強力な認証を選択的に要求することができるようになりました。 この機能により、開発者はアプリケーションのほとんどの部分でよりスムーズなユーザー エクスペリエンスを構築できるようになると同時に、より強力な認証制御に基づいて、より安全な操作やデータへのアクセスが保持されます。

## <a name="problem-statement"></a>問題の説明

IT 管理者と規制機関は、認証の追加要素をユーザーに頻繁に求めすぎることと、アプリケーションやサービスに機密性の高いデータと操作が含まれる場合に、それらに対する適切なセキュリティとポリシーの準拠を実現することの間のバランスを取ることで悩むことが多くあります。 これは、ほとんどのデータやアクションへのアクセス時にユーザーの生産性に影響を与える強力なポリシーか、機密性の高いリソースに対して十分ではないポリシーかのいずれかの選択になる場合があります。 

では、アプリが両方を兼ね備えている場合はどうでしょうか。つまり、大半のユーザーと操作に対してセキュリティの強度が比較的低く、プロンプトが出される頻度も低い状態で機能する一方、ユーザーが機密性の高い部分にアクセスしたときにセキュリティ要件を条件付きでステップアップできるような場合です。 

## <a name="common-scenarios"></a>一般的なシナリオ

たとえば、ユーザーが多要素認証を使用して SharePoint にサインインできるときに、機密性の高いドキュメントを含む SharePoint のサイト コレクションへのアクセスには、準拠しているデバイスを使用する必要があり、かつ信頼できる IP 範囲からしかアクセスできないようにすることができます。 

## <a name="steps"></a>手順

以下に、条件付きアクセス認証コンテキストを使用する場合の前提条件と手順を示します。 

### <a name="prerequisites"></a>前提条件

**最初に**、認証と許可に [OpenID Connect](v2-protocols-oidc.md)/ [OAuth 2.0](v2-oauth2-auth-code-flow.md) プロトコルを使用して、アプリを Microsoft ID プラットフォームと統合する必要があります。 [Microsoft ID プラットフォームの認証ライブラリ](reference-v2-libraries.md)を使用してアプリケーションを統合し、Azure Active Directory　でセキュリティ保護することが推奨されます。 アプリと Microsoft ID プラットフォームの統合方法の学習は、[Microsoft ID プラットフォームのドキュメント](index.yml)をお読みになることから始めることをお勧めします。 条件付きアクセス認証コンテキスト機能のサポートは、業界標準の [OpenID Connect](v2-protocols-oidc.md) プロトコルによって提供されるプロトコル拡張機能に基づいて構築されています。 開発者は、[条件付きアクセス認証コンテキスト参照](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)の **値** と [クレーム要求](claims-challenge.md)パラメーターを使用して、アプリがポリシーをトリガーして満たすことができるようにします。

**2 番目に**、[条件付きアクセス](../conditional-access/overview.md)では、Azure AD Premium P1 ライセンスが必要になります。 ライセンスの詳細については、[Azure AD の価格に関するページ](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)を参照してください。

**3 番目に**、現時点では、ユーザーがサインインするアプリケーションでのみ使用できます。 それ自体として認証されるアプリケーションはサポートされていません。 Microsoft ID プラットフォームでサポートされている認証アプリの種類とフローについては、「[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)」ガイドを参照してください。

### <a name="integration-steps"></a>統合手順

サポートされている認証プロトコルを使用してアプリケーションを統合し、条件付きアクセス機能を使用できる Azure AD テナントに登録したら、ユーザーがサインインするアプリケーションにこの機能を統合するプロセスを開始できます。

> [!NOTE]
> この機能の詳細なチュートリアルは、[Use Conditional Access Auth Context in your app for step\-up authentication](https://www.youtube.com/watch?v=_iO7CfoktTY) の録画されたセッションでも参照できます。

**最初に**、認証コンテキストを宣言し、テナントで使用できるようにします。 詳しくは、[認証コンテキストの構成](../conditional-access/concept-conditional-access-cloud-apps.md#configure-authentication-contexts)に関する記事をご覧ください。

テナントの **認証コンテキスト ID** として値 **C1 から C25** を使用できます。 認証コンテキストの例を次に示します。

- **C1** - 強力な認証が必要
- **C2** – 準拠しているデバイスが必須
- **C3** – 信頼できる場所が必要

条件付きアクセス認証コンテキストを使用する条件付きアクセス ポリシーを作成または変更します。 ポリシーの例を次に示します。

- この Web アプリケーションにサインインするすべてのユーザーは、認証コンテキスト ID **C1** の 2FA を正常に完了している必要があります。
- この Web アプリケーションにサインインするすべてのユーザーは、2FA を正常に完了し、さらに、認証コンテキスト ID **C3** の特定の IP アドレス範囲から Web アプリにアクセスする必要があります。

> [!NOTE]
> 条件付きアクセス認証コンテキストの値は、アプリケーションとは別に宣言および管理されます。 アプリケーションが認証コンテキスト ID に強く依存することは推奨されません。 条件付きアクセス ポリシーは通常、ポリシーを適用するために使用できるリソースをより深く理解している IT 管理者によって作成されます。 たとえば、Azure AD テナントの場合、IT 管理者は、MFA に 2FA を使用する用意ができているテナントのユーザー数を把握しているため、2FA を必要とする条件付きアクセス ポリシーの範囲をこれらの対応ユーザーに限定することができます。 同様に、アプリケーションが複数のテナントで使用されている場合、使用される認証コンテキスト ID は異なる可能性があり、場合によっては、まったく使用できない場合もあります。

**2 番目**: 条件付きアクセス認証コンテキストを使用する予定のアプリケーションの開発者は、まず、アプリケーション管理者または IT 管理者に、潜在的に機密性の高いアクションを認証コンテキスト ID にマップする方法を提供することをお勧めします。 手順は大まかに次のとおりです。

1. 認証コンテキスト ID にマップすることができるコード内のアクションを識別します。
1. IT 管理者が、機密性の高いアクションを使用可能な認証コンテキスト ID にマップするために使用できる、アプリの管理ポータル内の画面 (または同等の機能) を作成します。
1. 実行方法の例については、[条件付きアクセス認証コンテキストを使用したステップアップ認証の実行](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)に関するページのコード サンプルを参照してください。

以下の手順は、コード ベースで実行する必要がある変更です。 これらの手順は、大まかに以下で構成されています。

- MS Graph にクエリを実行して、[使用可能なすべての認証コンテキスト を一覧表示します](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)。
- IT 管理者が機密性が高く、高い特権を持つ操作を選択し、使用可能な認証コンテキストに対して CA ポリシーを使用してそれらを割り当てることができるようにします。 
- このマッピング情報をデータベースまたはローカル ストアに保存します。

:::image type="content" source="media/developer-guide-conditional-access-authentication-context/configure-conditional-access-authentication-context.png" alt-text="認証コンテキストを作成するためのセットアップ フロー":::

**3 番目**: アプリケーション (この例では、Web API を想定します) では、保存されたマッピングに対して呼び出しを評価し、その結果に応じて、クライアント アプリのクレーム チャレンジを発生させる必要があります。 このアクションを準備するには、次の手順を実行します。

1. 機密性が高く、認証コンテキストによって保護されている操作の場合は、前に保存した認証コンテキスト ID マッピングに対して **acrs** 要求の値を評価し、次のコード スニペットに示す [クレーム チャレンジ](claims-challenge.md)を発生させます。 

1. 次の図は、ユーザー、クライアント アプリ、Web API の間の相互作用を示しています。

   :::image type="content" source="media/developer-guide-conditional-access-authentication-context/authentication-context-application-flow.png" alt-text="ユーザー、Web アプリ、API、Azure AD の相互作用を示す図":::

   次のコード スニペットは、[条件付きアクセス認証コンテキストを使用したステップアップ認証の実行](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)に関するページのコード サンプルです。 API の最初のメソッド `CheckForRequiredAuthContext()` は、以下を実行します。 

      - 呼び出されているアプリケーションのアクションにステップアップ認証が必要かどうかを確認します。 そのために、このメソッド用に保存されたマッピングがデータベースにあるかどうかを確認します。
      - このアクションで昇格された認証コンテキストが実際に必要な場合は、既存の一致する認証コンテキスト ID があるか **acrs** 要求を確認します。
      - 一致する認証コンテキスト ID が見つからない場合は、[クレーム チャレンジ](claims-challenge.md#claims-challenge-header-format)を発生させます。

      ```
      public void CheckForRequiredAuthContext(string method)
      {
          string authType = _commonDBContext.AuthContext.FirstOrDefault(x => x.Operation == method 
                      && x.TenantId == _configuration["AzureAD:TenantId"])?.AuthContextId;

          if (!string.IsNullOrEmpty(authType))
          {
              HttpContext context = this.HttpContext;
              string authenticationContextClassReferencesClaim = "acrs";

              if (context == null || context.User == null || context.User.Claims == null 
                  || !context.User.Claims.Any())
              {
                  throw new ArgumentNullException("No Usercontext is available to pick claims from");
              }

              Claim acrsClaim = context.User.FindAll(authenticationContextClassReferencesClaim).FirstOrDefault(x 
                  => x.Value == authType);

              if (acrsClaim == null || acrsClaim.Value != authType)
              {
                  if (IsClientCapableofClaimsChallenge(context))
                  {
                      string clientId = _configuration.GetSection("AzureAd").GetSection("ClientId").Value;
                      var base64str = Convert.ToBase64String(Encoding.UTF8.GetBytes("{\"access_token\":{\"acrs\":{\"essential\":true,\"value\":\"" + authType + "\"}}}"));

                      context.Response.Headers.Append("WWW-Authenticate", $"Bearer realm=\"\", authorization_uri=\"https://login.microsoftonline.com/common/oauth2/authorize\", client_id=\"" + clientId + "\", error=\"insufficient_claims\", claims=\"" + base64str + "\", cc_type=\"authcontext\"");
                      context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
                      string message = string.Format(CultureInfo.InvariantCulture, "The presented access tokens had insufficient claims. Please request for claims requested in the WWW-Authentication header and try again.");
                      context.Response.WriteAsync(message);
                      context.Response.CompleteAsync();
                      throw new UnauthorizedAccessException(message);
                  }
                  else
                  {
                      throw new UnauthorizedAccessException("The caller does not meet the authentication  bar to carry our this operation. The service cannot allow this operation");
                  }
              }
          }
      }
      ```

   > [!NOTE]
   > クレーム チャレンジの形式については、[Microsoft Identity Platform のクレーム チャレンジ](claims-challenge.md)に関する記事で説明しています。 

1. クライアント アプリケーションで、クレーム チャレンジをインターセプトし、ユーザーを Azure AD に再度リダイレクトして、さらにポリシーを評価します。 次のコード スニペットは、[条件付きアクセス認証コンテキストを使用したステップアップ認証の実行](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)に関するページのコード サンプルです。

   ```
   internal static string ExtractHeaderValues(WebApiMsalUiRequiredException response)
   {
       if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized && response.Headers.WwwAuthenticate.Any())
       {
           AuthenticationHeaderValue bearer = response.Headers.WwwAuthenticate.First(v => v.Scheme == "Bearer");
           IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
           var errorValue = GetParameterValue(parameters, "error");

           try
           {
               // read the header and checks if it conatins error with insufficient_claims value.
               if (null != errorValue && "insufficient_claims" == errorValue)
               {
                   var claimChallengeParameter = GetParameterValue(parameters, "claims");
                   if (null != claimChallengeParameter)
                   {
                       var claimChallenge = ConvertBase64String(claimChallengeParameter);

                       return claimChallenge;
                   }
               }
           }
           catch (Exception ex)
           {
               throw ex;
           }
       }
       return null;
   }
   ```

   Web API の呼び出しで例外を処理します。クレーム チャレンジが提示された場合、ユーザーを再び Azure ADにリダイレクトして、さらに処理を行います。

   ```
   try
   {
       // Call the API 
       await _todoListService.AddAsync(todo);
   }
   catch (WebApiMsalUiRequiredException hex)
   {
       // Challenges the user if exception is thrown from Web API.
       try
       {
           var claimChallenge =ExtractHeaderValues(hex);
           _consentHandler.ChallengeUser(new string[] { "user.read" }, claimChallenge);

           return new EmptyResult();
       }
       catch (Exception ex)
       {
           _consentHandler.HandleException(ex);
       }

       Console.WriteLine(hex.Message);
   }
   return RedirectToAction("Index");
   ```
   
1. (省略可能) クライアントの機能を宣言します。 クライアントの機能は、呼び出し元のクライアント アプリケーションがクレーム チャレンジを認識し、それに応じて応答をカスタマイズできるかどうかをリソース プロバイダー (RP) (上記の Web API など) が検出するために役立ちます。 この機能は、すべての API クライアントがクレーム チャレンジを処理できるわけではなく、以前の一部のクライアントでは依然として別の応答が必要になる場合に便利であることがあります。 詳細については、「[クライアント機能](claims-challenge.md#client-capabilities)」のセクションを参照してください。

## <a name="caveats-and-recommendations"></a>注意事項と推奨事項

アプリで認証コンテキストの値をハードコーディングしないでください。 アプリは、[MS Graph 呼び出しを使用して](/graph/api/resources/authenticationcontextclassreference)、認証コンテキストの読み取りと適用を行う必要があります。 この方法は、[マルチテナント アプリケーション](howto-convert-app-to-be-multi-tenant.md)の場合に重要になります。 認証コンテキストの値は、Azure AD　テナント間で異なり、Azure AD の無料版では使用できません。 アプリがコード内で認証コンテキストをクエリ、設定、使用する方法の詳細については、[条件付きアクセス認証コンテキストを使用したステップアップ認証の実行](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)に関する記事のコード サンプルを参照して、アプリがコード内で認証コンテキストをクエリ、設定、使用する方法について確認してください。 

アプリ自体が条件付きアクセス ポリシーのターゲットになる場合には認証コンテキストを使用しないでください。 この機能は、アプリケーションの一部で、ユーザーが高いレベルの認証を満たす必要がある場合に最適です。

## <a name="next-steps"></a>次のステップ

- [機密データとアクションに対するきめ細かな条件付きアクセス (ブログ)](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
- [Microsoft ID プラットフォームを使用したゼロ トラスト](/security/zero-trust/identity-developer)
- [Microsoft ID プラットフォームを使用したゼロ トラスト対応アプリの構築](/security/zero-trust/identity-developer)
- [条件付きアクセス認証コンテキストを使用して、Web アプリで高\-特権の操作のステップ\-アップ認証を実行する](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md)
- [条件付きアクセス認証コンテキストを使用して、Web API で高特権の操作のステップアップ認証を実行する](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)
- [条件付きアクセス認証コンテキスト](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)
- [authenticationContextClassReference リソース タイプ - MS Graph](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)
- [Microsoft ID プラットフォームのクレーム チャレンジ、クレーム要求、クライアントの機能](claims-challenge.md)
- [Microsoft Information Protection および SharePoint での認証コンテキストの使用](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)
- [継続的アクセス評価が有効になった API をアプリケーションで使用する方法](app-resilience-continuous-access-evaluation.md)
