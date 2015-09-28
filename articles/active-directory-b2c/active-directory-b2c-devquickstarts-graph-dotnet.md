<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="アプリケーション ID を使用して B2C ディレクトリ用の Graph API を呼び出してプロセスを自動化する方法。"
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="dastrock"/>


# Azure AD B2C プレビュー: Graph API の使用

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Azure AD B2C ディレクトリは、非常に大きくなる傾向があります。これは、多くの一般的なディレクトリ管理タスクをプログラムで実行する必要があることを意味します。その代表例はユーザー管理です。たとえば、既存のユーザー ストアを B2C ディレクトリに移行する必要があったり、バック グラウンドで Azure AD 内にユーザー アカウントを作成してユーザー登録を独自のページでホストしたりする場合があります。この種のタスクでは、ユーザー アカウントの作成、読み取り、更新、削除を実行する機能が必要です。Azure AD Graph API を使用してこれらの操作を実行できます。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
B2C ディレクトリでは、主に 2 つのモードでGraph API と通信します。

- 対話型の一度だけ実行されるタスクでは、多くの場合、B2C ディレクトリの管理者アカウントとして機能する管理タスクを実行します。このモードでは、管理者は、Graph API への呼び出しを実行する前に、資格情報を使用してログインする必要があります。
- 自動化された継続的なタスクでは、必要な権限を付与した何らかの種類のサービス アカウントを使用して管理タスクを実行します。Azure AD では、アプリケーションを登録し、"アプリケーション ID" を使用して Azure AD に対する認証を行うことで、これを実行できます。この操作には [OAuth 2.0 Client Credentials Grant](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api) が使用されます。この場合、アプリケーションは、特定のユーザーとしてではなく、それ自身として機能する Graph API を呼び出します。  

この記事では、後者の自動化された事例を実行する方法を示します。デモンストレーションとして、ユーザーの CRUD 操作を実行する .NET 4.5 "B2CGraphClient" を構築します。いろいろなことを試すために、クライアントにはさまざまなメソッドを呼び出すことができる Windows コマンド ライン インターフェイスがあるものとします。ただし、コードは、非対話型の自動化された方法で動作するように記述されます。それでは始めましょう。

## B2C 対応ディレクトリを取得する

アプリケーションとユーザーの作成、または Azure AD との対話を行う前に、B2C 対応ディレクトリとそのディレクトリの管理者アカウントが必要です。まだ用意していない場合は、「[Azure Active Directory B2C プレビュー: Azure AD B2C ディレクトリの作成方法](active-directory-b2c-get-started.md)」のガイドに従ってください。

## サービス アプリケーションをディレクトリに登録する

B2C ディレクトリが用意できたら、Azure AD Powershell コマンドレットを使用してサービス アプリケーションを作成する必要があります。最初に、[Microsoft Online Services サインイン アシスタント](http://go.microsoft.com/fwlink/?LinkID=286152) をダウンロードしてインストールします。次に、[64-bit Azure Active Directory Module for Windows Powershell](http://go.microsoft.com/fwlink/p/?linkid=236297) をダウンロードしてインストールします。

Powershell モジュールをインストールした後、Powershell を開き、B2C ディレクトリに接続します。`Get-Credential` を実行すると、ユーザー名とパスワードの入力を求められます。B2C ディレクトリ管理者アカウントのユーザー名とパスワードを入力します。

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
``` 

アプリケーションを作成する前に、新しい "クライアント シークレット" を生成する必要があります。アプリケーションは、このクライアント シークレットを使用して Azure AD に対する認証を行ってアクセス トークンを取得します。Powershell で、有効なシークレットを生成できます。

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
``` 

上記の最後のコマンドによって、新しいクライアント シークレットが出力されます。それを安全な場所にメモしておいてください。すぐに必要になります。これで、新しいクライアント シークレットをアプリの資格情報として指定して、アプリケーションを作成できます。

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

アプリケーションの作成が成功すると、上記のようなアプリケーションの一部のプロパティが出力されます。`ObjectId` と `AppPrincipalId` の両方が必要になるので、これらの値もメモしておきます。

これで B2C ディレクトリ内にアプリケーションが作成されたので、ユーザーの CRUD 操作を実行するために必要なアクセス許可をそのアプリケーションに割り当てる必要があります。アプリケーションには、次の 3 つの異なるロールを割り当てる必要があります。ディレクトリ リーダー (ユーザーを読み取るため)、ディレクトリ ライター (ユーザーの作成と更新を行うため)、およびユーザー アカウント管理者 (ユーザーを削除するため)。これらのロールには既知の識別子があるため、次のコマンドを実行できます (`-RoleMemberObjectId` パラメーターを前述の `ObjectId` に置き換えます)。すべてのディレクトリ ロールの一覧を表示するには、`Get-MsolRole` を実行してみてください。

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

B2C ディレクトリに対してユーザーの作成、読み取り、更新、削除を実行する権限を持つアプリケーションが用意されました。次に、それを使用するコードを記述していきましょう。

## サンプル コードをダウンロード、構成、ビルドする

まず、サンプル コードをダウンロードして実行します。その後、バック グラウンドで何が起こっているかを調べることができます。[サンプル コードを .zip としてダウンロード](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)するか、任意のディレクトリにクローンを作成できます。

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

`B2CGraphClient\B2CGraphClient.sln` Visual Studio ソリューションを Visual Studio で開きます。`B2CGraphClient` プロジェクトで、`App.config` ファイルを開きます。アプリの 3 つの設定を、次のように独自の値に置き換えます。

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

次に、`B2CGraphClient` ソリューションを右クリックしてサンプルをリビルドします。成功すると、実行可能な `B2C.exe` が `B2CGraphClient\bin\Debug` に配置されます。

## Graph API でユーザーの CRUD を使用する

B2CGraphClient を使用するには、Windows コマンド プロンプトを開き、`Debug` ディレクトリに移動します。`B2C Help` コマンドを実行します。

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

各コマンドの短い説明が表示されます。これらのコマンドのいずれかを呼び出すと、B2CGraphClient が Azure AD Graph API に要求を行います。

### アクセス トークンの取得

Graph API へのすべての要求は、認証用のアクセス トークンを必要とします。B2CGraphClient では、アクセス トークンの取得を支援するためにオープン ソースである Active Directory Authentication Library (ADAL) を使用します。常に ADAL を使用してトークンを取得する必要はありません。トークンは、HTTP 要求を自分で作成することで取得できます。ADAL は、単純な API を提供し、アクセス トークンのキャッシュなどのいくつかの重要な詳細を処理することで、トークンの取得を多少容易にします。

> [AZURE.NOTE]このコード サンプルでは、一般に利用できる ADAL のバージョンである ADAL v2 を意図的に使用しています。Azure AD B2C で動作するように設計された ADAL v4 は使用していないことに注意してください。Azure AD B2C プレビューでは、Graph API と通信を行うには ADAL v2 を使用する必要があります。ADAL v4 による Graph API へのアクセスは今後実現されるため、完成した Azure AD B2C ソリューションで 2 つの異なる ADAL のバージョンを使用する必要はありません。

B2CGraphClient を実行すると、`B2CGraphClient` クラスのインスタンスが作成されます。このクラスのコンス トラクターは、ADAL の認証のスキャフォールディングを設定します。

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the directory to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are 
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

例として、`B2C Get-User` コマンドを使用してみましょう。`Get-User` がそれ以外の入力なしで呼び出されると、CLI は `B2CGraphClient.GetAllUsers(...)` メソッドを呼び出します。このメソッドは、HTTP GET 要求を Graph API に送信する `B2CGraphClient.SendGraphGetRequest(...)` を呼び出します。それは、GET 要求を送信する前に、ADAL を使用してアクセス トークンを取得します。

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	... 

```

このように、ADAL の `AuthenticationContext.AcquireToken(...)` メソッドを呼び出すことで、Graph API 用のアクセス トークンを取得できます。ADAL は、アプリケーションの ID を表す access\_token を返します。

### ユーザーの読み取り

Graph API からユーザーの一覧または特定のユーザーを取得するには、HTTP GET 要求を `/users` エンドポイントに送信します。ディレクトリ内のすべてのユーザーを求める要求は次のようになります。

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

この要求の実行を確認するには、次を実行してみます。

 ```
 > B2C Get-User
 ```
 
ここで注意すべき重要な点は 2 つあります。

- ADAL 経由で取得されたアクセス トークンは、`Bearer` スキームを使用する `Authorization` ヘッダーに追加されます。
- B2C ディレクトリに対しては、クエリ パラメーター `api-version=beta` を使用する必要があります。


> [AZURE.NOTE]Azure AD Graph API のベータ版の機能はプレビュー段階です。ベータ版の詳細については、[この Graph API チームのブログの投稿](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx)を参照してください。

これらの詳細は、両方とも `B2CGraphClient.SendGraphGetRequest(...)` メソッド内で処理されます。

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...
	
	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}
	
	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);
	
	... 
```
		
### コンシューマー ユーザー アカウントの作成 

B2C ディレクトリにユーザー アカウントを作成するときは、HTTP POST 要求を `/users` エンドポイントに送信できます。

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.
	 
	"accountEnabled": false,                    // always set to false
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	}
}
```

上記の要求に含まれる各プロパティは、コンシューマー ユーザーを作成するために必要です。`//` コメントは説明のために含まれています。実際の要求では使用しないください。

この要求の実行を確認するには、次のコマンドのいずれかを実行してみます。

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` コマンドは、入力パラメーターとして `.json` ファイルを使用します。このファイルには、ユーザー オブジェクトの JSON 表現が含まれます。サンプル コードには、ニーズに合わせて変更できる 2 つの `.json` ファイルのサンプルが含まれています (`usertemplate-email.json` と `usertemplate-username.json`)。上記の必須フィールドに加えて、これらのファイルには、使用できる多数のオプション フィールドがあります。これらのフィールドの詳細については、[Azure AD Graph API のエンティティ リファレンス](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity)を参照してください。

この POST 要求が `B2CGraphClient.SendGraphPostRequest(...)` にどのように構成されているかを確認できます。

- アクセス トークンを要求の `Authorization` ヘッダーにアタッチします。
- `api-version=beta` を設定します。
- JSON ユーザー オブジェクトを要求の本文に含めます。

### コンシューマー ユーザー アカウントの更新

ユーザー オブジェクトの更新は、ユーザー オブジェクトの作成とよく似ていますが、更新では HTTP PATCH 動詞を使用します。

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

JSON ファイルを新しいデータで更新し、B2CGraphClient を使用して次のコマンドのいずれかを実行することで、ユーザーを更新できます。

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```
	
`B2CGraphClient.SendGraphPatchRequest(...)` メソッドを調べて、この要求がどのように送信されるかの詳細を確認してください。

### ユーザーの削除

ユーザーの削除は単純な処理です。HTTP DELETE 動詞を使用して、適切なオブジェクト ID を持つ URL を構築するだけです。

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

例を確認するには、次のコマンドを実行し、コンソールに出力される DELETE 要求の結果を観察します。

```
> B2C Delete-User <object-id-of-user>
```

`B2CGraphClient.SendGraphDeleteRequest(...)` メソッドを調べて、この要求がどのように送信されるかの詳細を確認してください。

ユーザー管理に加え、Azure AD Graph API で実行できるアクションは他にも多数あります。「[Azure AD Graph REST API reference (Azure AD Graph REST API リファレンス)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)」に、各アクションの詳細が要求の例と共に説明されています。


## カスタム属性の使用

ほぼすべてのコンシューマー アプリケーションは、何らかの種類のカスタム ユーザー プロファイル情報を格納する必要があります。これを行う 1 つの方法は、B2C ディレクトリの中にカスタム属性を定義することです。これにより、その属性をユーザー オブジェクトの他のプロパティと同じように処理できます。属性の更新、属性の削除、属性によるクエリの実行、属性をサインイン トークンの中でクレームとして送信することができます。

B2C ディレクトリの中にカスタム属性を定義するには、「[Azure Active Directory B2C プレビュー: カスタム属性を使用してコンシューマーに関する情報を収集する](active-directory-b2c-reference-custom-attr.md)」を参照してください。

B2C ディレクトリ内に定義されたカスタム属性は、B2CGraphClient を使用して表示できます。

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

これらの関数の出力によって、各カスタム属性の詳細が明らかになります。

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

ユーザー オブジェクトのプロパティとして、`extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number` などの完全名を使用できます。`.json` ファイルを、新しいプロパティと値を使用して更新し、次を実行します。

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

これで終わりです。 B2CGraphClient を使用して B2C ディレクトリのユーザーをプログラムで管理できるサービス アプリケーションが作成されました。このアプリケーションは、独自のアプリケーション ID を使用して、Azure AD Graph API に対する認証を行い、client\_secret を使用してトークンを取得します。独自のアプリケーションにこの機能を組み込むときは、B2C アプリに関するいくつかの重要な点に注意してください。

- ディレクトリの適切なアクセス許可をアプリケーションに付与する必要があります。
- 現時点では、アクセス トークンを取得するには ADAL v2 を使用する必要があります (または、ライブラリなしでプロトコル メッセージを直接送信します)。
- Graph API を呼び出すときは、[`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) を使用します。
- コンシューマー ユーザーの作成と更新を行うときは、上記で説明したいくつかの必須プロパティがあります。

Graph API を使用した B2C ディレクトリに対する操作に関して、ご質問やご要望がある場合は、いつでもご遠慮なくお知らせください。 記事に対するコメントや GitHub リポジトリのコード例の問題をお寄せください。

<!---HONumber=Sept15_HO3-->