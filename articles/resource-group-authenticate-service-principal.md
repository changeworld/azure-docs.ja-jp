<properties
   pageTitle="Azure リソース マネージャーでのサービス プリンシパルの認証 | Microsoft Azure"
   description="ロール ベースのアクセス制御を使用して、サービス プリンシパルにアクセス権限を付与し、それを認証する方法について説明します。PowerShell と Azure CLI を使用してこれらのタスクを実行する方法を示します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# Azure リソース マネージャーでのサービス プリンシパルの認証

このトピックでは、サブスクリプション内の他のリソースにアクセスできるように、サービス プリンシパル (自動プロセス、アプリケーション、サービスなど) を許可する方法を示します。Azure リソース マネージャーでは、ロール ベースのアクセス制御を使用して、許可されたアクションをサービス プリンシパルに付与し、そのサービス プリンシパルを認証することができます。

このトピックでは、Azure PowerShell または Mac、Linux および Windows 用の Azure CLI を使用して、アプリケーションとサービス プリンシパルを作成したり、サービス プリンシパルにロールを割り当てたり、サービス プリンシパルとして認証する方法を示します。Azure PowerShell がインストールされていない場合、[Azure PowerShell のインストールと構成の方法](./powershell-install-configure.md)を参照してください。Azure CLI がインストールされていない場合は、「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。ポータルを使用してこれらの手順を実行する方法の詳細については、「[ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する](resource-group-create-service-principal-portal.md)」を参照してください。

## 概念
1. Azure Active Directory - クラウドの ID およびアクセス管理サービス。詳細については、「[Azure Active Directory とは](active-directory/active-directory-whatis.md)」を参照してください。
2. サービス プリンシパル - 他のリソースにアクセスする必要がある、ディレクトリ内のアプリケーションのインスタンス。
3. Active Directory アプリケーション - AAD に対してアプリケーションを特定するディレクトリ レコード。

アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

このトピックでは、Active Directory アプリケーションを作成し、そのアプリケーションを認証する 4 つの方法を示します。方法は、認証用にパスワードまたは証明書を使用するか、または PowerShell または Azure CLI を使用するかによって異なります。方法は次のとおりです。

- [パスワードで認証する: PowerShell](#authenticate-with-password---powershell)
- [証明書で認証する: PowerShell](#authenticate-with-certificate---powershell)
- [パスワードで認証する: Azure CLI](#authenticate-with-password---azure-cli)
- [証明書で認証する: Azure CLI](#authenticate-with-certificate---azure-cli)

## パスワードで認証する: PowerShell

このセクションでは、Azure Active Directory アプリケーションのサービス プリンシパルを作成し、ロールをサービス プリンシパルに割り当て、アプリケーションの ID とパスワードを指定することでサービス プリンシパルとして認証する手順を実行します。

1. ご使用のアカウントにサインインします。

        PS C:\> Login-AzureRmAccount

1. **New-AzureRmADApplication** コマンドを実行して、新しい Active Directory アプリケーションを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     新しいアプリケーション オブジェクトを調べます。サービス プリンシパルの作成、ロールの割り当て、およびアクセス トークンの取得には、**ApplicationId** プロパティが必要です。

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

3. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Active Directory アプリケーションとそのアプリケーション用のサービス プリンシパルの作成が完了しました。サービス プリンシパルをロールに割り当てました。これで、サービス プリンシパルとして操作できるよう、サービス プリンシパルとしてログインする必要があります。このトピックには、3 つのオプションがあります。

- [PowerShell を使用して資格情報を手動で渡す](#manually-provide-credentials-through-powershell)
- [自動化された PowerShell スクリプトから資格情報を渡す](#provide-credentials-through-automated-powershell-script)
- [アプリケーションのコードから資格情報を渡す](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### PowerShell を使用して資格情報を手動で渡す

必要に応じて使用するスクリプトまたはコマンドを実行して、手動でサービス プリンシパルの資格情報を渡すことができます。

1. **Get-credential** コマンドを実行して、ユーザーの資格情報を含む新しい**PSCredential** オブジェクトを作成します。

        PS C:\> $creds = Get-Credential

2. 資格情報を入力するためのプロンプトが表示されます。ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** または **IdentifierUris** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

     ![資格情報を入力][1]

3. ロールの割り当てが作成されたサブスクリプションを取得します。このサブスクリプションは、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を取得するために使用されます。

        PS C:\> $subscription = Get-AzureRmSubscription

     現在選択されているサブスクリプション以外のサブスクリプション内にロールの割り当てを作成した場合、**SubscriptoinId** または **SubscriptionName** パラメーターを指定して、別のサブスクリプションを取得できます。

4. **Login-AzureRmAccount** コマンドレットを使用して、サービス プリンシパルとしてログインします。ただし、資格情報オブジェクトを提供し、このアカウントは、サービス プリンシパルであることを指定します。

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

<a id="provide-credentials-through-automated-powershell-script" />
### 自動化された PowerShell スクリプトから資格情報を渡す

このセクションでは、資格情報を手動で入力せずに、サービス プリンシパルとしてログインする方法を示します。パスワードは、Key Vault から取得されるために、手動で入力する必要はありません。

> [AZURE.NOTE] パスワードはテキストとして公開されるため、PowerShell スクリプトにパスワードを直接含めるのは危険です。代わりに、Key Vault のようなサービスを使用してパスワードを格納し、スクリプトの実行時にそれを取得します。

これらの手順では、パスワードを格納する Key Vault とシークレットが設定済みであると仮定しています。テンプレートを使用して Key Vault とシークレットをデプロイする方法については、「[Key Vault テンプレートの形式]()」を参照してください。Key Vault の詳細については、「[Azure Key Vault の概要](./key-vault/key-vault-get-started.md)」を参照してください。

1. Key Vault からパスワードを取得します (以下の例では、シークレットは **appPassword** という名前で格納されています) です。

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. Active Directory アプリケーションを取得します。ログイン時にアプリケーション ID が必要です。

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. アプリケーション ID とパスワードを資格情報として渡し、新しい **PSCredential** オブジェクトを作成します。

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. ロールの割り当てが作成されたサブスクリプションを取得します。このサブスクリプションは、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を取得するために後に使用されます。

        PS C:\> $subscription = Get-AzureRmSubscription

     現在選択されているサブスクリプション以外のサブスクリプション内にロールの割り当てを作成した場合、**SubscriptoinId** または **SubscriptionName** パラメーターを指定して、別のサブスクリプションを取得できます。

5. **Login-AzureRmAccount** コマンドレットを使用して、サービス プリンシパルとしてログインします。ただし、資格情報オブジェクトを提供し、このアカウントは、サービス プリンシパルであることを指定します。
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

<a id="provide-credentials-through-code-in-an-application" />
### アプリケーションのコードから資格情報を渡す

.NET アプリケーションから認証するには、次のコードを含めます。Active Directory アプリケーションにはアプリケーション ID が、サブスクリプションには、パスワードおよびテナント ID が必要です。アクセス トークンを取得すると、サブスクリプションのリソースを使用できます。

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }


## 証明書で認証する: PowerShell

このセクションでは、Azure Active Directory アプリケーションのサービス プリンシパルを作成し、ロールをサービス プリンシパルに割り当て、証明書を指定することでサービス プリンシパルとして認証する手順を実行します。このトピックでは、証明書が発行されているものとします。

証明書を使用するための 2 つの方法を示します。キーの資格情報とキーの値です。いずれかの方法を使用できます。

最初に、後にアプリケーションを作成するときに使用するいくつかの値を PowerShell に設定する必要があります。

1. ご使用のアカウントにサインインします。

        PS C:\> Login-AzureRmAccount

1. いずれの方法でも、証明書から X509Certificate2 オブジェクトを作成し、キーの値を取得します。証明書のパスとその証明書のパスワードを使用します。

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. キーの資格情報を使用する場合、キーの資格情報オブジェクトを作成し、その値を前の手順の `$keyValue` に設定します。次の例には、アセンブリから型を追加する `Add-Type` の呼び出しが含まれます。この例のパスは、ユーザーのパスと似ているはずですが、若干異なる場合もあります。

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. ディレクトリにアプリケーションを作成します。最初のコマンドはキーの値を使用する方法を示します。

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    または、2 番目の例を使用し、キーの資格情報を割り当てます。

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    新しいアプリケーション オブジェクトを調べます。サービス プリンシパルの作成、ロールの割り当て、およびアクセス トークンの取得には、**ApplicationId** プロパティが必要です。

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

5. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Active Directory アプリケーションとそのアプリケーション用のサービス プリンシパルの作成が完了しました。サービス プリンシパルをロールに割り当てました。これで、サービス プリンシパルとして操作できるよう、サービス プリンシパルとしてログインする必要があります。このトピックでは、2 つのオプションがあります。

- [自動化された PowerShell スクリプトから証明書を渡す](#provide-certificate-through-automated-powershell-script)
- [アプリケーションのコードから証明書を渡す](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### 自動化された PowerShell スクリプトから証明書を渡す

1. Active Directory アプリケーションを取得します。ログイン時にアプリケーション ID が必要です。

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. ロールの割り当てが作成されたサブスクリプションを取得します。このサブスクリプションは、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を取得するために後に使用されます。

        PS C:\> $subscription = Get-AzureRmSubscription

     現在選択されているサブスクリプション以外のサブスクリプション内にロールの割り当てを作成した場合、**SubscriptoinId** または **SubscriptionName** パラメーターを指定して、別のサブスクリプションを取得できます。

3. 認証に使用する証明書を取得します。

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. PowerShell で認証するには、証明書の拇印、アプリケーション ID、およびテナント ID を渡します。

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

<a id="provide-certificate-through-code-in-an-application" />
### アプリケーションのコードから証明書を渡す

.NET アプリケーションから認証するには、次のコードを含めます。クライアントを取得すると、サブスクリプションのリソースにアクセスできます。

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred);
    // If using the new resource manager package like "Microsoft.Azure.ResourceManager" version="1.0.0-preview" use below
    var creds = new TokenCredentials(token.AccessToken); 
    // Else if using older package versions like Microsoft.Azure.Management.Resources" version="3.4.0-preview" use below
    // var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds); 
        

## パスワードで認証する: Azure CLI

まず、サービス プリンシパルを作成します。これを行うには、ディレクトリにアプリケーションを作成する必要があります。このセクションでは、ディレクトリに新しいアプリケーションを作成する手順を示します。

1. Azure リソース マネージャー モードに切り替えて、アカウントにサインインします。

        azure config mode arm
        azure login

2. **azure ad app create** コマンドを実行して、新しい Active Directory アプリケーションを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Active Directory アプリケーションが返されます。サービス プリンシパルの作成、ロールの割り当て、およびアクセス トークンの取得には、AppId プロパティが必要です。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. アプリケーションのサービス プリンシパルを作成します。前の手順で返されたアプリケーション ID を入力します。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

4. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Active Directory アプリケーションとそのアプリケーション用のサービス プリンシパルの作成が完了しました。サービス プリンシパルをロールに割り当てました。これで、サービス プリンシパルとして操作できるよう、サービス プリンシパルとしてログインする必要があります。このトピックには、3 つのオプションがあります。

- [資格情報を Azure CLI で手動で渡す](#manually-provide-credentials-through-azure-cli)
- [資格情報を自動化された Azure CLI スクリプトで渡す](#provide-credentials-through-automated-azure-cli-script)
- [アプリケーションのコードから資格情報を渡す](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### 資格情報を Azure CLI で手動で渡す

手動でサービス プリンシパルとしてサインインする場合は、**azure login** コマンドを使用します。テナント ID、アプリケーション ID とパスワードを指定する必要があります。パスワードはファイルに格納されるため、スクリプトにパスワードを直接含めるのは危険です。自動化されたスクリプトを実行する際のより適切なオプションについては、次のセクションを参照してください。

1. サービス プリンシパルを含むサブスクリプションの **TenantId** を確定します。現在認証されているサブスクリプションのテナント ID を取得する場合は、サブスクリプション ID をパラメーターとして渡す必要はありません。**-r** スイッチは、二重引用符なしで値を取得します。

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. ユーザー名には、サービス プリンシパルの作成時に使用した **AppId** を使用します。アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。**search** パラメーターに、Active Directory アプリケーションの名前を指定します。

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. サービス プリンシパルとしてログインします。

        azure login -u "$appId" --service-principal --tenant "$tenantId"

求められたら、アカウントの作成時に指定したパスワードを使用します。

    info:    Executing command login
    Password: ********

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

<a id="provide-credentials-through-automated-azure-cli-script" />
### 資格情報を自動化された Azure CLI スクリプトで渡す

このセクションでは、資格情報を手動で入力せずに、サービス プリンシパルとしてログインする方法を示します。

> [AZURE.NOTE] パスワードはテキストとして公開されるため、Azure CLI スクリプトにパスワードを含めるのは危険です。代わりに、Key Vault のようなサービスを使用してパスワードを格納し、スクリプトの実行時にそれを取得します。

これらの手順では、パスワードを格納する Key Vault とシークレットが設定済みであると仮定しています。テンプレートを使用して Key Vault とシークレットをデプロイする方法については、「[Key Vault テンプレートの形式]()」を参照してください。Key Vault の詳細については、「[Azure Key Vault の概要](./key-vault/key-vault-get-started.md)」を参照してください。

1. Key Vault からパスワードを取得します (以下の例では、シークレットは **appPassword** という名前で格納されています) です。JSON の出力から先頭と末尾の二重引用符を除去するには、**-r** スイッチを指定します。

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq -r '.value')
    
2. サービス プリンシパルを含むサブスクリプションの **TenantId** を確定します。現在認証されているサブスクリプションのテナント ID を取得する場合は、サブスクリプション ID をパラメーターとして渡す必要はありません。

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. ユーザー名には、サービス プリンシパルの作成時に使用した **AppId** を使用します。アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。**search** パラメーターに、Active Directory アプリケーションの名前を指定します。

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. アプリケーション ID、Key Vault からのパスワード、テナント ID を指定し、サービス プリンシパルとしてにログインします。

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

<a id="provide-credentials-through-code-in-an-application-cli" />
### アプリケーションのコードから資格情報を渡す

.NET アプリケーションから認証するには、次のコードを含めます。Active Directory アプリケーションにはアプリケーション ID が、サブスクリプションには、パスワードおよびテナント ID が必要です。アクセス トークンを取得すると、サブスクリプションのリソースを使用できます。

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }

## 証明書で認証する: Azure CLI

ここでは、認証に証明書を使用する Azure Active Directory アプリケーション用のサービス プリンシパルを作成します。このトピックでは、証明書が発行されていて、[OpenSSL](http://www.openssl.org/) がインストール済みであるものとします。

1. **.pem** ファイルを作成します。

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. **.pem** ファイルを開き、証明書データをコピーします。**-----BEGIN CERTIFICATE-----** と **-----END CERTIFICATE-----** 間の長い文字のシーケンスを探します。

3. **azure ad app create** コマンドを実行して新しい Active Directory アプリケーションを作成し、前の手順でコピーした証明書データをキーの値として提供します。

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Active Directory アプリケーションが返されます。サービス プリンシパルの作成、ロールの割り当て、およびアクセス トークンの取得には、AppId プロパティが必要です。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. アプリケーションのサービス プリンシパルを作成します。前の手順で返されたアプリケーション ID を入力します。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Active Directory アプリケーションとそのアプリケーション用のサービス プリンシパルの作成が完了しました。サービス プリンシパルをロールに割り当てました。これで、サービス プリンシパルとして操作できるよう、サービス プリンシパルとしてログインする必要があります。このトピックでは、2 つのオプションがあります。

- [自動化された Azure CLI スクリプトから証明書を渡す](#provide-certificate-through-automated-azure-cli-script)
- [アプリケーションのコードから証明書を渡す](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### 自動化された Azure CLI スクリプトから証明書を渡す

1. 証明書の拇印を取得し、不要な文字を削除する必要があります。

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     次のような拇印の値が返されます。

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. サービス プリンシパルを含むサブスクリプションの **TenantId** を確定します。現在認証されているサブスクリプションのテナント ID を取得する場合は、サブスクリプション ID をパラメーターとして渡す必要はありません。**-r** スイッチは、二重引用符なしで値を取得します。

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. ユーザー名には、サービス プリンシパルの作成時に使用した **AppId** を使用します。アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。**search** パラメーターに、Active Directory アプリケーションの名前を指定します。

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Azure CLI で認証するには、証明書の拇印、証明書のファイル、アプリケーション ID、およびテナント ID を渡します。

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

<a id="provide-certificate-through-code-in-an-application-cli" />
### アプリケーションのコードから証明書を渡す

.NET アプリケーションから認証するには、次のコードを含めます。クライアントを取得すると、サブスクリプションのリソースにアクセスできます。

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    // If using the new resource manager package like "Microsoft.Azure.ResourceManager" version="1.0.0-preview" use below
    var creds = new TokenCredentials(token.AccessToken); 
    // Else if using older package versions like Microsoft.Azure.Management.Resources" version="3.4.0-preview" use below
    // var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds); 
       
証明書と Azure CLI の使用方法の詳細については、「[Linux コマンドラインからの Azure サービス プリンシパルの証明書での認証](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)」を参照してください。

## 次のステップ
  
- ポータルを使用してサービス プリンシパルを操作する方法については、「[Azure ポータルを使用した Azure サービス プリンシパルの新規作成](./resource-group-create-service-principal-portal.md)」を参照してください。  
- Azure リソース マネージャーを使用したセキュリティの実装のガイダンスについては、「[Azure Resource Manager のセキュリティに関する考慮事項](best-practices-resource-manager-security.md)」を参照してください。


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0330_2016------>