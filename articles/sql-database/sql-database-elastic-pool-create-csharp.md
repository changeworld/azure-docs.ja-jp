---
title: "C# を使用したエラスティック プールの作成 | Microsoft Docs"
description: "多数のデータベースでリソースを共有できるように、C# データベース開発手法を使用して、Azure SQL Database にスケーラブルなエラスティック プールを作成します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 2dedddbb-618d-462b-80dd-e4a57857c737
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: b905aa65f46f601121b6774a633e72152e8ef80a


---
# <a name="create-an-elastic-pool-with-cx23"></a>C&#x23; を使用したエラスティック プールの作成
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
> 
> 

この記事では、C# と [Azure SQL Database Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) を使用して Azure SQL エラスティック プールを作成する方法を説明します。 スタンドアロンの SQL データベースを作成する場合には、「 [C# を使用して SQL Database Library for .NET で SQL Database を作成する](sql-database-get-started-csharp.md)」を参照してください。

Azure SQL Database Library for .NET は、[Resource Manager ベースの SQL Database REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) をラップする [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ベースの API を提供します。

> [!NOTE]
> SQL Database の新機能の多くは、[Azure Resource Manager デプロイメント モデル](../azure-resource-manager/resource-group-overview.md)を使用している場合にのみサポートされます。そのため、常に最新の **Azure SQL Database Management Library for .NET ([ドキュメント](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))** を使用する必要があります。 以前の[クラシック デプロイメント モデル ベースのライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)は互換性のためだけにサポートされているため、より新しい Resource Manager ベースのライブラリを使用することをお勧めします。
> 
> 

この記事の手順を完了するには、次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、このページの上部にある **無料アカウント** をクリックしてサブスクリプションを作成してから、この記事に戻って最後まで完了してください。
* 見ることができます。 Visual Studio の無償版については、 [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs) に関するページを参照してください。

## <a name="create-a-console-app-and-install-the-required-libraries"></a>コンソール アプリケーションの作成と必要なライブラリのインストール
1. Visual Studio を起動します。
2. **[ファイル]** > **[新規]** > **[プロジェクト]** の順にクリックします。
3. C# **コンソール アプリケーション** を作成し、 *SqlElasticPoolConsoleApp*

C# を使用して SQL データベースを作成するために、( [パッケージ マネージャー コンソール](http://docs.nuget.org/Consume/Package-Manager-Console)を使用して) 必要な管理ライブラリを読み込みます。

1. **[ツール]** > **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順にクリックします。
2. 「 `Install-Package Microsoft.Azure.Management.Sql –Pre` 」と入力して [Microsoft Azure SQL 管理ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)をインストールします。
3. 「 `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` 」と入力して [Microsoft Azure Resource Manager ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)をインストールします。
4. 「 `Install-Package Microsoft.Azure.Common.Authentication –Pre` 」と入力して [Microsoft Azure 一般認証ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication)をインストールします。 

> [!NOTE]
> この記事の例では、各 API 要求の同期フォームを使用し、基になるサービスでの REST 呼び出しが完了するまでブロックします。 非同期の手法も利用できます。
> 
> 

## <a name="create-a-sql-elastic-pool---c-example"></a>SQL エラスティック プールの作成 - C# の例
次のサンプルでは、リソース グループ、サーバー、ファイアウォール規則、エラスティック プールを作成した後、そのプールに SQL データベースを作成します。 「[リソースにアクセスするためのサービス プリンシパルの作成](#create-a-service-principal-to-access-resources)」を参照し、変数 `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` を取得してください。

**Program.cs** の内容を次のとおりに書き換え、実際のアプリの値で `{variables}` を更新します (`{}` は含めません)。

```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```





## <a name="create-a-service-principal-to-access-resources"></a>リソースにアクセスするためのサービス プリンシパルの作成
次の PowerShell スクリプトを実行すると、Active Directory (AD) アプリケーションのほか、C# アプリの認証に必要なサービス プリンシパルが作成されます。 このスクリプトによって、上記の C# のサンプルに必要な値が出力されます。 詳細については、「 [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../resource-group-authenticate-service-principal.md)」を参照してください。

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="next-steps"></a>次のステップ
* [プールを管理する](sql-database-elastic-pool-manage-csharp.md)
* [エラスティック ジョブを作成する](sql-database-elastic-jobs-overview.md): エラスティック ジョブを使用すると、プール内にある任意の数のデータベースに対して T-SQL スクリプトを実行できます。
* [Azure SQL Database によるスケールアウト](sql-database-elastic-scale-introduction.md): Elastic Database のツールを使用してスケールアウトを実施します。

## <a name="additional-resources"></a>その他のリソース
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
* [Azure リソース管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)




<!--HONumber=Dec16_HO2-->


