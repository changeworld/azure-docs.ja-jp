<properties
   pageTitle="Azure リソース マネージャーでのサービス プリンシパルの認証"
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
   ms.date="10/30/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーでのサービス プリンシパルの認証

このトピックでは、サブスクリプション内の他のリソースにアクセスできるように、サービス プリンシパル (自動プロセス、アプリケーション、サービスなど) を許可する方法を示します。Azure リソース マネージャーでは、ロール ベースのアクセス制御を使用して、許可されたアクションをサービス プリンシパルに付与し、そのサービス プリンシパルを認証することができます。このトピックでは、PowerShell と Azure CLI を使用して、サービス プリンシパルにロールを割り当て、そのサービス プリンシパルを認証する方法を示します。

ユーザー名とパスワードで認証する方法と証明書で認証する方法を示します。

Azure PowerShell を利用するか、Azure CLI for Mac, Linux and Windows を利用できます。Azure PowerShell がインストールされていない場合、[Azure PowerShell のインストールと構成の方法](./powershell-install-configure.md)を参照してください。Azure CLI がインストールされていない場合は、「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。

## 概念
1. Azure Active Directory (AAD) - クラウドの ID およびアクセス管理サービス。詳しくは、「[Azure Active Directory とは](active-directory/active-directory-whatis.md)」を参照してください。
2. サービス プリンシパル - 他のリソースにアクセスする必要がある、ディレクトリ内のアプリケーションのインスタンス。
3. AD アプリケーション - AAD に対してアプリケーションを特定するディレクトリ レコード。詳しくは、[Azure AD での認証の基本](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)を参照してください。

## パスワードによるサービス プリンシパルの認証 - PowerShell

このセクションでは、Azure Active Directory アプリケーションのサービス プリンシパルを作成し、ロールをサービス プリンシパルに割り当て、アプリケーションの ID とパスワードを指定することでサービス プリンシパルとして認証する手順を実行します。

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

1. ご使用のアカウントにサインインします。

        PS C:\> Login-AzureRmAccount

1. **New-AzureRmADApplication** コマンドを実行して、新しい AAD アプリケーションを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     新しいアプリケーション オブジェクトを調べます。サービス プリンシパルの作成、ロールの割り当て、および JWT トークンの取得には、**ApplicationId** プロパティが必要です。

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. アプリケーションのサービス プリンシパルを作成します。

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

3. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロール ベースのアクセス制御について詳しくは、[Managing and Auditing Access to Resources (リソースへのアクセスの管理と監査)](resource-group-rbac.md) を参照してください。

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. ロールの割り当てが作成されたサブスクリプションを取得します。このサブスクリプションは、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を取得するために後に使用されます。

        PS C:\> $subscription = Get-AzureRmSubscription

     現在選択されているサブスクリプション以外のサブスクリプション内にロールの割り当てを作成した場合、**SubscriptoinId** または **SubscriptionName** パラメーターを指定して、別のサブスクリプションを取得できます。

5. **Get-credential** コマンドを実行して、ユーザーの資格情報を含む新しい**PSCredential** オブジェクトを作成します。

        PS C:\> $creds = Get-Credential

     資格情報を入力するためのプロンプトが表示されます。

     ![][1]

     ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** または **IdentifierUris** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

6. 入力した資格情報は、**Add-azureaccount** コマンドレットへの入力として使用します。このコマンドレットは、以下の場所のサービス プリンシパルに署名します。

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

     これで、作成した AAD アプリケーションのサービス プリンシパルとして認証されるはずです。

7. アプリケーションから認証するには、次の .NET コードを含めます。トークンを取得すると、サブスクリプションのリソースにアクセスできます。

        public static string GetAToken()
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



## 証明書によるサービス プリンシパルの認証 - PowerShell

このセクションでは、Azure Active Directory アプリケーションのサービス プリンシパルを作成し、ロールをサービス プリンシパルに割り当て、証明書を指定することでサービス プリンシパルとして認証する手順を実行します。このトピックでは、証明書が発行されているものとします。

証明書を使用するための 2 つの方法を示します。キーの資格情報とキーの値です。いずれかの方法を使用できます。

最初に、後にアプリケーションを作成するときに使用するいくつかの値を PowerShell に設定する必要があります。

1. ご使用のアカウントにサインインします。

        PS C:\> Login-AzureRmAccount

1. いずれの方法でも、証明書から X509Certificate オブジェクトを作成し、キーの値を取得します。証明書のパスとその証明書のパスワードを使用します。

        $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. キーの資格情報を使用する場合、キーの資格情報オブジェクトを作成し、その値を前の手順の `$keyValue` に設定します。

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

        $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    または、2 番目の例を使用し、キーの資格情報を割り当てます。

         $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    新しいアプリケーション オブジェクトを調べます。サービス プリンシパルの作成、ロールの割り当て、および JWT トークンの取得には、**ApplicationId** プロパティが必要です。

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. アプリケーションのサービス プリンシパルを作成します。

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

5. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロール ベースのアクセス制御について詳しくは、[Managing and Auditing Access to Resources (リソースへのアクセスの管理と監査)](resource-group-rbac.md) を参照してください。

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. アプリケーションから認証するには、次の .NET コードを含めます。クライアントを取得すると、サブスクリプションのリソースにアクセスできます。

        string clientId = "<Application ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<Tenant id or tenant name>"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
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
        var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
        var client = new ResourceManagementClient(creds); 
        

## パスワードによるサービス プリンシパルの認証 - Azure CLI

まず、サービス プリンシパルを作成します。これを行うには、ディレクトリにアプリケーションを作成する必要があります。このセクションでは、ディレクトリに新しいアプリケーションを作成する手順を示します。

1. Azure リソース マネージャー モードに切り替えて、アカウントにサインインします。

        azure config mode arm
        azure login

2. **azure ad app create** コマンドを実行して、新しい AAD アプリケーションを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    Azure AD アプリケーションが返されます。サービス プリンシパルの作成、ロールの割り当て、および JWT トークンの取得には、ApplicationId プロパティが必要です。

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

3. アプリケーションのサービス プリンシパルを作成します。前の手順で返されたアプリケーション ID を入力します。

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

4. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロール ベースのアクセス制御について詳しくは、[Managing and Auditing Access to Resources (リソースへのアクセスの管理と監査)](resource-group-rbac.md) を参照してください。

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

5. アカウントを一覧表示し、その出力から **TenantId** プロパティを検索することによって、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を決めます。

        azure account list --json

6. サービス プリンシパルを ID として使用してサインインします。ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    これで、作成した AAD アプリケーションのサービス プリンシパルとして認証されるはずです。

## 次のステップ
  
- ロールベースのアクセス制御の概要については、「[リソースへのアクセスの管理と監査](resource-group-rbac.md)」を参照してください。  
- ポータルを使用してサービス プリンシパルを操作する方法については、「[Azure ポータルを使用した Azure サービス プリンシパルの新規作成](./resource-group-create-service-principal-portal.md)」を参照してください。  
- Azure リソース マネージャーを使用したセキュリティの実装のガイダンスについては、「[Azure Resource Manager のセキュリティに関する考慮事項](best-practices-resource-manager-security.md)」を参照してください。


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=Nov15_HO2-->