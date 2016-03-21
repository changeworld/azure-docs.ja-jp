<properties
   pageTitle="マルチテナント アプリケーションでの要求ベース ID の操作 | Microsoft Azure"
   description="発行者の検証と承認に要求を使用する方法"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# マルチテナント アプリケーションでの要求ベース ID の操作

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

## Azure AD の要求

ユーザーがサインインすると、Azure AD は、ユーザーに関する要求セットを含む ID トークンを送信します。要求は、キーと値のペアで表される 1 つの情報です。たとえば、`email`=`bob@contoso.com` です。要求には発行者があり、この例では Azure AD です。発行者は、ユーザーを認証し、要求を作成するエンティティです。発行者を信頼していれば、要求も信頼することになります(逆に、信頼できない発行者の場合は、要求も信頼しないでください)。

概要:

1.	ユーザーを認証します。
2.	IDP から 1 セットの要求が送信されます。
3.	アプリで、要求の正規化または強化が行われます (省略可能)。
4.	アプリで、要求を使用して承認が決定されます。

取得した要求セットは、OpenID Connect で認証要求の [scope パラメーター]によって制御されます。一方、Azure AD から OpenID Connect を介して制限されたセットの要求が発行されます。[サポートされているトークンと要求の種類]に関するページを参照してください。ユーザーの詳細情報を取得するには、Azure AD Graph API を使用する必要があります。

次に、通常はアプリが処理するような AAD の要求を示します。

ID トークンの要求の種類 |	説明
-----------------------|--------------
aud | トークンの発行対象アプリケーションのクライアント ID です。一般的に、この要求はミドルウェアで自動的に検証されるため、心配する必要はありません。例: `"91464657-d17a-4327-91f3-2ed99386406f"`
groups | ユーザーがメンバーである AAD グループの一覧。例: `["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
iss | OIDC トークンの[発行者]。例: `https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
name | ユーザーの表示名。例: `"Alice A."`
oid | AAD のユーザーのオブジェクト識別子。この値は、ユーザーの不変で再利用不可の識別子です。ユーザーの一意の識別子として、電子メール アドレスではなくこの値を使用します。電子メール アドレスは変わる可能性があります。アプリに Azure AD Graph API を使用する場合、オブジェクト ID は、プロファイル情報のクエリに使用される値です。例: `"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
roles | ユーザーのアプリ ロールの一覧。例: `["SurveyCreator"]`
tid | テナント IDこの値は、Azure AD のテナントの一意の識別子です。例: `"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique\_name | 人間が読み取り可能なユーザーの表示名。例: `"alice@contoso.com"`
upn | ユーザー プリンシパル名。例: `"alice@contoso.com"`

この表は、ID トークンに表示される要求の種類の一覧です。ASP.NET Core 1.0 では、OpenID Connect ミドルウェアがユーザー プリンシパルの Claims コレクションを設定するときに、一部の要求の種類を変換します。

-	oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`
-	tid > `http://schemas.microsoft.com/identity/claims/tenantid`
-	unique\_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-	upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## 要求の変換

認証フロー内で、IDP から取得する要求を変更することがあります。ASP.NET Core 1.0 では、OpenID Connect ミドルウェアから **AuthenticationValidated** イベント内の要求変換を実行できます([認証イベント]に関するページを参照してください)。

**AuthenticationValidated** 中に追加された要求は、セッション認証 Cookie に保存されます。Azure AD にプッシュバックされることはありません。

次に、要求変換の例をいくつか示します。

-	**要求の正規化**。ユーザー全体で一貫性のある要求にすることです。これは、複数の IDP から要求を取得する場合に特に関係があります。このような場合、似た情報で異なる要求の種類を使用することがあります。たとえば、Azure AD は、ユーザーの電子メール アドレスを含む "upn" 要求を送信します。他の IDP も "email" 要求を送信する機能性があります。次のコードは、"upn" 要求を "email" 要求に変換します。

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- 存在しない要求の**既定の要求値**を追加します。たとえば、既定のロールにユーザーを割り当てます。場合によっては、承認ロジックを簡易化できます。
- ユーザーのアプリケーション固有の情報を使用して**カスタム要求の種類**を追加します。たとえば、データベース内のユーザーに関する一部の情報を保存することができます。この情報を含むカスタム要求を認証チケットに追加できます。要求は Cookie に保存されるので、ログイン セッションごとに 1 度のみ、データベースから取得する必要があります。一方で、過度に大きな Cookie を作成することを回避するには、Cookie のサイズとデータベース検索の妥協点を考慮する必要があります。   

認証フローが完了すると、要求は `HttpContext.User` で使用できるようになります。この時点で、要求は読み取り専用コレクションとして扱う必要があります。たとえば、要求を使用して承認を判断します。

## 発行者の検証
OpenID Connect の発行者要求 ("iss") によって、ID トークンを発行した IDP が識別されます。OIDC 認証フローの一部では、発行者要求が実際の発行者と一致することが検証されます。この処理は、OIDC ミドルウェアが自動実行します。

Azure AD で発行者値は AD テナントごとに一意です (`https://sts.windows.net/<tenantID>`)。そのため、発行者がアプリにサインインできるテナントであることを確認するには、アプリケーションで追加チェックを実行する必要があります。

シングルテナント アプリケーションの場合、発行者が自分のテナントであることを確認するだけです。実際の処理では、既定で OIDC ミドルウェアが自動的に実行します。マルチテナント アプリの場合、異なるテナントに対応する複数の発行者を許容する必要があります。次に、利用できる一般的なアプローチを示します。

-	OIDC ミドルウェア オプションで、**ValidateIssuer** を false に設定します。これで自動チェックが無効になります。
-	テナントがサインアップしたら、テナント発行者をユーザー DB に保存します。
-	ユーザーがサインインするたびに、データベース内の発行者を検索します。発行元が見つからない場合、そのテナントがサインアップしていないことを意味します。このような場合、サインアップ ページにリダイレクトすることができます。
-  また、たとえばサブスクリプション料金を支払わない顧客など、特定のテナントをブラックリストに登録することもできます。

詳細については、[サインアップとテナントのオンボーディング]に関するページを参照してください。

## 承認の要求を使用する

要求を使用する場合、ユーザーの ID はモノリシック エンティティではなくなります。たとえば、ユーザーには電子メール アドレス、電話番号、誕生日、性別などがあるとします。ユーザーの IDP はこれらすべての情報を保存する可能性があります。ただし、ユーザーの認証時には、一般的にこれらの一部を要求として取得します。このモデルでは、ユーザーの ID は単に複数の要求のバンドルです。ユーザーの承認を判断する場合、特定セットの要求を確認します。つまり、"ユーザー X はアクション Y を実行できますか" という問いは、最終的に "ユーザー X は要求 Z を持っていますか" になります。

要求を要求する基本的なパターンを次に示します。

-  特定の値が設定された特定の要求を持つユーザーを確認する例を次に示します。

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    このコードでは、ユーザーが "Admin" 値の Role 要求を持っているかどうかを確認します。ユーザーが Role 要求を持っていない場合、または複数の Role 要求を持っている場合も正しく処理されます。

    **ClaimTypes** クラスでは、一般的に使用される要求の種類に対して定数を定義します。ただし、要求の種類には任意の文字列値を使用できます。

-	最大で 1 つの値があると想定される要求の種類の場合、1 つの値を取得する例を次に示します。
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-	要求の種類のすべての値を取得する例を次に示します。

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

承認に要求を使用する方法については、[承認]に関するページを参照してください。

## その他のリソース

- [要求ベースの承認]


<!-- Links -->
[シリーズの一部]: guidance-multitenant-identity.md
[scope パラメーター]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[サポートされているトークンと要求の種類]: ../active-directory/active-directory-token-and-claims.md
[発行者]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[認証イベント]: guidance-multitenant-identity-authenticate.md#authentication-events
[サインアップとテナントのオンボーディング]: guidance-multitenant-identity-signup.md
[承認]: guidance-multitenant-identity-authorize.md
[要求ベースの承認]: https://docs.asp.net/en/latest/security/authorization/claims.html
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->