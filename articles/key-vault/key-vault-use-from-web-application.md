---
title: Web アプリからの Azure Key Vault の使用チュートリアル | Microsoft Docs
description: このチュートリアルを使用すると、Web アプリケーションから Azure Key Vault を使用する方法について学習できます。
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: barclayn
ms.openlocfilehash: ff59e39e54433aa673b093e2ee1fbe8c74010e54
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171325"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>チュートリアル: Web アプリから Azure Key Vault を使用する

このチュートリアルを使用すると、Azure の Web アプリケーションから Azure Key Vault を使用する方法について学習できます。 Web アプリで使うために Azure Key Vault のシークレットにアクセスするプロセスを示します。 その後、このプロセスで、クライアント シークレットではなく証明書を使います。 このチュートリアルは、Azure 上での Web アプリケーション作成の基本を理解している Web 開発者向けに設計されています。

このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * アプリケーションの設定を web.config ファイルに追加する
> * アクセス トークンを取得するメソッドを追加する
> * アプリケーションの起動時にトークンを取得する
> * 証明書で認証を行う

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

* Azure Key Vault のシークレットへの URI
* Key Vault にアクセスできる Azure Active Directory に登録された Web アプリケーションのクライアント ID とクライアント シークレット
* Web アプリケーション。 このチュートリアルでは、Web アプリとして Azure に展開される ASP.NET MVC アプリケーションの手順を示します。

「[Azure Key Vault の概要](key-vault-get-started.md)」の手順を完了して、シークレット、クライアント ID、クライアント シークレットの URI を取得し、アプリケーションを登録します。 この Web アプリは、コンテナーにアクセスします。また、Azure Active Directory に登録する必要があります。 Key Vault へのアクセス権も必要です。 ない場合は、概要のチュートリアルにあるアプリケーションの登録に関するトピックに戻り、記載されている手順を繰り返します。 Azure Web Apps の作成について詳しくは、「[Web Apps の概要](../app-service/app-service-web-overview.md)」をご覧ください。

このサンプルは、Azure Active Directory ID の手動プロビジョニングに依存します。 ただし、[マネージド サービス ID (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview) を代わりに使用する必要があります。 MSI は、Azure AD の ID を自動的にプロビジョニングできます。 詳しくは、[GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) のサンプルと、関連する[App Service と Functions での MSI に関するチュートリアル](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity)をご覧ください。 また、Key Vault 固有の [MSI チュートリアル](tutorial-web-application-keyvault.md)もご覧ください。


## <a id="packages"></a>NuGet パッケージを追加する

Web アプリケーションでインストールしておく必要のあるパッケージは 2 つあります。

* Active Directory 認証ライブラリ: Azure Active Directory と対話してユーザー ID を管理するためのメソッドがあります。
* Azure Key Vault ライブラリ: Azure Key Vault と対話するためのメソッドがあります。

どちらのパッケージも、パッケージ マネージャー コンソールで Install-Package コマンドを使用してインストールできます。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>web.config を変更する

次のように、web.config ファイルに追加する必要のある 3 つのアプリケーション設定があります。 セキュリティ レベルを上げるために、Azure portal で実際の値を追加する予定です。

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>アクセス トークンを取得するメソッドを追加する

Key Vault API を使用するには、アクセス トークンが必要です。 Key Vault クライアントによって Key Vault API の呼び出しが処理されますが、アクセス トークンを取得する関数を指定する必要があります。 Azure Active Directory からアクセス トークンを取得するコードの例を次に示します。 このコードはアプリケーション内の任意の場所に配置できます。 Utils クラスまたは EncryptionHelper クラスを追加します。  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>アプリケーション起動時のシークレットの取得

ここで、Key Vault API を呼び出してシークレットを取得するコードが必要になります。 次のコードは、それが必要になる前に呼び出されれば、どこに配置してもかまいません。 ここでは、このコードを Global.asax の Application Start イベントに配置しました。これにより、コードは起動時に 1 回実行され、アプリケーションでシークレットを使用できるようになります。

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Azure portal でアプリの設定を追加する (省略可能)

Azure Web アプリでは、Azure portal でアプリ設定の実際の値を追加できます。 これにより、実際の値は web.config ファイルに存在しなくなりますが、個別のアクセス制御機能があるポータルによって保護されます。 これらの値は、web.config で入力した値の代わりに使用されます。名前が同じであるかどうかを確認してください。

![Azure Portal に表示されるアプリケーション設定][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>クライアント シークレットではなく、証明書を使用して認証する

クライアント ID とクライアント シークレットを使って Azure AD アプリの認証を行う方法がわかったので、クライアント ID と証明書を使用してみます。 Azure Web アプリで証明書を使用するには、次の手順のようにします。

1. 証明書を取得または作成する
2. 証明書を Azure AD アプリケーションに関連付ける
3. 証明書を使用する Web アプリにコードを追加する
4. 証明書を Web アプリに追加する

### <a name="get-or-create-a-certificate"></a>証明書を取得または作成する

 このチュートリアル用のテスト証明書を作成します。 次に示すのは、自己署名証明書を作成するためのスクリプトです。 証明書ファイルの作成先となるディレクトリを変更します。  証明書の開始日と終了日には、現在の日付の 1 年後の日付を使用できます。

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = "c:\data\KVWebApp.pfx" 
$CerFilePath = "c:\data\KVWebApp.cer" 
$DNSName = "MyComputer.Contoso.com" 
$Password ="MyPassword" 
$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\LocalMachine\My" -NotBefore 05/15/2018 -NotAfter 05/15/2019 
Export-PfxCertificate -cert $cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $cert -FilePath $CerFilePath 
```

終了日と .pfx のパスワードをメモしておきます (この例では、2019 年 5 月 15 日と MyPassword)。 次のスクリプトに必要です。 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>証明書を Azure AD アプリケーションに関連付ける

証明書を作成したので、それを Azure AD アプリケーションに関連付ける必要があります。 関連付けは PowerShell を使って行うことができます。 次のコマンドを実行して、証明書を Azure AD アプリケーションに関連付けます。

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

これらのコマンドを実行した後、Azure AD でアプリケーションを確認できます。 アプリの登録を検索するときは、検索ダイアログで [すべてのアプリ] ではなく **[マイ アプリ]** を選択します。 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>証明書を使用する Web アプリにコードを追加する

ここで証明書にアクセスするコードを Web アプリに追加し、認証に使用します。 

まず、証明書にアクセスするコードがあります。StoreLocation は LocalMachine ではなく CurrentUser であることに注意してください。 また、ここではテスト証明書を使用しているため、Find メソッドに対して 'false' を指定しています。

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



次に、CertificateHelper を使用し、認証に必要な ClientAssertionCertificate を作成するコードがあります。

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

アクセス トークンを取得する新しいコードを次に示します。 このコードで、前の例にある GetToken メソッドを置き換えます。 便宜上、別の名前を指定しています。 使いやすくするために、このコードをすべて Web アプリ プロジェクトの Utils クラスに配置してあります。

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



最後のコード変更は、Application_Start メソッドで行います。 最初に、GetCert() メソッドを呼び出して ClientAssertionCertificate を読み込む必要があります。 次に、新しい KeyVaultClient を作成するときに指定するコールバック メソッドを変更します。 このコードで、前の例のコードを置き換えます。

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Azure portal を使用して証明書を Web アプリに追加する

証明書を Web アプリに追加する手順は、簡単な 2 段階のプロセスです。 まず Azure Portal に移動し、Web アプリに移動します。 Web アプリの [設定] で、**[SSL 設定]** のエントリをクリックします。 開いたら、前の例で作成した証明書 KVWebApp.pfx をアップロードします。 pfx のパスワードを憶えておいてください。

![Azure Portal での Web アプリへの証明書の追加][2]

最後にWeb アプリへアプリケーション設定を、名前 "WEBSITE\_LOAD\_CERTIFICATES" 値 "*" と追加する必要があります。 この手順では、すべての証明書が読み込まれたことを確認します。 アップロードした証明書のみを読み込む場合は、そのサムプリントのコンマ区切りリストを入力できます。


## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要なくなったら、チュートリアルで使用したアプリ サービス、キー コンテナー、および Azure AD アプリケーションを削除します。  


## <a id="next"></a>次のステップ
> [!div class="nextstepaction"]
>[Azure Key Vault Management API リファレンス](/dotnet/api/overview/azure/keyvault/management).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 