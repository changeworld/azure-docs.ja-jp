---
title: "Azure Active Directory B2C: REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合する"
description: "REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合する方法のサンプル"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: da7b8326a89fc6eb0bc3e7365c522d4a3adc809b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Azure Active Directory B2C: REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合する
Azure Active Directory B2C (Azure AD B2C) の基盤となる Identity Experience Framework (IEF) を使用すると、ユーザー体験における RESTful API との統合が可能です。 このチュートリアルでは、B2C と .NET Framework RESTful サービス (Web API) がどのように対話するかを示します。

## <a name="introduction"></a>はじめに
Azure AD B2C では、自分の RESTful サービスを呼び出すことで、独自のビジネス ロジックをユーザー体験に追加できます。 Identity Experience Framework は、**入力要求**コレクションでデータを RESTful サービスに送信し、**出力要求**コレクションで RESTful から返されたデータを受信します。 RESTful サービスの統合により、次の操作を実行できます。

* ユーザー入力データを検証して、不適切なデータが Azure AD に保持されるのを防ぐ。 ユーザーが入力した値が有効でない場合、RESTful サービスは、入力し直すようユーザーに指示するエラー メッセージを返します。 たとえば、ユーザーによって入力されたメール アドレスが顧客データベースに存在するかどうかを確認できます。
* 入力要求を上書きする。 たとえば、ユーザーが名前を小文字または大文字で入力した場合、その名前の最初の文字のみを大文字にすることができます
* 企業の基幹業務アプリケーションとさらに緊密に統合することでユーザー データを拡充する。 RESTful サービスは、ユーザーのメール アドレスを受け取り、顧客データベースにクエリを実行して、ユーザーのロイヤルティ番号を B2C に返すことができます。 返された要求は、AAD ユーザー アカウントに保存するか、次の**オーケストレーション手順**で評価するか、アクセス トークンに含めることができます。
* カスタム ビジネス ロジックを実行する。プッシュ通知の送信、会社のデータベースの更新、ユーザー移行プロセスの実行、アクセス許可の管理、監査などを実行します。

RESTful サービスとの統合は、要求交換または検証技術プロファイルとして設計できます。

* **検証技術プロファイル**: RESTful サービスへの呼び出しは、指定した TechnicalProfile の ValidationTechnicalProfile 内で発生します。 検証技術プロファイルは、ユーザー体験を進める前に、ユーザーが入力したデータを検証します。 検証技術プロファイルは、次を行うことができます。
  * 入力要求を送信する
  * 入力要求を検証し、カスタム エラー メッセージをスローする
  * 出力要求を返送する

* **要求変換**: 前のものと似ていますが、オーケストレーション手順で発生します。 この定義は次に制限されます。
   * 入力要求を送信する
   * 出力要求を返送する

## <a name="restful-walkthrough"></a>RESTful のチュートリアル
このチュートリアルでは、ユーザー入力を検証し、ユーザーのロイヤルティ番号を提供する .NET Framework Web API を開発します。 たとえば、アプリケーションでは、ロイヤルティ番号に基づいて、"プラチナ会員のメリット" へのアクセス権を付与できます。

概要:
*   RESTful サービスの開発 (.NET Framework Web API)
*   ユーザー体験での RESTful サービスの使用
*   入力要求の送信とコード内での要求の読み取り
*   ユーザーの名前の検証
*   ロイヤルティ番号の返送 
*   JSON Web トークン (JWT) へのロイヤルティ番号の追加

## <a name="prerequisites"></a>前提条件
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

## <a name="step-1-create-an-aspnet-web-api"></a>手順 1: ASP.NET Web API を作成する
1.  Visual Studio で、 **[ファイル]、[新規作成]、[プロジェクト]** の順にクリックして、プロジェクトを作成します。
2.  **[新しいプロジェクト]** ダイアログで、**[Visual C#]、[Web]、[ASP.NET Web アプリケーション (.NET Framework)]** の順にクリックします。
3.  アプリケーションに Contoso.AADB2C.API などの名前を付けて、**[OK]** を選択します。

    ![新しい Visual Studio プロジェクトの作成](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4.  **Web API** テンプレートまたは **Azure API アプリ** テンプレートを選択します
5.  認証が **[認証なし]** に設定されていることを確認します

    ![Web API テンプレートの選択](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

6.  **[OK]** をクリックして、プロジェクトを作成します

## <a name="step-2-prepare-the-rest-api-endpoint"></a>手順 2: REST API エンドポイントを準備する

### <a name="step-21-add-data-models"></a>手順 2.1: データ モデルを追加する
モデルは、RESTful サービスの入力要求と出力要求のデータを表します。 コードは、入力要求モデルを JSON 文字列から C# オブジェクト (使用しているモデル) に逆シリアル化することで、入力データを読み取ります。 ASP.NET Web API では、出力要求モデルが JSON に自動的に逆シリアル化された後、シリアル化されたデータが、HTTP 応答メッセージの本文に書き込まれます。 まず、入力要求を表すモデルを作成しましょう。

1.  ソリューション エクスプローラーが表示されていない場合は、**[表示]**メニューをクリックし、**[ソリューション エクスプローラー]** を選択します。 ソリューション エクスプローラーで、[モデル] フォルダーを右クリックします。 コンテキスト メニューの **[追加]** を選択し、**[クラス]** を選択します。

    ![Add model](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

2.  クラス `InputClaimsModel` に名前を付けて、次のプロパティを `InputClaimsModel` クラスに追加します

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

3.  新しいモデル `OutputClaimsModel` を作成し、次のプロパティを `OutputClaimsModel` クラスに追加します

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

4.  もう 1 つのモデル `B2CResponseContent` を作成します。 このモデルは入力の検証を使用して、エラー メッセージをスローします。 次のプロパティを `B2CResponseContent` クラスに追加し、不明な参照を指定して、ファイルを保存します。

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>手順 2.2: コントローラーを追加する
Web API では、"_コントローラー_" は、HTTP 要求を処理するオブジェクトです。 ここでは、出力要求を返すか、名前が有効でない場合に HTTP 競合のエラー メッセージをスローするコントローラーを追加します。

1.  **ソリューション エクスプローラー**で、[コントローラー] フォルダーを右クリックします。 **[追加]**、**[コントローラー]** の順に選択します。

    ![新しいコントロールの追加](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2.  **[スキャフォールディングの追加]** ダイアログで **[Web API コントローラー - 空]** を選択します。 **[追加]**をクリックします。

    !![[Web API 2 コントローラー - 空] の選択](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3.  **[コントローラーの追加]** ダイアログで、コントローラーに `IdentityController` という名前を付けて、**[追加]** をクリックします。

    ![コントローラーの名前の入力](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    IdentityController.cs という名前のファイルが [コントローラー] フォルダーに作成されます。

4.  このファイルがまだ開かれていない場合は、ファイルをダブルクリックして開きます。 このファイルのコードを、次のコード行で置き換えます。

    ```C#
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If not data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create output claims object and set the loyalty number with random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-to-azure"></a>手順 3: Azure に発行する
1.  **ソリューション エクスプローラー**で **Contoso.AADB2C.API** プロジェクトを右クリックし、**[発行]** を選択します。

    ![Microsoft Azure App Service への発行](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2.  **[Microsoft Azure App Service]** が選択されていることを確認し、**[発行]** をクリックします。

    ![新しい Microsoft Azure App Service の作成](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **[App Service の作成]** ダイアログ ボックスが表示されます。このダイアログ ボックスでは、Azure で ASP.NET Web アプリを実行するために必要なすべての Azure リソースを作成できます。

> [!NOTE]
>発行方法の詳細については、「[Azure に ASP.NET Web アプリを作成する](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)」を参照してください

3.  **[Web アプリ名]** に一意のアプリ名 (有効な文字は `a-z`、`0-9`、および `-`) を入力します。 Web アプリの URL は`http://<app_name>.azurewebsites.NET`です。`<app_name>` には Web アプリの名前を指定します。 自動的に生成される名前をそのまま使用してもかまいません。この名前は一意になっています。

    **[作成]** をクリックして、Azure リソースの作成を開始します。

    ![App Service プロパティの入力](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4.  ウィザードの完了後に、Azure に ASP.NET Web アプリを発行してから、既定のブラウザーでアプリを起動し、URL をコピーします。

## <a name="step-4-add-the-new-claim-loyaltynumber-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>手順 4: 新しい要求 `loyaltyNumber` を TrustFrameworkExtensions.xml ファイルのスキーマに追加する
要求 `loyaltyNumber` は、まだスキーマのどこにも定義されていません。 そのため、要素 `<BuildingBlocks>` の中に定義を追加します。 この要素は、TrustFrameworkExtensions.xml ファイルの先頭で見つけることができます。

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-adding-claims-provider"></a>手順 5: クレーム プロバイダーを追加する 
クレーム プロバイダーには必ず、1 つ以上の技術プロファイルが必要で、そのプロファイルによって、クレーム プロバイダーとの通信に必要なエンドポイントとプロトコルが決まります。 クレーム プロバイダーが、複数の技術プロファイルを持つことができる理由はさまざまです。 たとえば、考慮対象のクレーム プロバイダーが複数のプロトコルや、さまざまな機能を持つさまざまなエンドポイントをサポートしている、さまざまな保証レベルでさまざまな要求をリリースしている、などの理由で、技術プロファイルが複数定義される可能性があります。 あるユーザー体験で機密性の高い要求をリリースできても、別のユーザー体験ではリリースできないこともあります。 

次の XML スニペットには、2 つの `TechnicalProfile` を持つ `ClaimsProvider` ノードが含まれます。
* `<TechnicalProfile Id="REST-API-SignUp">` は、RESTful サービスを定義します。 `Proprietary` は、RESTful ベースのプロバイダーのプロトコルとして記述されています。 `InputClaims` 要素は、B2C から REST サービスに送信される要求を定義します。 この例では、要求 `givenName` のコンテンツは `firstName` として REST サービスに送信され、要求 `surename` のコンテンツは `lastName` として REST サービスに送信され、`email` はそのまま送信されます。 `OutputClaims` 要素は、RESTful サービスから B2C に戻る間に取得する要求を定義します。

* `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` は、検証技術プロファイルを、(基本ポリシーで定義されている) 既存の技術プロファイルに追加します。 サインアップ中、検証技術プロファイルは、上記の技術プロファイルを呼び出します。 RESTful サービスが HTTP エラー 409 (競合) を返すと、ユーザーにエラー メッセージが表示されます。 

`<ClaimsProviders>` ノードを見つけて、次の XML スニペットを `<ClaimsProviders>` ノードの下に追加します。

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://yourdomain.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>手順 6: 要求 `loyaltyNumber` がアプリケーションに送信されるように、その要求を証明書利用者ポリシー ファイルに追加する
SignUpOrSignIn.xml 証明書利用者 (RP) ファイルを編集し、`<TechnicalProfile Id="PolicyProfile">` 要素を修正して `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />` を追加します。

新しい要求を追加した後の `RelyingParty` は次のようになります。

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>手順 7: ポリシーをテナントにアップロードする
1.  [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、**[Azure AD B2C]** を開きます
2.  **[Identity Experience Framework]** を選択します
3.  **[All Policies]\(すべてのポリシー\)** を開きます 
4.  **[Upload Policy]**(ポリシーのアップロード) を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。
7.  最後の手順を繰り返して、SignUpOrSignIn.xml をアップロードします

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>手順 8: [今すぐ実行] を使用してカスタム ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

> [!NOTE]
>
>**[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。


2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。

    ![](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3.  **[名]** フィールドに「Test」と入力しようとすると、ページ上部にエラー メッセージが表示されます

    ![ポリシーのテスト](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  **[名]** フィールドに "test" 以外の名前を入力してみます。 ユーザーがサインアップされ、アプリケーションに loyaltyNumber が送信されます。 この JWT の番号に注意してください。

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>[省略可能] 完全なポリシー ファイルとコードをダウンロードする
* これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシーを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)
* 完全なコードは、[参照用のVisual Studio ソリューションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)からダウンロードできます
    
## <a name="next-steps"></a>次のステップ
1.  [基本認証 (ユーザー名とパスワード) を使用して RESTful API をセキュリティで保護する](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
2.  [クライアント証明書を使用して RESTful API をセキュリティで保護する](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)