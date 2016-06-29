<properties
   pageTitle="PowerShell を使用した AD アプリケーション の作成 | Microsoft Azure"
   description="Azure PowerShell を使用して、Active Directory アプリケーションを作成し、ロール ベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。パスワードまたは証明書を使ってアプリケーションを認証する方法を示します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# リソースにアクセスできる Active Directory アプリケーションを Azure PowerShell で作成する

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [ポータル](resource-group-create-service-principal-portal.md)


このトピックでは、[Azure PowerShell](powershell-install-configure.md) を使用して、自動化プロセス、アプリケーション、またはサービスなどの、サブスクリプション内の他のリソースにアクセスできる Active Directory (AD) アプリケーションを作成する方法を示します。Azure Resource Manager では、ロール ベースのアクセス制御を使用して、アプリケーションへの許可されたアクションを付与することができます。

この記事では、AD アプリケーションとサービス プリンシパルの 2 つのオブジェクトを作成します。AD アプリケーションは、アプリが登録されているテナントに存在し、実行するプロセスを定義します。サービス プリンシパルには AD アプリケーションの ID が含まれており、この ID は権限を割り当てるために使用されます。AD アプリケーションから、多くのサービス プリンシパルを作成できます。アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](./active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](./active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

アプリケーションを認証する方法には 2 つのオプションがあります。

 - パスワード - 実行中に対話形式でログインする場合に適しています。
 - 証明書 - ユーザーの介入なしでの認証が必要な自動スクリプトに適しています。

## パスワードを使用した AD アプリケーションの作成

このセクションでは、パスワードを使用して AD アプリケーションを作成する手順を実行します。

1. ご使用のアカウントにサインインします。

        Add-AzureRmAccount

1. アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定して、新しい Active Directory アプリケーションを作成します。

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     新しいアプリケーション オブジェクトを調べます。

        $azureAdApplication
        
     特に、サービス プリンシパルの作成、ロールの割り当て、およびアクセス トークンの取得に必要な **ApplicationId** プロパティがあることを確認します。

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


### サービス プリンシパルの作成とロールへの割り当て

AD アプリケーションから、サービス プリンシパルを作成して、ロールを割り当てる必要があります。

1. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### PowerShell を使用して資格情報を手動で渡す

Active Directory アプリケーションとそのアプリケーション用のサービス プリンシパルの作成が完了しました。サービス プリンシパルをロールに割り当てました。操作を実行するアプリケーションとしてログインする必要があります。必要に応じて使用するスクリプトまたはコマンドを実行して、手動でアプリケーションの資格情報を渡すことができます。

1. **Get-credential** コマンドを実行して、ユーザーの資格情報を含む新しい**PSCredential** オブジェクトを作成します。

        $creds = Get-Credential

2. 資格情報を入力するためのプロンプトが表示されます。ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** または **IdentifierUris** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

     ![資格情報を入力](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

3. ロールの割り当てが作成されたサブスクリプションを取得します。このサブスクリプションは、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を取得するために使用されます。

        $subscription = Get-AzureRmSubscription

     アカウントが複数のサブスクリプションにリンクされている場合は、サブスクリプションの名前または ID を入力して、使用するサブスクリプションを取得します。
     
        $subscription = Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate"

4. このアカウントがサービス プリンシパルであることを指定して、資格情報オブジェクトを提供することにより、サービス プリンシパルとしてログインします。

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId
        
     これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

5. 後のセッションで現在のアクセス トークンを使用するために、プロファイルを保存することができます。

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     プロファイルを開き、その内容を確認できます。アクセス トークンが含まれていることを確認します。
        
6. 次回サービス プリンシパルとしてコードを実行するときは、再びログインする代わりにプロファイルをロードします。

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] アクセス トークンは期限切れするため、保存されているプロファイルはトークンが有効な限り機能します。永続的に自動スクリプトを実行するには、証明書を使用します。
        
## 証明書を使用した AD アプリケーションの作成

このセクションでは、証明書を使用して AD アプリケーションを作成する手順を実行します。

1. 自己署名証明書を作成します。Windows 10 または Windows Server 2016 Technical Preview をお使いの場合は、次のコマンドを実行します。 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     拇印を含む、証明書に関する情報が返されます。
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Windows 10 または Windows Server 2016 Technical Preview をお使いでない場合は、[Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) PowerShell スクリプトをダウンロードしてください。証明書を生成するには、次のコマンドを実行します。
     
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. 証明書からキーの値を取得します。

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

3. Azure アカウントにサインインします。

        Add-AzureRmAccount

4. ディレクトリにアプリケーションを作成します。

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    新しいアプリケーション オブジェクトを調べます。

        $azureAdApplication

    サービス プリンシパルの作成、ロールの割り当て、およびアクセス トークンの取得に必要な **ApplicationId** プロパティがあることを確認します。

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


### サービス プリンシパルの作成とロールへの割り当て

1. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### スクリプトの値を準備します。

スクリプトでは、サービス プリンシパルとしてログインするために必要な 3 つの値で渡します。次が必要です。

- アプリケーション ID
- テナント ID 
- 証明書の拇印

前の手順でアプリケーション ID と証明書の拇印について説明しました。ただし、後でこれらの値を取得する必要がある場合は、そのコマンドを、テナント ID を取得するコマンドとともに以下に示します。

1. テナント ID を取得するには、次のコマンドを使用します。

        (Get-AzureRmSubscription).TenantId 

    複数のサブスクリプションがある場合は、サブスクリプションの名前を入力します。

        (Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate").TenantId
        
2. アプリケーション ID を取得するには、次のコマンドを使用します。

        (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
3. 証明書の拇印を取得するには、次のコマンドを使用します。

        (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

### 自動化された PowerShell スクリプトから証明書を渡す

Active Directory アプリケーションとそのアプリケーション用のサービス プリンシパルの作成が完了しました。サービス プリンシパルをロールに割り当てました。これで、サービス プリンシパルとして操作できるよう、サービス プリンシパルとしてログインする必要があります。

スクリプトで認証するためには、アカウントがサービス プリンシパルであることを指定し、証明書の拇印、アプリケーション ID、およびテナント ID を提供します。

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint {thumbprint} -ApplicationId {applicationId} -TenantId {tenantid}

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

## 次のステップ
  
- .NET 認証の例については、「[Azure Resource Manager SDK for .NET](resource-manager-net-sdk.md)」を参照してください。
- Java 認証の例については、「[Azure Resource Manager SDK for Java](resource-manager-java-sdk.md)」を参照してください。 
- Python 認証の例については、[Python 向けリソース管理認証](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)に関するページを参照してください。
- REST 認証の例については、「[Resource Manager REST API](resource-manager-rest-api.md)」を参照してください。
- アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

<!---HONumber=AcomDC_0615_2016-->