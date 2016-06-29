<properties
	pageTitle=".NET 向け Azure CDN ライブラリの概要 | Microsoft Azure"
	description="Visual Studio を使用して Azure CDN を管理するための .NET アプリケーションを記述する方法について説明します。"
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="casoper"/>

# .NET 向け Azure CDN ライブラリの概要

[.NET 向け Azure CDN ライブラリ](https://msdn.microsoft.com/library/mt657769.aspx)を使用すると、CDN プロファイルとエンドポイントの作成と管理を自動化できます。このチュートリアルでは、単純な .NET コンソール アプリケーションを作成しながら、使用可能な操作のいくつかを紹介します。このチュートリアルは、.NET 向け Azure CDN ライブラリのすべての側面を詳細に説明することを目的としていません。

このチュートリアルを完了するには、Visual Studio 2015 が必要です。[Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) は、自由にダウンロードできます。

このチュートリアルの完成したサンプルは、[ここ](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)にあります。

## 準備

CDN 管理コードを記述する前に、いくつか準備作業を行う必要があります。最初に、このチュートリアルで作成する CDN プロファイルを格納するリソース グループを作成します。次に、Azure Active Directory をセットアップして、アプリケーションの認証を提供します。その後、リソース グループにアクセス許可を適用して、Azure AD テナントの承認されたユーザーのみが CDN プロファイルを操作できるようにします。

### リソース グループの作成

1. [Azure ポータル](https://portal.azure.com)にログインします。

2. 左上の **[新規]** ボタンをクリックし、**[管理]**、**[リソース グループ]** の順にクリックします。
	
	![Creating a new resource group](./media/cdn-app-dev-net/cdn-new-rg-1.png)

3. リソース グループに *CdnConsoleTutorial* という名前を付けます。サブスクリプションを選択し、近くの場所を選択します。必要に応じて **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、リソース グループをポータルのダッシュボードにピン留めします。これにより、後で見つけるのが容易になります。必要な項目を選択したら、**[作成]** をクリックします。

	![Naming the resource group](./media/cdn-app-dev-net/cdn-new-rg-2.png)

4. ダッシュボードにリソース グループをピン留めしていない場合は、**[参照]**、**[リソース グループ]** の順にクリックして、作成したリソース グループを見つけることができます。このリソース グループをクリックして開きます。**サブスクリプション ID** をメモしておきます。この情報は後で必要になります。

	 ![Naming the resource group](./media/cdn-app-dev-net/cdn-subscription-id.png)

### Azure AD アプリケーションの作成

Azure Active Directory でのアプリの認証には、個別ユーザーか、サービス プリンシパルかという 2 つのアプローチがあります。サービス プリンシパルは、Windows のサービス アカウントと似ています。CDN プロファイルとやり取りするアクセス許可を特定のユーザーに付与する代わりに、サービス プリンシパルにアクセス許可を付与します。通常、サービス プリンシパルは、自動化された非対話型プロセスに使用されます。このチュートリアルでは対話型コンソール アプリを作成していますが、サービス プリンシパルのアプローチに注目します。

サービス プリンシパルの作成は、Azure Active Directory アプリケーションの作成を含むいくつかの手順で構成されます。これを行うために、[このチュートリアルに従います](../resource-group-create-service-principal-portal.md)。

> [AZURE.IMPORTANT] [リンク先のチュートリアル](../resource-group-create-service-principal-portal.md)のすべての手順を実行してください。説明に従って正確に手順を完了することが*非常に重要*です。**テナント ID**、**テナントのドメイン名** (ドメイン カスタム ドメインが指定されている場合を除き、通常は *.onmicrosoft.com* ドメイン)、**クライアント ID**、**クライアントの認証キー**をメモしておきます。これらの情報は後で必要になります。**クライアント ID** と**クライアントの認証キー**の取り扱いには特に注意してください。これらの資格情報を使用すると、だれでもサービス プリンシパルとして操作を実行できます。
> 	
> 「[マルチテナント アプリケーションを構成する](../resource-group-create-service-principal-portal.md#configure-multi-tenant-application)」の手順で、**[いいえ]** を選択します。
> 
> 「[アプリケーションをロールに割り当てる](../resource-group-create-service-principal-portal.md#assign-application-to-role)」の手順で、前に作成したリソース グループ *CdnConsoleTutorial* を使用します。ただし、**[閲覧者]** ロールではなく、**[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールを割り当てます。アプリケーションにリソース グループの **[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールを割り当てたら、このチュートリアルに戻ります。

サービス プリンシパルを作成し、**[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールを割り当てた後、リソース グループの **[ユーザー]** ブレードの表示は次のようになります。

![Users blade](./media/cdn-app-dev-net/cdn-service-principal.png)


### 対話型ユーザー認証

サービス プリンシパルの代わりに対話型の個別ユーザー認証を行う場合でも、そのプロセスはサービス プリンシパルを使用する場合とよく似ています。実際に行う手順は同じですが、いくつか細かい点が異なります。

>[AZURE.IMPORTANT] 次の手順は、サービス プリンシパルの代わりに個別ユーザー認証を行う場合にのみ使用してください。

1. アプリケーションを作成するときに、**Web アプリ**ではなく**ネイティブ アプリケーション**の項目を選択します。 
	
	![Native application](./media/cdn-app-dev-net/cdn-native-application.png)
	
2. 次のページで、**リダイレクト URI** の入力を求められます。URI は検証されません。入力した内容を覚えておいてください。この情報は後で必要になります。

3. **クライアント認証キー**を作成する必要はありません。

4. サービス プリンシパルを **CDN Profile Contributor (CDN プロファイルの共同作成者)** ロールに割り当てる代わりに、個別ユーザーまたはグループに割り当てます。この例では、*CDN Demo User* を **[CDN Profile Contributor (CDN プロファイルの共同作成者)]** ロールに割り当てています。
	
	![Individual user access](./media/cdn-app-dev-net/cdn-aad-user.png)


## プロジェクトを作成し、NuGet パッケージを追加する

これまでの手順で、CDN プロファイルのリソース グループを作成し、そのグループ内の CDN プロファイルとエンドポイントを管理するための Azure AD アプリケーション アクセス許可を割り当てました。この段階で、アプリケーションの作成を開始できます。

Visual Studio 2015 で、**[ファイル]**、**[新規]**、**[プロジェクト]** の順にクリックして、新しいプロジェクト ダイアログを開きます。左側のウィンドウで **[Visual C#]** を展開し、**[Windows]** を選択します。中央のウィンドウで **[コンソール アプリケーション]** をクリックします。プロジェクトに名前を付け、**[OK]** をクリックします。

![新しいプロジェクト](./media/cdn-app-dev-net/cdn-new-project.png)

このプロジェクトでは、NuGet パッケージに含まれているいくつかの Azure ライブラリを使用します。それでは、ライブラリをプロジェクトに追加していきましょう。

1. **[ツール]** メニュー、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

	![Manage Nuget Packages](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. パッケージ マネージャー コンソールで、次のコマンドを実行して、**Active Directory Authentication Library (ADAL)** をインストールします。

	`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. 次のコマンドを実行して、**Azure CDN Management Library** をインストールします。

	`Install-Package Microsoft.Azure.Management.Cdn`

## ディレクティブ、定数、main メソッド、およびヘルパー メソッド

ここでは、プログラムの基本的な構造を記述します。

1. [Program.cs] タブに戻り、最上部の `using` ディレクティブを次の内容で置き換えます。

	```
	using System;
	using System.Collections.Generic;
	using Microsoft.Azure.Management.Cdn;
	using Microsoft.Azure.Management.Cdn.Models;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Rest;
	```

2. 次に、メソッドで使用するいくつかの定数を定義します。`Program` クラスの `Main` メソッドの前に次のコードを追加します。必要に応じて、**&lt;山かっこ&gt;** などのプレースホルダーを自分の環境に合わせて置き換えてください。

	```
	//Tenant app constants
	private const string clientID = "<YOUR CLIENT ID>";
	private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
	private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

	//Application constants
	private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
	private const string profileName = "CdnConsoleApp";
	private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
	private const string resourceGroupName = "CdnConsoleTutorial";
	private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
	```

3. さらに、クラス レベルで次の 2 つの変数を定義します。これらの変数は、後でプロファイルとエンドポイントが既に存在しているかどうかを判定するために使用します。

	```
	static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
	```

4.  次のように、`Main` メソッドを置き換えます。

	```
	static void Main(string[] args)
	{
		//Get a token
		AuthenticationResult authResult = GetAccessToken();

		// Create CDN client
		CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
			{ SubscriptionId = subscriptionId };

		ListProfilesAndEndpoints(cdn);

		// Create CDN Profile
		CreateCdnProfile(cdn);

		// Create CDN Endpoint
		CreateCdnEndpoint(cdn);
		
		Console.WriteLine();

		// Purge CDN Endpoint
		PromptPurgeCdnEndpoint(cdn);

		// Delete CDN Endpoint
		PromptDeleteCdnEndpoint(cdn);

		// Delete CDN Profile
		PromptDeleteCdnProfile(cdn);

		Console.WriteLine("Press Enter to end program.");
		Console.ReadLine();
	}
	```

5. 他のいくつかのメソッドでは、ユーザーが "はい/いいえ" で回答する質問を行います。この処理を少し簡単にするために、次のメソッドを追加します。

	```
	private static bool PromptUser(string Question)
	{
		Console.Write(Question + " (Y/N): ");
		var response = Console.ReadKey();
		Console.WriteLine();
		if (response.Key == ConsoleKey.Y)
		{
			return true;
		}
		else if (response.Key == ConsoleKey.N)
		{
			return false;
		}
		else
		{
			// They're not pressing Y or N.  Let's ask them again.
			return PromptUser(Question);
		}
	}
	```

これで、プログラムの基本的な構造が作成されました。次に、`Main` メソッドによって呼び出されるメソッドを作成します。

## 認証

Azure CDN Management Library を使用するには、サービス プリンシパルを認証し、認証トークンを取得する必要があります。このメソッドは、ADAL を使用してトークンを取得します。

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority); 
	ClientCredential credential = new ClientCredential(clientID, clientSecret);
	AuthenticationResult authResult = 
		authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

	return authResult;
}
```

個別ユーザー認証を使用する場合の `GetAccessToken` メソッドは多少異なります。

>[AZURE.IMPORTANT] このコード サンプルは、サービス プリンシパルの代わりに個別ユーザー認証を行う場合にのみ使用してください。

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority);
	AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
		clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

	return authResult;
}
```

`<redirect URI>` は、アプリケーションを Azure AD に登録するときに入力したリダイレクト URI に置き換えてください。

## CDN プロファイルとエンドポイントのリストの取得

これで、CDN の操作を実行する準備ができました。このメソッドでは、まずリソース グループ内のすべてのプロファイルとエンドポイントのリストを取得し、定数に指定されているプロファイルとエンドポイントの名前に一致するものが見つかった場合は重複を作成しないようにその名前を記録します。

```
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
	// List all the CDN profiles in this resource group
	var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
	foreach (Profile p in profileList)
	{
		Console.WriteLine("CDN profile {0}", p.Name);
		if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
		{
			// Hey, that's the name of the CDN profile we want to create!
			profileAlreadyExists = true;
		}

		//List all the CDN endpoints on this CDN profile
		Console.WriteLine("Endpoints:");
		var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
		foreach (Endpoint e in endpointList)
		{
			Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
			if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
			{
				// The unique endpoint name already exists.
				endpointAlreadyExists = true;
			}
		}
		Console.WriteLine();
	}
}
```

## CDN プロファイルとエンドポイントの作成

次に、プロファイルを作成します。

```
private static void CreateCdnProfile(CdnManagementClient cdn)
{
	if (profileAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating profile {0}.", profileName);
		ProfileCreateParameters profileParms =
			new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
		cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
	}
}
```

プロファイルを作成した後は、エンドポイントを作成します。

```
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
	if (endpointAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
		EndpointCreateParameters endpointParms =
			new EndpointCreateParameters()
			{
				Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
				IsHttpAllowed = true,
				IsHttpsAllowed = true,
				Location = resourceLocation
			};
		cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
	}
}
```

>[AZURE.NOTE] 上の例では、ホスト名 `www.contoso.com` を持つ *Contoso* という名前のオリジンをエンドポイントに割り当てています。オリジンのホスト名を指すようにこれを変更する必要があります。

## エンドポイントの消去

エンドポイントが作成されたと仮定して、プログラムで実行する一般的なタスクの 1 つに、エンドポイントの内容を消去することがあります。

```
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
	if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
	{
		Console.WriteLine("Purging endpoint. Please wait...");
		cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

>[AZURE.NOTE] 上記の例の文字列 `/*` は、エンドポイント パスのルートにあるものをすべて消去することを示します。これは、Azure ポータルの "消去" ダイアログで **[すべて消去]** を選択するのと同じです。`CreateCdnProfile` メソッドでは、`Sku = new Sku(SkuName.StandardVerizon)` というコードを使用して **Azure CDN from Verizon** としてプロファイルを作成しているため、このコードは動作します。ただし、**Azure CDN from Akamai** プロファイルでは **[すべて消去]** がサポートされません。このチュートリアルで使用するプロファイルが Akamai プロファイルであった場合は、消去する特定のパスを含める必要があります。

## CDN プロファイルとエンドポイントの削除

最後のメソッドでは、エンドポイントとプロファイルを削除します。

```
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
	{
		Console.WriteLine("Deleting endpoint. Please wait...");
		cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
	{
		Console.WriteLine("Deleting profile. Please wait...");
		cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

## プログラムの実行

Visual Studio の **[開始]** をクリックして、プログラムをコンパイルおよび実行します。

![Program running](./media/cdn-app-dev-net/cdn-program-running-1.png)

プログラムによって上記のプロンプトが表示された時点で、Azure ポータルでリソース グループに戻り、このプロファイルが作成されたことを確認できます。

![成功です。](./media/cdn-app-dev-net/cdn-success.png)

次に、プログラムの残りの部分を実行するかどうかを確認できます。

![Program completing](./media/cdn-app-dev-net/cdn-program-running-2.png)

## 次のステップ

このチュートリアルの完成したプロジェクトを確認するには、[サンプルをダウンロード](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)してください。

Azure CDN Management Library for .NET に関連するドキュメントについては、[MSDN のリファレンス](https://msdn.microsoft.com/library/mt657769.aspx)を参照してください。

<!---HONumber=AcomDC_0615_2016-->