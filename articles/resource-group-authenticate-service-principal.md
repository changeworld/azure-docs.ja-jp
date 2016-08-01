<properties
   pageTitle="Azure サービス プリンシパルを PowerShell で作成する | Microsoft Azure"
   description="Azure PowerShell を使用して、Active Directory アプリケーションやサービス プリンシパルを作成し、ロールベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。パスワードまたは証明書を使ってアプリケーションを認証する方法を示します。"
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
   ms.date="07/18/2016"
   ms.author="tomfitz"/>

# リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [ポータル](resource-group-create-service-principal-portal.md)

リソースへのアクセスを必要とするアプリケーションやスクリプトがあるとき、そのプロセスを特定のユーザーの資格情報で実行すると高い確率で不都合が生じます。ユーザーの権限がプロセスに割り当てるべき権限と異なっていたり、ユーザーの職責が変わったりする可能性があります。そのような場合は、認証の資格情報とロールの割り当てを含んだアプリケーションの ID を作成します。アプリケーションは、その実行時に都度、この ID でログインすることになります。このトピックでは、アプリケーションをその独自の資格情報と ID で実行させるために必要な設定をすべて [Azure PowerShell](powershell-install-configure.md) で行う方法を紹介しています。

この記事では、Active Directory (AD) アプリケーションとサービス プリンシパルの 2 つのオブジェクトを作成します。この AD アプリケーションは、資格情報としてアプリケーション ID とパスワード (またはアプリケーション ID と証明書) を保持します。サービス プリンシパルは、ロールの割り当てを保持します。AD アプリケーションから、多くのサービス プリンシパルを作成できます。このトピックでは、シングル テナント アプリケーション (単一組織内で実行することのみが意図されたアプリケーション) に焦点を絞って説明します。一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。アプリケーションを複数の組織で実行する必要があるときは、マルチテナント アプリケーションを作成することもできます。一般に、Software-as-a-Service (SaaS) アプリケーションには、マルチテナント アプリケーションが使用されます。マルチテナント アプリケーションのセットアップについては、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

Active Directory との連携では、理解すべき概念が数多く存在します。アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](./active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](./active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

AD アプリケーションの認証に関して、PowerShell には次の 2 つの選択肢があります。

 - パスワード
 - 証明書

AD アプリケーションのセットアップ後、別のプログラミング フレームワーク (Python、Ruby、Node.js など) から Azure にログインする予定がある場合、パスワード認証が最適な選択肢と考えられます。パスワードと証明書のどちらを使用するか決める前に、「[サンプル アプリケーション](#sample-applications)」セクションで、各種フレームワークにおける認証の例を参照してください。

## テナント ID を取得する

サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。テナントは、Active Directory のインスタンスです。テナント ID の値は、パスワード認証と証明書認証のどちらであっても必要となるので、ここで取得しておきましょう。

1. ご使用のアカウントにサインインします。

        Add-AzureRmAccount

2. 所有するサブスクリプションが 1 つだけである場合は、次のコマンドを使用できます。

        $tenant = (Get-AzureRmSubscription).TenantId
    
     複数のサブスクリプションがある場合は、AD アプリに使用するサブスクリプションを指定します。Active Directory が存在するサブスクリプションを選択します。詳細については、[Azure AD ディレクトリの管理](./active-directory/active-directory-administer.md)に関するページを参照してください。

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

ここまで終えたら、[パスワード](#create-service-principal-with-password)認証または[証明書](#create-service-principal-with-certificate)認証のセクションに進みます。

## パスワードを使用したサービス プリンシパルの作成

このセクションでは、パスワードを使用して AD アプリケーションとサービス プリンシパルを作成する手順を実行します。

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


     AD アプリケーションから、サービス プリンシパルを作成して、ロールを割り当てる必要があります。

2. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。ロールを割り当てるには、[所有者](./active-directory/role-based-access-built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)ロールを通じて付与される `Microsoft.Authorization/*/Write` アクセス権が必要です。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。次のセクションでは、PowerShell から資格情報を使ってログインする方法を紹介していますが、独自コードのアプリケーションから資格情報を使用する場合は、特に読む必要はありません。「[サンプル アプリケーション](#sample-applications)」に進んで、アプリケーション ID とパスワードでログインする例をご覧ください。

### PowerShell を使用して資格情報を渡す

操作を実行するアプリケーションとしてログインする必要があります。

1. **Get-credential** コマンドを実行して、ユーザーの資格情報を含む新しい**PSCredential** オブジェクトを作成します。このコマンドを実行する前に、**ApplicationId** または **IdentifierUris** が必要となります。いつでも貼り付けできるようにしておいてください。

        $creds = Get-Credential

2. 資格情報を入力するためのプロンプトが表示されます。ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** または **IdentifierUris** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

     ![資格情報を入力](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

4. このアカウントがサービス プリンシパルであることを指定して、資格情報オブジェクトを提供することにより、サービス プリンシパルとしてログインします。「[テナント ID を取得する](#get-tenant-id)」で取得したテナント ID が必要となります。

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

5. 後のセッションで現在のアクセス トークンを使用するために、プロファイルを保存することができます。

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     プロファイルを開き、その内容を確認できます。アクセス トークンが含まれていることを確認します。
        
6. 次回サービス プリンシパルとしてコードを実行するときは、手動で再びログインする代わりにプロファイルをロードします。

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] アクセス トークンは期限切れするため、保存されているプロファイルはトークンが有効な限り機能します。
        
## 証明書を使用したサービス プリンシパルの作成

このセクションでは、証明書を使用して AD アプリケーションとサービス プリンシパルを作成する手順を実行します。

1. 自己署名証明書を作成します。**Windows 10 または Windows Server 2016 Technical Preview** をお使いの場合は、次のコマンドを実行します。

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     拇印を含む、証明書に関する情報が変数に格納されます。
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Windows 10 も Windows Server 2016 Technical Preview も持って**いない**場合、**New-SelfSignedCertificate** コマンドレットは存在しません。その場合は、[Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) という PowerShell スクリプトをダウンロードし、次のコマンドを実行して証明書を生成してください。前の例で既に証明書を作成済みである場合、この手順は不要です。
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. 証明書からキーの値を取得します。

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

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


5. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

6. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。ロールを割り当てるには、[所有者](./active-directory/role-based-access-built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)ロールを通じて付与される `Microsoft.Authorization/*/Write` アクセス権が必要です。

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。次のセクションでは、PowerShell から証明書を使ってログインする方法を紹介します。

### 自動化された PowerShell スクリプトから証明書を渡す

スクリプトで認証するためには、アカウントがサービス プリンシパルであることを指定し、証明書の拇印、アプリケーション ID、およびテナント ID を提供します。これらの値は既に、**$azureAdApplication.ApplicationId**、**$cert.Thumbprint**、**$tenant** の各変数に格納されています。これらの値を環境変数に格納しておいて実行時に取得するか、スクリプトに記述することで、スクリプトを自動化することができます。

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint 000000 -ApplicationId 000000 -TenantId 0000000

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

アプリケーション ID を後から取得する必要がある場合は、次のコマンドを使用します。

    (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
証明書の拇印を後から取得する必要がある場合は、次のコマンドを使用します。

    (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

テナント ID を後から取得する必要がある場合は、「[テナント ID を取得する](#get-tenant-id)」を参照してください。

## サンプル アプリケーション

サービス プリンシパルとしてログインする方法については、以下のサンプル アプリケーションで紹介されています。

**.NET**

- [.NET からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Azure のリソースとリソース グループを .NET で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [リソースの概要 - Java で Azure Resource Manager テンプレートをデプロイする](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [リソースの概要 - Java でリソース グループを管理する](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Python からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Azure のリソースとリソース グループを Python で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

- [Node.js からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Azure のリソースとリソース グループを Node.js で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Ruby からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Azure のリソースとリソース グループを Ruby で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## 次のステップ
  
- アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->