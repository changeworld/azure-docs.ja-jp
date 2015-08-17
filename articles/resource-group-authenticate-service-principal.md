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
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーでのサービス プリンシパルの認証

このトピックでは、サブスクリプション内の他のリソースにアクセスできるように、サービス プリンシパル (自動プロセス、アプリケーション、サービスなど) を許可する方法を示します。Azure リソース マネージャーでは、ロール ベースのアクセス制御を使用して、許可されたアクションをサービス プリンシパルに付与し、そのサービス プリンシパルを認証することができます。このトピックでは、PowerShell と Azure CLI を使用して、サービス プリンシパルにロールを割り当て、そのサービス プリンシパルを認証する方法を示します。


## 概念
1. Azure Active Directory (AAD) - クラウドの ID およびアクセス管理サービス。詳しくは、[Azure Active Directory とは何ですか。](active-directory/active-directory-whatis.md)を参照してください。
2. サービス プリンシパル - 他のリソースにアクセスする必要がある、ディレクトリ内のアプリケーションのインスタンス。
3. AD アプリケーション - AAD に対してアプリケーションを特定するディレクトリ レコード。詳しくは、[Azure AD での認証の基本](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)を参照してください。

## PowerShell を使用したサービス プリンシパルに対するアクセス付与と認証

Azure PowerShell がインストールされていない場合、[Azure PowerShell のインストールと構成の方法](./powershell-install-configure.md)を参照してください。

まず、サービス プリンシパルを作成します。これを行うには、ディレクトリにアプリケーションを作成する必要があります。このセクションでは、ディレクトリに新しいアプリケーションを作成する手順を示します。

1. **New-AzureADApplication** コマンドを実行して、新しい AAD アプリケーションを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。

        PS C:\> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Azure AD アプリケーションが返されます。サービス プリンシパルの作成、ロールの割り当て、および JWT トークンの取得には、**ApplicationId** プロパティが必要です。出力を保存するか、または変数内にキャプチャします。

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


2. アプリケーションのサービス プリンシパルを作成します。

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

3. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロール ベースのアクセス制御について詳しくは、[Managing and Auditing Access to Resources (リソースへのアクセスの管理と監査)](azure-portal/resource-group-rbac.md) を参照してください。

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. ロールの割り当てが作成されたサブスクリプションを取得します。このサブスクリプションは、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を取得するために後に使用されます。

        PS C:\> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     現在選択されているサブスクリプション以外のサブスクリプション内にロールの割り当てを作成した場合、**SubscriptoinId** または **SubscriptionName** パラメーターを指定して、別のサブスクリプションを取得できます。

5. **Get-credential** コマンドを実行して、ユーザーの資格情報を含む新しい**PSCredential** オブジェクトを作成します。

        PS C:\> $creds = Get-Credential

     資格情報を入力するためのプロンプトが表示されます。

     ![][1]

     ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** または **IdentifierUris** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

6. 入力した資格情報は、**Add-azureaccount** コマンドレットへの入力として使用します。このコマンドレットは、以下の場所のサービス プリンシパルに署名します。

        PS C:\> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     これで、作成した AAD アプリケーションのサービス プリンシパルとして認証されるはずです。


## Azure CLI を使用したサービス プリンシパルに対するアクセス付与と認証

Azure CLI for Mac, Linux and Windows がインストールされていない場合は、[Azure CLI のインストール](xplat-cli-install.md)を参照してください。

1. **azure ad app create** コマンドを実行して、新しい AAD アプリケーションを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。

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

2. アプリケーションのサービス プリンシパルを作成します。前の手順で返されたアプリケーション ID を入力します。

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

3. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロール ベースのアクセス制御について詳しくは、[Managing and Auditing Access to Resources (リソースへのアクセスの管理と監査)](azure-portal/resource-group-rbac.md) を参照してください。

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. アカウントを一覧表示し、その出力から **TenantId** プロパティを検索することによって、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を決めます。

        azure account list

5. サービス プリンシパルを ID として使用してサインインします。ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    これで、作成した AAD アプリケーションのサービス プリンシパルとして認証されるはずです。

## 次のステップ
  
- ロールベースのアクセス制御の概要については、「[リソースへのアクセスの管理と監査](azure-portal/resource-group-rbac.md)」を参照してください。  
- ポータルを使用してサービス プリンシパルを操作する方法については、「[Azure ポータルを使用した Azure サービス プリンシパルの新規作成](./resource-group-create-service-principal-portal.md)」を参照してください。  
- Azure リソース マネージャーを使用したセキュリティの実装のガイダンスについては、「[Azure リソース マネージャーのセキュリティに関する考慮事項](best-practices-resource-manager-security.md)」を参照してください。


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=August15_HO6-->