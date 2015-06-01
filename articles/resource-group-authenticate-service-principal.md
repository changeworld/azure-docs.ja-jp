<properties
   pageTitle="Azure リソース マネージャーでのサービス プリンシパルの認証"
   description="ロール ベースのアクセス制御を使用して、サービス プリンシパルにアクセス権限を付与し、それを認証する方法について説明します。PowerShell と Azure CLI を使用してこれらのタスクを実行する方法を示します。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーでのサービス プリンシパルの認証

このトピックでは、サブスクリプション内の他のリソースにアクセスできるように、サービス プリンシパル (自動プロセス、アプリケーション、サービスなど) を許可する方法を示します。Azure リソース マネージャーでは、ロール ベースのアクセス制御を使用して、許可されたアクションをサービス プリンシパルに付与し、そのサービス プリンシパルを認証することができます。このトピックでは、PowerShell と Azure CLI を使用して、サービス プリンシパルにロールを割り当て、そのサービス プリンシパルを認証する方法を示します。


## 概念
1. Azure Active Directory (AAD) - クラウドの ID およびアクセス管理サービス。詳しくは、「[Azure Active Directory とは](./active-directory-whatis.md)」を参照してください。
2. サービス プリンシパル - ディレクトリ内のアプリケーションのインスタンス。
3. AD アプリケーション - AAD に対してアプリケーションを特定するディレクトリ レコード。詳しくは、「[Azure AD での認証の基本](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)」を参照してください。

## PowerShell を使用したサービス プリンシパルに対するアクセス付与と認証

Azure PowerShell がインストールされていない場合、「[Azure PowerShell のインストールと構成の方法](./powershell-install-configure.md)」を参照してください。

まず、サービス プリンシパルを作成します。これを行うには、ディレクトリにアプリケーションを作成する必要があります。このセクションでは、ディレクトリに新しいアプリケーションを作成する手順を示します。

1. **New-AzureADApplication** コマンドを実行して、新しい AAD アプリケーションを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。

        PS C:> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

   Azure AD アプリケーションが返されます。

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


   >[AZURE.NOTE]サービス プリンシパルの作成、ロールの割り当て、および JWT トークンの取得には、**ApplicationId** プロパティが必要です。出力を保存するか、または変数内にキャプチャします。

3. アプリケーションのサービス プリンシパルを作成します。

        PS C:> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

   これでディレクトリにサービス プリンシパルが作成されましたが、そのサービスには権限やスコープが割り当てられていません。いくつかのスコープで操作を実行する権限を、サービス プリンシパルに明示的に付与する必要があります。

4. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロール ベースのアクセス制御について詳しくは、「[リソースへのアクセスの管理と監査](./resource-group-rbac.md)」を参照してください。

        PS C:> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

5. ロールの割り当てが作成されたサブスクリプションを取得します。このサブスクリプションは、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を取得するために後に使用されます。

        PS C:> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

   現在選択されているサブスクリプション以外のサブスクリプション内にロールの割り当てを作成した場合、**SubscriptoinId** または **SubscriptionName** パラメーターを指定して、別のサブスクリプションを取得できます。

6. **Get-credential** コマンドを実行して、ユーザーの資格情報を含む新しい**PSCredential** オブジェクトを作成します。

        PS C:> $creds = Get-Credential

   資格情報を入力するためのプロンプトが表示されます。

   ![][1]

   ユーザー名には、アプリケーションの作成時に使用した  **ApplicationId** または **IdentifierUris** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

7. 入力した資格情報は、**Add-azureaccount** コマンドレットへの入力として使用します。このコマンドレットは、以下の場所のサービス プリンシパルに署名します。

        PS C:> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

   これで、作成した AAD アプリケーションのサービス プリンシパルとして認証されるはずです。


## Azure CLI を使用したサービス プリンシパルに対するアクセス付与と認証

Azure CLI for Mac, Linux and Windows がインストールされていない場合は、「[Azure CLI のインストールと構成](xplat-cli-install.md)」を参照してください。

これらの手順を実行するには、AD アプリケーションとサービス プリンシパルを既に所有している必要があります。従来の Azure ポータルによって AD アプリケーションとサービス プリンシパルをセットアップする方法については、「[従来の Azure ポータルを使用した Azure サービス プリンシパルの新規作成](./resource-group-create-service-principal-portal.md)」を参照してください。

1. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。**ServicePrincipalName** パラメーターには、アプリケーションを作成するときに使用した **ApplicationId** または **IdentifierUris** を指定します。ロール ベースのアクセス制御について詳しくは、「[リソースへのアクセスの管理と監査](./resource-group-rbac.md)」を参照してください。

        azure role assignment create --objectId {service-principal-object-id} -o Reader -c /subscriptions/{subscriptionId}/

2. アカウントを一覧表示し、その出力から **TenantId** プロパティを検索することによって、サービス プリンシパルのロールの割り当てが存在するテナントの **TenantId** を決めます。

        azure account list

3. サービス プリンシパルを ID として使用してサインインします。ユーザー名には、アプリケーションの作成時に使用した **ApplicationId** を使用します。パスワードには、アカウントの作成時に指定したものを使用します。

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

  これで、作成した AAD アプリケーションのサービス プリンシパルとして認証されるはずです。

## 次のステップ
Getting Started (概要)

- [Azure リソース マネージャーの概要](./resource-group-overview.md)  
- [Azure リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)
- [Azure リソース 管理での Azure CLI for Mac, Linux, and Windows の使用](./xplat-cli-azure-resource-manager.md)  
- [Azure ポータルを使用した Azure リソースの管理](./resource-group-portal.md)  
  
アプリケーションの作成と展開
  
- [Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)  
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](./resource-group-template-deploy.md)  
- [Azure でのリソース グループのデプロイのトラブルシューティング](./resource-group-deploy-debug.md)  
- [Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)  
- [高度なテンプレートの操作](./resource-group-advanced-template.md)  
- [.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ](./arm-template-deployment.md)
  
リソースの整理
  
- [タグを使用した Azure リソースの整理](./resource-group-using-tags.md)  
  
アクセスの管理と監査
  
- [リソースへのアクセスの管理と監査](./resource-group-rbac.md)  
- [Azure ポータルを使用した Azure サービス プリンシパルの新規作成](./resource-group-create-service-principal-portal.md)  
  


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!--HONumber=52-->
