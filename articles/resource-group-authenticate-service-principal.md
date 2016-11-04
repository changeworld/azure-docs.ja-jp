---
title: Azure サービス プリンシパルを PowerShell で作成する | Microsoft Docs
description: Azure PowerShell を使用して、Active Directory アプリケーションやサービス プリンシパルを作成し、ロールベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。パスワードまたは証明書を使ってアプリケーションを認証する方法を示します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/12/2016
ms.author: tomfitz

---
# リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Azure CLI](resource-group-authenticate-service-principal-cli.md)
> * [ポータル](resource-group-create-service-principal-portal.md)
> 
> 

リソースへのアクセスを必要とするアプリケーションやスクリプトがある場合、そのプロセスをお客様自身の資格情報で実行すると高い確率で不都合が生じます。アプリケーション用に別のアクセス許可が必要になるほか、担当が変わった場合もアプリケーションではお客様の資格情報が引き続き使用されることになります。このような場合は、認証の資格情報とロールの割り当てを含んだアプリケーションの ID を作成します。アプリケーションは、実行のたびにこれらの資格情報を使用して自動認証を行います。このトピックでは、アプリケーションをその独自の資格情報と ID で実行させるために必要な設定をすべて [Azure PowerShell](powershell-install-configure.md) で行う方法を紹介しています。

PowerShell では、AD アプリケーションの認証に次の 2 つの選択肢を利用できます。

* パスワード
* 証明書

このトピックでは、PowerShell でのこれらの選択肢の使用方法について説明します。プログラミング フレームワーク (Python、Ruby、Node.js など) から Azure にログインする場合は、パスワード認証が最適な選択肢と考えられます。パスワードと証明書のどちらを使用するか決める前に、「[サンプル アプリケーション](#sample-applications)」セクションで、各種フレームワークにおける認証の例を参照してください。

## Active Directory の概念
この記事では、Active Directory (AD) アプリケーションとサービス プリンシパルという 2 つのオブジェクトを作成します。AD アプリケーションは、アプリケーションをグローバルに表現したものです。資格情報としてアプリケーション ID と、パスワードまたは証明書のいずれかを保持します。サービス プリンシパルは、Active Directory のアプリケーションをローカルに表現したものです。こちらは、ロールの割り当てを保持します。このトピックでは、シングル テナント アプリケーション (単一組織内でのみ実行するように意図されたアプリケーション) に焦点を絞って説明します。一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。シングルテナント アプリケーションでは、1 つの AD アプリケーションと 1 つのサービス プリンシパルを設定します。

両方のオブジェクトが必要になるわけを疑問に思う方もいるでしょう。 このような方法の利点は、マルチテナント アプリケーションについて考えるとはっきりします。通常、マルチテナント アプリケーションは、さまざまなサブスクリプションでアプリケーションが実行されるサービスとしてのソフトウェア (SaaS) アプリケーションで使用します。マルチテナント アプリケーションでは、1 つの AD アプリケーションと複数のサービス プリンシパル (1 つは Active Directory でアプリケーションにアクセスを付与するためのもの) を設定できます。マルチテナント アプリケーションのセットアップについては、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

## 必要なアクセス許可
このトピックを完了するには、Azure Active Directory と Azure サブスクリプションの両方で適切なアクセス許可を持っている必要があります。具体的には、Active Directory でアプリケーションを作成し、ロールにサービス プリンシパルを割り当てることができる必要があります。

Active Directory のアカウントは管理者 (**グローバル管理者**や**ユーザー管理者**など) である必要があります。アカウントが**ユーザー** ロールに割り当てられている場合は、管理者に依頼してアクセス許可のレベルを上げてもらう必要があります。

サブスクリプションのアカウントには `Microsoft.Authorization/*/Write` アクセス権が必要です。このアクセス権は、[所有者](active-directory/role-based-access-built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](active-directory/role-based-access-built-in-roles.md#user-access-administrator)ロールを通じて付与されます。アカウントが**共同作成者**ロールに割り当てられている場合、ロールにサービス プリンシパルを割り当てようとするとエラーが発生します。ここでも、サブスクリプション管理者に適切なアクセス権を付与してもらう必要があります。

アクセス許可について確認したら、[パスワード認証](#create-service-principal-with-password)または[証明書認証](#create-service-principal-with-certificate)のどちらかのセクションに進みます。

## パスワードを使用したサービス プリンシパルの作成
このセクションでは次の手順を実行します。

* パスワードを指定して AD アプリケーションを作成する
* サービス プリンシパルを作成する
* サービス プリンシパルに閲覧者ロールを割り当てる

これらの手順をすぐに実行するには、次の 3 つのコマンドレットを参照してください。

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

それでは、各プロセスについて理解できるように、これらの手順についてより細かく見ていきましょう。

1. ご使用のアカウントにサインインします。
   
        Add-AzureRmAccount
2. 表示名、アプリケーションを説明する URI、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定して、新しい Active Directory アプリケーションを作成します。
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"
   
     シングルテナント アプリケーションでは、URI の検証は行われません。
   
     Active Directory のアカウントに[必要なアクセス許可](#required-permissions)が設定されていない場合、"認証が承認されなかったこと" または "コンテキストにサブスクリプションが見つからなかったこと" を示すエラー メッセージが表示されます。
3. 新しいアプリケーション オブジェクトを調べます。
   
        $app
   
     特に、サービス プリンシパルの作成、ロールの割り当て、アクセス トークンの取得に必要な **ApplicationId** プロパティがあることを確認します。
   
        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}
4. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。
   
        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
5. サブスクリプションに対する権限をサービス プリンシパルに付与します。この例では、サブスクリプション内のすべてのリソースを読み取る権限である**閲覧者**ロールを、サービス プリンシパルに付与します。ほかのロールについては、「[RBAC: 組み込みのロール](active-directory/role-based-access-built-in-roles.md)」を参照してください。**ServicePrincipalName** パラメーターには、アプリケーションの作成時に使用した **ApplicationId** を指定します。
   
        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid
   
    ロールを割り当てることのできるアクセス許可がアカウントに設定されていない場合は、エラー メッセージが表示されます。このメッセージは、アカウントが**スコープ '/subscriptions/{GUID}' に対するアクション 'Microsoft.Authorization/roleAssignments/write' の実行を承認されていない**ことを示しています。

これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。次のセクションでは、PowerShell から資格情報を使ってログインする方法を紹介します。コード アプリケーションの資格情報を使用する場合は、「[サンプル アプリケーション](#sample-applications)」に進んでください。

### PowerShell を使用して資格情報を渡す
次に、操作を実行するアプリケーションとしてログインする必要があります。

1. **Get-Credential** コマンドを実行して、ユーザーの資格情報を含む **PSCredential** オブジェクトを作成します。このコマンドを実行するには **ApplicationId** が必要ですので、この ID を貼り付けできるようにしておいてください。
   
        $creds = Get-Credential
2. 資格情報を入力するためのプロンプトが表示されます。ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。
   
     ![資格情報を入力](./media/resource-group-authenticate-service-principal/arm-get-credential.png)
3. サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。テナントは、Active Directory のインスタンスです。所有するサブスクリプションが 1 つのみである場合は、次のコマンドを使用できます。
   
        $tenant = (Get-AzureRmSubscription).TenantId
   
     サブスクリプションが複数ある場合は、Active Directory が関連付けられているサブスクリプションを指定します。詳細については、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。
   
        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
4. このアカウントがサービス プリンシパルであることを指定し、資格情報オブジェクトを提供することにより、サービス プリンシパルとしてログインします。
   
        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
   
     これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

### アクセス トークンを保存してログインを簡略化する
ログインの必要があるたびにサービス プリンシパルを指定しなくても済むように、アクセス トークンを保存することができます。

1. 後のセッションで現在のアクセス トークンを使用するために、プロファイルを保存します。
   
        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   
     プロファイルを開いて中身を確かめます。アクセス トークンが含まれていることを確認します。
2. 手動でもう一度ログインするのではなく、プロファイルをロードするだけで済みます。
   
        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json

> [!NOTE]
> アクセス トークンには有効期限があるため、保存したプロファイルを使用できるのはトークンが有効である間のみです。
> 
> 

## 証明書を使用したサービス プリンシパルの作成
このセクションでは次の手順を実行します。

* 自己署名証明書を作成する
* 証明書を指定して AD アプリケーションを作成する
* サービス プリンシパルを作成する
* サービス プリンシパルに閲覧者ロールを割り当てる

これらの手順をすぐに Windows 10 または Windows Server 2016 Technical Preview 上の Azure PowerShell 2.0 で実行するには、次のコマンドレットを参照してください。

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

それでは、各プロセスについて理解できるように、これらの手順についてより細かく見ていきましょう。以前のバージョンの Azure PowerShell またはオペレーティング システムを使用してタスクを完了する方法についても説明します。

### 自己署名証明書を作成する
Windows 10 および Windows Server 2016 Technical Preview に搭載されているバージョンの PowerShell では、**New-SelfSignedCertificate** コマンドレットが自己署名証明書を生成できるように更新されています。New-SelfSignedCertificate コマンドレットは古いオペレーティング システムにも備わっていますが、このトピックに必要なパラメーターは使用できません。この場合は、証明書を生成するモジュールをインポートする必要があります。このトピックでは、お使いのオペレーティング システムに応じて証明書を生成する方法を紹介します。

* **Windows 10 または Windows Server 2016 Technical Preview** をお使いの場合は、次のコマンドを実行して自己署名証明書を作成します。
  
        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
* **Windows 10 または Windows Server 2016 Technical Preview をお使いでない**場合は、Microsoft スクリプト センターから [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) スクリプトをダウンロードします。ダウンロードしたファイルを展開し、必要なコマンドレットをインポートします。
  
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
     その後、証明書を生成します。
  
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

これで証明書を用意し、AD アプリケーションを作成できるようになりました。

### Active Directory アプリケーションとサービス プリンシパルを作成する
1. 証明書からキーの値を取得します。
   
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
2. Azure アカウントにサインインします。
   
        Add-AzureRmAccount
3. 表示名、アプリケーションを説明する URI、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定して、新しい Active Directory アプリケーションを作成します。
   
     Azure PowerShell 2.0 (2016 年 8 月以降) をお使いの場合は、次のコマンドレットを実行します。
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   
    Azure PowerShell 1.0 をお使いの場合は、次のコマンドレットを実行します。
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   
    シングルテナント アプリケーションでは、URI の検証は行われません。
   
    Active Directory のアカウントに[必要なアクセス許可](#required-permissions)が設定されていない場合、"認証が承認されなかったこと" または "コンテキストにサブスクリプションが見つからなかったこと" を示すエラー メッセージが表示されます。
   
    新しいアプリケーション オブジェクトを調べます。
   
        $app
   
    サービス プリンシパルの作成、ロールの割り当て、アクセス トークンの取得に必要な **ApplicationId** プロパティがあることを確認します。
   
        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}
4. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。
   
        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
5. サブスクリプションに対する権限をサービス プリンシパルに付与します。この例では、サブスクリプション内のすべてのリソースを読み取る権限である**閲覧者**ロールを、サービス プリンシパルに付与します。ほかのロールについては、「[RBAC: 組み込みのロール](active-directory/role-based-access-built-in-roles.md)」を参照してください。**ServicePrincipalName** パラメーターには、アプリケーションの作成時に使用した **ApplicationId** を指定します。
   
        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid
   
    ロールを割り当てることのできるアクセス許可がアカウントに設定されていない場合は、エラー メッセージが表示されます。このメッセージは、アカウントが**スコープ '/subscriptions/{GUID}' に対するアクション 'Microsoft.Authorization/roleAssignments/write' の実行を承認されていない**ことを示しています。

これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。次のセクションでは、PowerShell から証明書を使ってログインする方法を紹介します。

### 自動化された PowerShell スクリプトから証明書を渡す
サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。テナントは、Active Directory のインスタンスです。所有するサブスクリプションが 1 つのみである場合は、次のコマンドを使用できます。

    $tenant = (Get-AzureRmSubscription).TenantId

サブスクリプションが複数ある場合は、Active Directory が関連付けられているサブスクリプションを指定します。詳細については、[Azure AD ディレクトリの管理](active-directory/active-directory-administer.md)に関するページを参照してください。

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

スクリプトで認証するためには、アカウントがサービス プリンシパルであることを指定し、証明書の拇印、アプリケーション ID、およびテナント ID を提供します。これらの値を環境変数に格納しておいて実行時に取得するか、スクリプトに記述することで、スクリプトを自動化することができます。

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

## サンプル アプリケーション
サービス プリンシパルとしてログインする方法については、以下のサンプル アプリケーションで紹介されています。

**.NET**

* [.NET からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Azure のリソースとリソース グループを .NET で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [リソースの概要 - Java で Azure Resource Manager テンプレートをデプロイする](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [リソースの概要 - Java でリソース グループを管理する](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Python からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Azure のリソースとリソース グループを Python で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Node.js からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Azure のリソースとリソース グループを Node.js で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Ruby からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Azure のリソースとリソース グループを Ruby で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## 次のステップ
* アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
* アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](active-directory/active-directory-application-objects.md)」を参照してください。
* Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

<!---HONumber=AcomDC_0914_2016-->