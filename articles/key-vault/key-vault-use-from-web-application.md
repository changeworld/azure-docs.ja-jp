<properties 
	pageTitle="Web アプリケーションからの Azure Key Vault の使用 | 概要" 
	description="このチュートリアルを使用すると、Web アプリケーションから Azure Key Vault を使用する方法について学習できます。" 
	services="key-vault" 
	documentationCenter="" 
	authors="adamhurwitz" 
	manager=""
	tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# Web アプリケーションからの Azure Key Vault の使用 #

## はじめに  
このチュートリアルを使用すると、Azure の Web アプリケーションから Azure Key Vault を使用する方法について学習できます。ここでは、Web アプリケーションで使用できるように Azure Key Vault からシークレットにアクセスするプロセスについて説明します。

**推定所要時間:** 15 分


Azure Key Vault の概要については、「[Azure Key Vault とは](key-vault-whatis.md)」をご覧ください。

## 前提条件 

このチュートリアルを完了するには、以下が必要です。

- Azure Key Vault のシークレットへの URI
- Key Vault にアクセスできる Azure Active Directory に登録された Web アプリケーションのクライアント ID とクライアント シークレット
- Web アプリケーション。Web アプリとして Azure にデプロイされた ASP.NET MVC アプリケーションの手順について説明します。 

> [AZURE.NOTE]このチュートリアルでは、[Azure Key Vault の概要](key-vault-get-started.md)に関するページに記載されている手順を完了している必要があります。これにより、シークレットへの URI と、Web アプリケーションのクライアント ID およびクライアント シークレットを入手できます。

Key Vault にアクセスする Web アプリケーションは、Azure Active Directory に登録されていて、Key Vault へのアクセス権が付与されているアプリケーションです。そうでない場合は、概要のチュートリアルにあるアプリケーションの登録に関するトピックに戻り、記載されている手順を繰り返します。

このチュートリアルは、Azure 上での Web アプリケーション作成の基本を理解している Web 開発者向けに設計されています。Azure Web Apps の詳細については、[Web Apps の概要](../app-service-web-overview.md)に関するページを参照してください。



## <a id="packages"></a>NuGet パッケージの追加 ##
Web アプリケーションをインストールしておく必要のあるパッケージは 3 つあります。

- Active Directory 認証ライブラリ: Azure Active Directory と対話してユーザー ID を管理するためのメソッドが含まれています。
- Azure Key Vault ライブラリ: Azure Key Vault と対話するためのメソッドが含まれています。


これらの 3 つのパッケージはすべて、パッケージ マネージャー コンソールで Install-Package コマンドを使用するとインストールできます。

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>web.config の変更 ##
次のように、web.config ファイルに追加する必要のある 3 つのアプリケーション設定があります。

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


今後 Azure の Web アプリとしてアプリケーションをホストしない場合は、web.config に実際のクライアント ID、クライアント シークレット、およびシークレットの URI の値を追加する必要があります。追加しない場合は、これらのダミーの値をそのままにしてください。セキュリティ レベルを上げるために、Azure ポータルで実際の値を追加する予定です。


## <a id="gettoken"></a>アクセス トークンを取得するメソッドの追加 ##
Key Vault API を使用するには、アクセス トークンが必要です。Key Vault クライアントによって Key Vault API の呼び出しが処理されますが、アクセス トークンを取得する関数を指定する必要があります。

Azure Active Directory からアクセス トークンを取得するコードを次に示します。このコードはアプリケーション内の任意の場所に配置できます。Utils クラスまたは EncryptionHelper クラスを追加します。

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

## <a id="appstart"></a>アプリケーション起動時のシークレットの取得 ##
ここで、Key Vault API を呼び出してシークレットを取得するコードが必要になります。次のコードは、それが必要になる前に呼び出されれば、どこに配置してもかまいません。ここでは、このコードを Global.asax の Application Start イベントに配置しました。これにより、コードは起動時に 1 回実行され、アプリケーションでシークレットを使用できるようになります。

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Azure ポータルのアプリ設定の追加 (省略可能) ##
Azure の Web アプリがある場合は、Azure ポータルでアプリ設定の実際の値を追加できます。これにより、実際の値は web.config ファイルに存在しなくなりますが、個別のアクセス制御機能があるポータルによって保護されます。これらの値は、web.config で入力した値の代わりに使用されます。名前が同じであるかどうかを確認してください。

![Application Settings displayed in Azure Portal][1]



## <a id="next"></a>次のステップ ##


プログラミング リファレンスについては、[Azure Key Vault C# クライアント API リファレンス](https://msdn.microsoft.com/library/azure/dn903628.aspx)に関するページを参照してください。


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=July15_HO2-->