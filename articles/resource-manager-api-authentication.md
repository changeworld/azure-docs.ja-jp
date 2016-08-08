<properties 
   pageTitle="Resource Manager API を使用した承認 | Microsoft Azure"
   description="アプリケーションを Azure と統合するための Azure Resource Manager API と Active Directory を使用した承認の開発者ガイド"
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/12/2016"
   ms.author="dugill;tomfitz" />


# Azure Resource Manager API を使用した承認の開発者ガイド

このトピックでは、アプリケーションと Azure の統合や顧客の Azure リソースの管理を希望するソフトウェア開発者向けに、Azure Resource Manager API を使用して認証する方法を説明します。

アプリケーションは、次の 2 とおりの方法で Resource Manager API にアクセスできます。

1. **ユーザー + アプリケーション アクセス**: アプリケーションがサインインしたユーザーに代わってリソースにアクセスする場合は、この方法を使用します。この方法は、Web アプリケーションやコマンド ライン ツールなど、Azure リソースの "対話形式の管理" だけに対応するアプリケーションに適しています。
1. **アプリケーション専用アクセス**: アプリケーションの ID にリソースへの直接アクセスを許可する必要がある場合は、この方法を使用します。この方法は、Azure への長期間の "オフライン アクセス" を必要とするデーモン サービスやスケジュールされたジョブに適しています。

このトピックでは、この両方の承認方法を使用するアプリケーションを作成する方法を手順を追って説明します。

次のような Web アプリケーションを作成します。

1. Azure ユーザーをサインインさせる
2. ユーザーに代わって Resource Manager に照会して (ユーザー + アプリケーション アクセス)、ユーザーが所有する Azure サブスクリプションの一覧を取得する
3. ユーザーがサブスクリプションをアプリケーションに "接続" できるようにすることで、サブスクリプションでアプリケーションへの直接アクセスを許可する
4. アプリケーションとして Resource Manager にアクセスしてオフライン操作を実行する (アプリケーション専用アクセス)

作成する Web アプリケーションのエンド ツー エンドのフローを次に示します。

![ARM の承認 - アプリケーションの登録 1](./media/resource-manager-api-authentication/ARM-Auth-Swim-Lane.png)

このトピックのコードはすべて、[http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense) で試用できる Web アプリケーションとして実行されます。

ユーザーとして、ログインに使用するアカウントの種類を選択します。

![サインインの選択](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-1.png)

資格情報を入力します。

![資格情報の入力](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-2.png)

アプリケーションに Azure サブスクリプションへのアクセスを許可します。
 
 ![アクセス権の付与](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-3.png)
 
監視のためにサブスクリプションをアプリケーションに接続します。

![サブスクリプションの接続](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4.png)

アプリケーションへの接続を切断または修復します。

![サブスクリプションの切断](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-5.png)

## Azure Active Directory にアプリケーションを登録する

まず、Web アプリケーションを Azure Active Directory (AD) に登録します。アプリケーションの登録により、Azure AD にアプリケーションの主要 ID が作成されます。この ID には、OAuth クライアント ID、応答 URL、アプリケーションが Azure Resource Manager API への認証とアクセスに使用する資格情報など、アプリケーションに関する基本情報が保持されます。また、アプリケーションの登録により、アプリケーションがユーザーに代わって Microsoft API にアクセスするときに必要となるさまざまな委任アクセス許可が記録されます。

「[ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する](resource-group-create-service-principal-portal.md)」では、アプリケーションのセットアップに必要なすべての手順が示されています。次の特性を持つアプリケーションを作成するときは、このトピックをご覧ください。

- **CloudSense** という名前の Web アプリケーションである
- サインイン URL とアプリケーション ID URI が **http://{domain_name_of_your_directory}/{name_of_the_app}** 形式である
- アプリケーションへのサインインに認証キーを使用する
- **Azure Service Management API** の委任されたアクセス許可として **Access Azure Service Management** を追加する**Azure Active Directory** の、**シングル サインオンを有効にし、ユーザーのプロファイルを読み取る**という既定の設定をそのまま使用する
- マルチテナント アプリケーションである

### オプションの構成 - 証明書資格情報

Azure AD では、アプリケーションの証明書資格情報もサポートしています。自己署名証明書を作成し、秘密キーを保持して、Azure AD アプリケーションの登録に公開キーを追加します。認証では、アプリケーションが秘密キーを使用して署名された小さなペイロードを Azure AD に送信すると、Azure AD は登録済みの公開キーを使用して署名を検証します。

証明書の構成については、「[Office 365 でサービス アプリおよびデーモン アプリを構築する](https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365)」をご覧ください。この記事の「アプリケーション用の X.509 公開証明書の構成」に、証明書を設定する手順が記載されています。Azure PowerShell または Azure CLI を使用した証明書の構成例については、「[Azure Resource Manager でのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md)」をご覧ください。

## ユーザーを認証し、アクセス トークンを取得する

これで、アプリケーションのコーディングを開始するために必要なものがすべて揃いました。このトピックでは、エンド ツー エンドのフローの手順ごとに REST API の例を示し、各手順の関連する C# コードへのリンクも提供しています。ASP.NET MVC アプリケーションの完全なサンプルは、[https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense) で入手できます。

ユーザーが Azure サブスクリプションをアプリケーションに接続することに決めた時点から開始します。

ユーザーに次の 2 点を確認する必要があります。

1. **Directory ドメイン名**: ユーザーの Azure サブスクリプションに関連付けられた Azure Active Directory のドメイン名。OAuth 2.0 承認要求は、この Azure AD に送信する必要があります。ユーザーは Azure ポータルに移動し、右上隅にあるアカウントを選択することで、Azure AD のドメイン名を確認できます。ユーザーに次のような視覚的な指示を表示できます。

     ![](./media/resource-manager-api-authentication/show-directory.png)
   
1. **Microsoft アカウントと職場アカウント**: ユーザーが Microsoft アカウント (Live ID) と職場アカウント (組織アカウント) のどちらで Azure サブスクリプションを管理しているのかを確認します。Microsoft アカウントの場合、アプリケーションは、ユーザーを Microsoft アカウントのサインイン ページに直接移動させるよう Azure AD に指示するクエリ文字列パラメーター (&domain\_hint=live.com) を使用して、ユーザーを Azure Active Directory のログイン ページにリダイレクトします。どちらの種類のアカウントでも、受け取った認証コードとトークンは同様に処理されます。

アプリケーションは、ユーザーの資格情報を認証し、認証コードを返すために、OAuth 2.0 承認要求と共にユーザーを Azure AD にリダイレクトします。アプリケーションは、認証コードを使用して Resource Manager のアクセス トークンを取得します。

### 承認要求 (OAuth 2.0)

Azure AD 承認エンドポイントに Open ID Connect/OAuth 2.0 承認要求を発行します。

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Authorize

この要求で使用できるクエリ文字列パラメーターについては、「[Authorization Code Grant Flow (認証コード付与フロー)](https://msdn.microsoft.com/library/azure/dn645542.aspx)」をご覧ください。

次の例は、OAuth 2.0 承認を要求する方法を示しています。

    https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD がユーザーを認証し、必要に応じて、アプリケーションにアクセス許可を付与するようユーザーに求めます。アプリケーションの応答 URL に認証コードが返されます。要求された response\_mode に応じて、Azure AD はデータをクエリ文字列で送信するか、POST データとして送信します。

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### 承認要求 (Open ID Connect)

ユーザーに代わって Azure Resource Manager にアクセスするだけでなく、ユーザーが Azure AD アカウントを使用してアプリケーションにサインインできるようにする場合は、Open ID Connect 承認要求を発行します。Open ID Connect により、アプリケーションはユーザーのサインインに使用できる id\_token も Azure AD から受け取ります。

OAuth 2.0 承認要求のクエリ文字列パラメーターを次に示します。

| QS パラメーター | 値
|----|----
| client\_id | アプリケーションのクライアント ID
| response\_mode | **form\_post** または **query**
| response\_type | **code+id\_token**
| redirect\_uri | アプリケーションの URL エンコードされた応答 URL。例: http://www.vipswapper.com/cloudsense/Account/SignIn |
| resource | Azure Service Management API の URL エンコードされた識別子: https://management.core.windows.net/ |
| scope | openid+profile
| nonce | 承認応答が要請されているものであり、再生されないことを保証するために、返された id\_token に承認要求を結び付けるデータ。
| domain\_hint | live.com <br />**注**: domain\_hint パラメーターは、ユーザーが Microsoft アカウントを使用して Azure サブスクリプションを管理している場合にのみ使用します。
| state | 必要に応じて、Azure AD が応答で返す状態データを指定します。

Open ID Connect 要求の例を次に示します。

     https://login.windows.net/dushyantgill.com/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD がユーザーを認証し、必要に応じて、アプリケーションにアクセス許可を付与するようユーザーに求めます。アプリケーションの応答 URL に認証コードが返されます。要求された response\_mode に応じて、Azure AD はデータをクエリ文字列で送信するか、POST データとして送信します。

Open ID Connect 応答の例を次に示します。

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### id\_token を検証する

アプリケーションは、ユーザーをサインインさせる前に id\_token を検証する必要があります。トークンの検証は複雑なトピックであるため、開発プラットフォームで標準の JSON Web トークン ハンドラー ライブラリを使用することをお勧めします ([.NET Azure AD JWT ハンドラーのソース コード](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/System.IdentityModel.Tokens.Jwt/JwtSecurityTokenHandler.cs)をご覧ください)。ただし、アプリケーションのセキュリティは開発者の責任であるため、id\_token を適切に処理するために使用するライブラリによって、トークンの次の側面が確実に検証されるようにします。

- **トークンのタイミング**: nbf 要求と exp 要求をチェックして、トークンが新しすぎたり古すぎたりしないことを確認します。時刻のずれに対応するために、少し余裕を持たせるのが一般的です (5 分程度)。
- **発行元**: iss 要求をチェックして、トークンの発行元が Azure Active Directory (https://sts.windows.net/{tenant_id_of_the_directory}) であることを確認します。
- **対象**: aud 要求をチェックして、トークンの発行対象がアプリケーションであることを確認します。この値は、アプリケーションのクライアント ID である必要があります。
- **nonce**: nonce 要求を承認要求で送信した nonce データと照合して、応答がアプリケーションから要請されたものであり、トークンが再生されていないことを確認します。
- **署名**: アプリケーションは、トークンが Azure Active Directory によって署名されていることを確認する必要があります。Azure AD の署名キーは頻繁にロールオーバーされるので、アプリケーションは更新されたキーの有無を毎日ポーリングするか、署名の検証に失敗した場合は更新されたキーの問題を見つける必要があります。詳細については、「[Important Information About Signing Key Rollover in Azure AD (Azure AD での署名キーのロールオーバーに関する重要な情報)](active-directory/active-directory-signing-key-rollover.md)」をご覧ください。

**id\_token** が検証されたら、ユーザーの不変の識別子または再利用できない識別子として oid 要求値を使用します。ユーザーの、人間が判読可能な表示名として、**unique\_name** 要求または upn/email 要求を使用します。また、表示用にオプションの given\_name/family\_name 要求を使用することもできます。

### トークン要求 (OAuth 2.0 コード付与フロー)

アプリケーションが Azure AD から認証コードを受け取ったので、次に、Azure Resource Manager のアクセス トークンを取得します。OAuth 2.0 コード付与トークン要求を Azure AD トークン エンドポイントに送ります。

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

この要求で使用できるクエリ文字列パラメーターについては、「[Authorization Code Grant Flow (認証コード付与フロー)](https://msdn.microsoft.com/library/azure/dn645542.aspx)」をご覧ください。

パスワード資格情報を使用したコード付与トークンの要求の例を次に示します。

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

証明書資格情報を使用する場合は、JSON Web トークン (JWT) を作成し、アプリケーションの証明書資格情報の秘密キーを使用して署名 (RSA SHA256) します。トークンの要求の種類は、「[Authorization Code Grant Flow (認証コード付与フロー)](https://msdn.microsoft.com/library/azure/dn645542.aspx)」に記載されています。クライアント アサーション JWT トークンの署名については、[Active Directory 認証ライブラリ (.NET) のコード](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/master/src/ADAL.NET/CryptographyHelper.cs)を参照してください。

クライアント認証の詳細については、[Open ID Connect 仕様](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)をご覧ください。こちらに[クライアント アサーション JWT トークンのサンプル](https://www.authnauthz.com/OAuth/ParseJWTToken?token=eyJhbGciOiJSUzI1NiIsIng1dCI6IlFwcXdKZnJNZ003ekJ4M1hkM2NSSFdkYVFsTSJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ud2luZG93cy5uZXRcL2FhbHRlc3RzLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQyODk2Mjk5MSwiaXNzIjoiOTA4M2NjYjgtOGE0Ni00M2U3LTg0MzktMWQ2OTZkZjk4NGFlIiwianRpIjoiMmYyMjczMzQtZGQ3YS00NzZkLWFlOTYtYzg4NDQ4YTkxZGM0IiwibmJmIjoxNDI4OTYyMzkxLCJzdWIiOiI5MDgzY2NiOC04YTQ2LTQzZTctODQzOS0xZDY5NmRmOTg0YWUifQ.UXQE9H-FlwxYQmRVG0-p7pAX9TFgiRXcYr7GhbcC7ndIPHKpZ5tfHWPEgBl3ZVRvF2l8uA7HEV86T7t2w7OHhHwLBoW7XTgj-17hnV1CY21MwjrebPjaPIVITiilekKiBASfW2pmss3MjeOYcnBV2MuUnIgt4A_iUbF_-opRivgI4TFT4n17_3VPlChcU8zJqAMpt3TcAxC3EXXfh10Mw0qFfdZKqQOQxKHjnL8y7Of9xeB9BBD_b22JNRv0m7s0cYRx2Cz0cUUHw-ipHhWaW7YwhVRMfK6BMkaDUgaie4zFkcgHb7rm1z0rM1CvzIqP-Mwu3oEqYpY9cYo8nEjMyA)があります。

証明書資格情報を使用したコード付与トークンの要求の例を次に示します。

	POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
	
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 1012
	
	grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

コード付与トークンの応答の例を次に示します。

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### コード付与トークン応答を処理する

正常なトークン応答には、Azure Resource Manager の (ユーザー + アプリケーション) アクセス トークンが含まれます。アプリケーションは、このアクセス トークンを使用して、ユーザーに代わって Resource Manager にアクセスします。Azure AD によって発行されたアクセス トークンの有効期間は 1 時間です。Web アプリケーションが (ユーザー + アプリケーション) アクセス トークンを更新する必要があることはほとんどありません。ただし、その必要がある場合は、アプリケーションがトークン応答で受け取った更新トークンを使用できます。OAuth 2.0 トークン要求を Azure AD トークン エンドポイントに送ります。

    https://login.microsoftonline.com/{directory_domain_name}/OAuth2/Token

更新要求で使用するパラメーターについては、「[Authorization Code Grant Flow (認証コード付与フロー)](https://msdn.microsoft.com/library/azure/dn645542.aspx)」をご覧ください。

次の例は、更新トークンの使用方法を示しています。

    POST https://login.windows.net/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

更新トークンを使用して Azure Resource Manager の新しいアクセス トークンを取得できますが、更新トークンはアプリケーションによるオフライン アクセスには適していません。更新トークンの有効期間は限られています。また、更新トークンはユーザーにバインドされています。ユーザーが組織を離れた場合、更新トークンを使用するアプリケーションはアクセスできなくなります。このアプローチは、Azure リソースを管理するためにチームで使用されるアプリケーションには適していません。

## 使用可能なサブスクリプションの一覧を表示する

アプリケーションは、ユーザーに代わって Azure Resource Manager にアクセスするためのトークンを取得しました。

次に、ユーザーが Azure サブスクリプションをアプリケーションに接続できるようにします。これにより、ユーザーが不在の場合でも、アプリケーションはサブスクリプションを管理できるようになります (長期間のオフライン アクセス)。ユーザーがアクセスを管理できる Azure サブスクリプションの一覧を表示し、ユーザーが RBAC の役割をアプリケーションの ID に直接割り当てることができるようにします。

![ARM の承認 - サンプル アプリケーション Ux 4](./media/resource-manager-api-authentication/ARM-Auth-Sample-App-Ux-4-full.png)

### ユーザーが任意のアクセス権を持つサブスクリプションの一覧を表示する

まず、[Resource Manager List Subscriptions](https://msdn.microsoft.com/library/azure/dn790531.aspx) API を呼び出して、ユーザーが任意のアクセス権を持つすべてのサブスクリプションの一覧を表示します。次に、ユーザーがアクセスを管理できるサブスクリプションを特定します。

ASP.net MVC サンプル アプリケーションの [GetUserSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L79) メソッドは、この呼び出しを実装しています。

サブスクリプションの一覧を表示する要求の例を次に示します。

    GET https://management.azure.com/subscriptions?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

サブスクリプションの一覧を表示する応答の例を次に示します。

    HTTP/1.1 200 OK

    {"value":[{"id":"/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2","subscriptionId":"34370e90-ac4a-4bf9-821f-85eeedeae1a2","displayName":"Sandbox","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}},{"id":"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e","subscriptionId":"c276fc76-9cd4-44c9-99a7-4fd71546436e","displayName":"Production","state":"Enabled","subscriptionPolicies":{"locationPlacementId":"Public_2014-09-01","quotaId":"PayAsYouGo_2014-09-01"}}]}

### サブスクリプションに対するユーザーのアクセス許可を取得する

接続/切断操作は、ユーザーがアクセスを管理できるサブスクリプションにのみ表示する必要があります。サブスクリプションごとに、[Resource Manager List Permissions](https://msdn.microsoft.com/library/azure/dn906889.aspx) API を呼び出して、ユーザーがサブスクリプションのアクセス管理権限を持っているかどうかを確認します。

ASP.net MVC サンプル アプリケーションの [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L132) メソッドは、この呼び出しを実装しています。

次の例は、サブスクリプションに対するユーザーのアクセス許可を要求する方法を示しています。83cfe939-2402-4581-b761-4f59b0a041e4 は、サブスクリプションの ID です。

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

サブスクリプションに対するユーザーのアクセス許可を取得する応答の例を次に示します。

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Permissions API は複数のアクセス許可を返します。各アクセス許可は、許可されているアクション (actions) と許可されていないアクション (notactions) で構成されます。操作がアクセス許可の許可されているアクションのリストにあり、そのアクセス許可の notactions リストにない場合、ユーザーはそのアクションを実行できます。**microsoft.authorization/roleassignments/write** は、アクセス管理権限を付与するアクションです。アプリケーションは、アクセス許可の結果を解析して、各アクセス許可の actions と notactions のこのアクション文字列で正規表現に一致するものを探す必要があります。

### 省略可能: ユーザー アカウントが存在するディレクトリの一覧を表示する

ユーザーのアカウントが複数の Azure Active Directory に存在する場合があります。ユーザーが最初に正しいディレクトリ名を指定していなかった可能性があります。その場合、一覧に目的のサブスクリプションが表示されません。

[Resource Manager List Tenants](https://msdn.microsoft.com/library/azure/dn790536.aspx) API は、ユーザーのアカウントが存在するすべてのディレクトリの識別子の一覧を表示します。この API を呼び出して、ユーザーのアカウントが複数のディレクトリに存在するかどうかを確認できます。必要に応じて、"お探しのサブスクリプションが見つかりませんか? そのサブスクリプションは、あなたがメンバーとして属している他の Azure Active Directory に存在する可能性があります。ここをクリックしてディレクトリを切り替えてください。" といったメッセージをユーザーに表示します。

ASP.NET MVC サンプル アプリケーションの [GetUserOrganizations](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) メソッドは、この呼び出しを実装しています。

ディレクトリの一覧を表示する要求の例を次に示します。

    GET https://management.azure.com/tenants?api-version=2014-04-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1Qi****8DJf1UO4a-ZZ_TJmWFlwO1mM7Cw6JWtfY2lGc5A

ディレクトリの一覧を表示する応答の例を次に示します。

    HTTP/1.1 200 OK

    {"value":[{"id":"/tenants/7fe877e6-a150-4992-bbfe-f517e304dfa0","tenantId":"7fe877e6-a150-4992-bbfe-f517e304dfa0"},{"id":"/tenants/62e173e9-301e-423e-bcd4-29121ec1aa24","tenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24"}]}

## サブスクリプションをアプリケーションに接続する

ユーザーがアプリケーションに接続できる Azure サブスクリプションの一覧を入手しました。次に、接続を作成するコマンドをユーザーに提供します。ユーザーが**接続**を選択したら、アプリケーションは次の処理を実行します。

1. サブスクリプションでアプリケーションの ID に適切な RBAC 役割を割り当てます。
2. サブスクリプションに対するアプリケーションのアクセス許可を照会するか、アプリケーション専用トークンを使用して Resource Manager にアクセスすることで、アクセスの割り当てを検証します。
1. アプリケーションの "接続されているサブスクリプション" のデータ構造に接続を記録して、サブスクリプションの ID を保持します。

最初の手順を詳しく見てみましょう。アプリケーションの ID に適切な RBAC 役割を割り当てるには、以下を確認する必要があります。

- ユーザーの Azure Active Directory でのアプリケーションの ID のオブジェクト ID
- サブスクリプションでアプリケーションに必要な RBAC 役割の識別子

最初の部分を詳しく説明します。アプリケーションが Azure AD からユーザーを初めて認証したときに、その Azure AD にアプリケーションのサービス プリンシパル オブジェクトが作成されます。Azure は、サービス プリンシパルに割り当てられている RBAC 役割が、Azure リソースの対応するアプリケーションへの直接アクセスを許可できるようにします。これはまさに私たちが行おうとしていることなので、まず、Azure AD Graph API に照会して、サインインしたユーザーの Azure AD でのアプリケーションのサービス プリンシパルの識別子を確認します。

Azure Resource Manager のアクセス トークンしかないため、Azure AD Graph API を呼び出すための新しいアクセス トークンが必要です。Azure AD の各アプリケーションは、独自のサービス プリンシパル オブジェクトにクエリを実行するアクセス許可を持っているので、このためにユーザー + アプリケーション アクセス トークンは不要です。アプリケーション専用アクセス トークンで十分です。

<a id="app-azure-ad-graph">
### Azure AD Graph API のアプリケーション専用アクセス トークンを取得する

アプリケーションを認証し、Azure AD Graph API へのトークンを取得するには、OAuth 2.0 クライアント資格情報付与フロー トークン要求を Azure AD トークン エンドポイントに発行します (**https://login.microsoftonline.com/{directory\_domain\_name}/OAuth2/Token**)。

ASP.net MVC サンプル アプリケーションの [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L73) メソッドの行 73 ～ 77 では、Active Directory Authentication Library for .NET を使用して、Graph API のアプリケーション専用アクセス トークンを取得しています。

クライアント資格情報により、次のトークン要求データが付与されます。

| 要素 | 値
|----|----
| grant\_type | **client\_credentials**
| client\_id | アプリケーションのクライアント ID
| resource | アクセス トークンが要求されているリソースの URL エンコードされた識別子。このケースでは Azure AD Graph API の識別子 (**https://graph.windows.net/**) です。
| client\_secret または client\_assertion\_type + client\_assertion | アプリケーションでパスワード資格情報を使用している場合は、client\_secret を使用してください。アプリケーションが証明書資格情報を使用する場合は、client\_assertion を使用します。

クライアント資格情報付与トークンの要求の例を次に示します。

    POST https://login.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

クライアント資格情報付与トークンの応答の例を次に示します。

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### ユーザーの Azure AD でのアプリケーション サービス プリンシパルの ObjectId を取得する

次に、アプリケーション専用のアクセス トークンを使用して、[Azure AD Graph Service Principals](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API に問い合わせを行い、ディレクトリに登録されているアプリケーションのサービス プリンシパルのオブジェクト ID を特定します。

ASP.net MVC サンプル アプリケーションの [GetObjectIdOfServicePrincipalInOrganiation](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#L66) メソッドは、この呼び出しを実装しています。

次の例は、アプリケーションのサービス プリンシパルを要求する方法を示しています。a0448380-c346-4f9f-b897-c18733de9394 は、アプリケーションのクライアント ID です。

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

アプリケーションのサービス プリンシパルの要求に対する応答の例を次に示します。

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### Azure RBAC の役割の識別子を取得する

選択したサブスクリプションで、アプリケーションのサービス プリンシパルに適切な RBAC 役割を割り当てる必要があります。そのためには、Azure RBAC の役割の識別子を確認する必要があります。

アプリケーションの適切な RBAC 役割:

- アプリケーションが変更は一切行わず、サブスクリプションを監視するだけの場合は、サブスクリプションに対する閲覧者アクセス許可だけが必要です。**閲覧者**役割を割り当てます。
- アプリケーションがエンティティを作成/変更/削除して、Azure サブスクリプションを管理する場合は、作成協力者アクセス許可のいずれかが必要です。
  - 特定の種類のリソースを管理するには、そのリソース固有の作成協力者役割 (仮想マシン作成協力者、仮想ネットワーク作成協力者、ストレージ アカウント作成協力者など) を割り当てます。
  - すべての種類のリソースを管理するには、**作成協力者**役割を割り当てます。

アプリケーションの役割の割り当てはユーザーに表示されるので、最低限必要な権限を選択します。

[Resource Manager Role Definition API](https://msdn.microsoft.com/library/azure/dn906879.aspx) を呼び出してすべての Azure RBAC 役割の一覧を表示し、結果を順に調べて目的の役割定義を名前で検索します。

ASP.net MVC サンプル アプリケーションの [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L354) メソッドは、この呼び出しを実装しています。

次の要求例は、Azure RBAC の役割の識別子を取得する方法を示しています。09cbd307-aa71-4aca-b346-5f253e6e3ebb は、サブスクリプションの ID です。

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-07-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

応答は次の形式になります。

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

この API を継続的に呼び出す必要はありません。役割定義の既知の GUID を確認したら、次のように役割定義 ID を作成できます。

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

よく使用される組み込み役割の既知の GUID を次に示します。

| 役割 | Guid |
| ----- | ------ |
| 閲覧者 | acdd72a7-3385-48ef-bd42-f606fba81ae7
| 共同作成者 | b24988ac-6180-42a0-ab88-20f7382dd24c
| 仮想マシンの共同作業者 | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| 仮想ネットワークの共同作業者 | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| ストレージ アカウントの共同作業者 | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Web サイトの共同作業者 | de139f84-1756-47ae-9be6-808fbbe84772
| Web Plan の共同作業者 | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server の共同作業者 | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| SQL DB の共同作業者 | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### アプリケーションに RBAC の役割を割り当てる

[Resource Manager Create Role Assignment](https://msdn.microsoft.com/library/azure/dn906887.aspx) API を使用して、選択したサブスクリプションでアプリケーションのサービス プリンシパルに適切な RBAC 役割を割り当てるために必要なものがすべて揃いました。

ASP.net MVC サンプル アプリケーションの [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L269) メソッドは、この呼び出しを実装しています。

アプリケーションに RBAC の役割を割り当てる要求の例を次に示します。

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2014-10-01-preview HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

この要求では、次の値が使用されています。

| Guid | 説明 |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | サブスクリプションの ID
| c3097b31-7309-4c59-b4e3-770f8406bad2 | アプリケーションのサービス プリンシパルのオブジェクト ID
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | 閲覧者 RBAC 役割の既知の GUID
| 4f87261d-2816-465d-8311-70a27558df4c | 新しい役割の割り当て用に作成された新しい GUID

応答は次の形式になります。

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### Azure Resource Manager のアプリケーション専用アクセス トークンを取得する

次に、サブスクリプションでアプリケーションが必要なアクセス権を持っていることを検証します。そのためには、Azure Resource Manager のアプリケーション専用トークンを使用して、サブスクリプションでテスト タスクを実行する必要があります。テスト タスクでは、アプリケーションが監視/管理をオフラインで実行するために、サブスクリプションで必要なアクセス権を確かに持っていることを検証します。

Azure Resource Manager のアプリケーション専用アクセス トークンを取得するには、「[Azure AD Graph API のアプリケーション専用アクセス トークンを取得する](#app-azure-ad-graph)」の手順に従います。リソース パラメーターには別の値を指定します。

    https://management.core.windows.net/

ASP.net MVC サンプル アプリケーションの [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) メソッドの行 210 ～ 214 では、Active Directory Authentication Library for .Net を使用して、Azure Resource Manager のアプリケーション専用アクセス トークンを取得しています。

#### サブスクリプションに対するアプリケーションのアクセス許可を取得する

アプリケーションが Azure サブスクリプションで必要なアクセス権を持っていることを確認する場合、ユーザーがサブスクリプションのアクセス管理権限を持っているかどうかを確認したときと同様に、[Resource Manager Permissions](https://msdn.microsoft.com/library/azure/dn906889.aspx) API を呼び出すこともできます。ただし、今回は、前の手順で取得したアプリケーション専用アクセス トークンを使用して Permissions API を呼び出します。

ASP.NET MVC サンプル アプリケーションの [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L203) メソッドは、この呼び出しを実装しています。

## 接続されているサブスクリプションを管理する

サブスクリプションでアプリケーションのサービス プリンシパルに適切な RBAC 役割を割り当てると、アプリケーションは Azure Resource Manager のアプリケーション専用アクセス トークンを使用して、サブスクリプションを監視/管理し続けることができます。

サブスクリプションの所有者が、Microsoft Azure 管理ポータルまたはコマンド ライン ツールを使用して、アプリケーションの役割の割り当てを削除した場合、アプリケーションはそのサブスクリプションにアクセスできなくなります。その場合、サブスクリプションとの接続がアプリケーションの外部から提供されていたことをユーザーに通知し、接続を "修復" するオプションを提供する必要があります。"修復" により、オフラインで削除された役割の割り当てが再作成されます。


## サブスクリプションを切断する

ユーザーがサブスクリプションをアプリケーションに接続できるようにしたときと同様に、ユーザーがサブスクリプションを切断することもできるようにする必要があります。アクセス管理の観点から言うと、切断はアプリケーションのサービス プリンシパルがサブスクリプションで持つ役割の割り当てを削除することを意味します。必要に応じて、サブスクリプションでのアプリケーションの状態も削除される場合があります。サブスクリプションでアクセス管理アクセス許可を持つユーザーだけがサブスクリプションを切断できます。

ASP.net MVC サンプル アプリケーションの [RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L303) メソッドは、この呼び出しを実装しています。

これで完了です。ユーザーは、Azure サブスクリプションをアプリケーションに簡単に接続して管理できるようになりました。

<!---HONumber=AcomDC_0727_2016-->