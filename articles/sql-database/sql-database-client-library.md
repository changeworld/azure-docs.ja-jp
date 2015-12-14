<properties 
   pageTitle="C# を使用した Azure SQL Database の作成と管理" 
   description="この記事では、C# で Azure SQL Database Library for .NET を使用して、Azure SQL Database を作成および管理する方法を説明します。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="12/01/2015"
   ms.author="sstein"/>

# C&#x23; で SQL Database を作成し、管理する

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## 概要

この記事には、C# で [Azure SQL Database Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) を使用して、多くの Azure SQL Database の管理タスクを実行するコマンドを記載しています。

各コード スニペットはわかりやすさを重視し、細かく分けて説明しています。また、サンプルのコンソール アプリケーションのすべてのコマンドは、この記事の下部にまとめられています。

Azure SQL Database Library for .NET は、[リソース マネージャー ベースの SQL Database REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) をラップする [Azure リソース マネージャー](resource-group-overview.md) ベースの API を提供します。このクライアント ライブラリは、リソース マネージャー ベースのクライアント ライブラリの一般的なパターンに従います。リソース マネージャーでは、リソース グループ、および [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD) を使用した認証が必要です。

<br>

> [AZURE.NOTE]SQL Database Library for .NET は現在プレビュー段階にあります。

<br>

Azure サブスクリプションをお持ちでない場合、このページの上部の **[無料試用版]** をクリックしてからこの記事にお戻りください。Visual Studio の無償版については、「[Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs)」 ページを参照してください。

## 必要なライブラリのインストール

[パッケージ マネージャー コンソール](http://docs.nuget.org/Consume/Package-Manager-Console)で次のパッケージをインストールし、必要な管理ライブラリを入手します。

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Azure Active Directory による認証の構成

まず、必要な認証を設定して、REST API にアクセスするアプリケーションを有効にする必要があります。

[Azure リソース マネージャー REST API](https://msdn.microsoft.com/library/azure/dn948464.aspx) は、認証に、以前の Azure サービス管理 REST API で使用された証明書ではなく、Azure Active Directory を使用します。

現在のユーザーに基づいてクライアント アプリケーションを認証するには、まず、Azure のリソースが作成されたサブスクリプションに関連付けられている AAD ドメインにアプリケーションを登録する必要があります。職場アカウントまたは学校アカウントではなく、Microsoft アカウントで Azure サブスクリプションが作成されている場合は、既定の AAD ドメインが既に存在します。アプリケーションの登録は、[クラシック ポータル](https://manage.windowsazure.com/)で実行できます。

新しいアプリケーションを作成し、適切な Active Directory に登録するには、以下を実行します。

1. 左側にあるメニューをスクロールして **Active Directory** サービスを探し、開きます。

    ![AAD][1]

2. アプリケーションを認証するディレクトリを選択し、その**名前**をクリックします。

    ![ディレクトリ][4]

3. [ディレクトリ] ページで、**[アプリケーション]** をクリックします。

    ![アプリケーション][5]

4. **[追加]** をクリックして、新しいアプリケーションを作成します。

    ![Add application][6]

5. **[組織で開発中のアプリケーションを追加]** を選択します。

5. アプリの**名前**を指定し、**[ネイティブ クライアント アプリケーション]** を選択します。

    ![Add application][7]

6. **リダイレクト URI** を指定します。実際のエンドポイントである必要はありませんが、有効な URI である必要があります。

    ![Add application][8]

7. アプリの作成を完了し、**[構成]** をクリックして、**[クライアント ID]** をコピーします (クライアント ID は、コードで必要になります)。

    ![クライアント ID の取得][9]


1. ページの下部で **[アプリケーションの追加]** をクリックします。
1. **[Microsoft Apps]** を選択します。
1. **[Azure サービス管理 API]** を選択し、ウィザードを完了します。
2. API を選択した状態で、**[Access Azure Service Management (プレビュー)]** を選択し、この API へのアクセスに必要な特定のアクセス許可を付与する必要があります。

    ![アクセス許可][2]

2. **[保存]** をクリックします。



### ドメイン名の指定

コードにはドメイン名が必要です。適切なドメイン名を指定する簡単な方法を以下に示します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 右上隅の自分の名前にマウスを合わせ、ポップアップ ウィンドウに表示されるドメインをメモしてください。

    ![ドメイン名の指定][3]





**その他の AAD リソース**

認証向けの Azure Active Directory の使用に関する追加情報は、[この便利なブログの投稿](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/)で検索できます。


### 現在のユーザーのアクセス トークンの取得 

クライアント アプリケーションでは、現在のユーザーのアプリケーション アクセス トークンを取得する必要があります。ユーザーが最初にコードを実行すると、ユーザーの資格情報を入力するよう求められ、作成されたトークンがローカルにキャッシュされます。以降の実行では、キャッシュからトークンが取得されます。トークンの有効期限が切れている場合のみログインが必要になります。


    /// <summary>
    /// Prompts for user credentials when first run or if the cached credentials have expired.
    /// </summary>
    /// <returns>The access token from AAD.</returns>
    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" /* AAD URI */ 
                + "domain.onmicrosoft.com" /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */, 
                "aa00a0a0-a0a0-0000-0a00-a0a00000a0aa" /* application client ID from AAD*/, 
        new Uri("urn:ietf:wg:oauth:2.0:oob") /* redirect URI */, 
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }

ユーザーの操作が不要な自動化されたスクリプトを作成するためには、ユーザーの代わりにサービス プリンシパルに基づいて認証することができます。この方法では、資格情報オブジェクトの作成と送信が必要です。




> [AZURE.NOTE]この記事の例では、各 API 要求の同期フォームを使用し、基になるサービスでの REST 呼び出しが完了するまでブロックします。非同期の手法も利用できます。



## リソース グループの作成

リソース マネージャーでは、すべてのリソースをリソース グループに作成する必要があります。リソース グループは、1 つのアプリケーションの関連リソースを保持するコンテナーです。Azure SQL Database では、最初に既存のリソース グループ内でデータベース サーバーを作成し、次にサーバー上でデータベースを作成する必要があります。その後、 TDS を使用して、アプリケーションをサーバーまたはデータベースに接続し、T-SQL を送信する前に、ファイアウォール規則をサーバーで作成し、クライアントの IP アドレスからアクセスを開く必要があります。


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



## サーバーの作成 

SQL Database はサーバーに格納されます。サーバー名がすでに使われている場合はエラーが発生する可能性があるため、すべての Azure SQL Server でグローバルに一意のサーバー名を使用する必要があります。このコマンドは完了するまでに数分かかる場合があることに注意してください。


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = "P@ssword1",
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);




## サーバーのファイアウォール規則を作成し、サーバーへのアクセスを許可します。

既定では、任意の場所からサーバーに接続することはできません。TDS を使用してサーバーに接続し、T-SQL をサーバーまたはサーバー上の任意のデータベースに送信するには、クライアントの IP アドレスからのアクセスを許可する[ファイアウォール規則](https://msdn.microsoft.com/library/azure/ee621782.aspx)を定義する必要があります。

次の例では、任意の IP アドレスからサーバーへのアクセスを開く規則を作成します。データベースをセキュリティで保護し、侵入に対する基本的な対策としてファイアウォール規則に依存しないよう、適切な SQL ログインおよびパスワードを作成することをお勧めします。


    // Create a firewall rule on the server to allow TDS connection 
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);




他の Azure サービスによるサーバーへのアクセスを許可するには、ファイアウォール規則を追加し、StartIpAddress と EndIpAddress を 0.0.0.0 に設定します。これにより、*任意*の Azure サブスクリプションからの Azure トラフィックがサーバーへアクセスできるようになる点に注意してください。


## データベースの作成

次のコマンドは、サーバー上に同じ名前のデータベースが存在しない場合、新しい Basic データベースを作成します。同じ名前のデータベースが存在する場合は、データベースを更新します。

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Basic",
                RequestedServiceObjectiveName = "Basic",
                MaxSizeBytes = 2147483648,
                Collation = "SQL_Latin1_General_CP1_CI_AS"
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);



## データベースの更新 

データベースを更新するには (たとえば、サービス階層やパフォーマンス レベルを変更する場合)、上記のデータベースの作成や更新のように **Databases.CreateOrUpdate** メソッドを呼び出します。**[Edition]** および **[RequestedServiceObjectiveName]** プロパティを目的のサービス階層やパフォーマンス レベルに設定します。**Premium** から、または、Premium へエディションを変更する場合、データベースのサイズによっては、更新に少し時間がかかる点に注意してください。

以下は、SQL Database を Standard (S0) レベルに更新します。

    // Retrieve current database properties 
    var currentDatabase = sqlClient.Databases.Get("resourecegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updateDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "S0", // alternatively set the RequestedServiceObjectiveId
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation
        }
     };

    // Update the database
    dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updateDatabaseParameters);


## サーバー上のすべてのデータベースの一覧表示

サーバー上のすべてのデータベースを一覧表示するには、Databases.List メソッドに、サーバーとリソース グループの名前を渡します。

    // List databases on the server
    DatabaseListResponse dbListOnServer = sqlClient.Databases.List("resourcegroup-name", "server-name");
    Console.WriteLine("Databases on Server {0}", "server-name");
    foreach (Database db in dbListOnServer)
    {
        Console.WriteLine("  Database {0}, Service Objective {1}", db.Name, db.Properties.ServiceObjective);
    }



## エラスティック データベース プールの作成

サーバー上に新しいプールを作成するには、以下の手順を実行します。



    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## エラスティック データベース プールの更新

    // Retrieve existing pool properties
    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);



## 既存のデータベースをエラスティック データベース プールに移動

*プールを作成したら、Transact-SQL を使用して既存のデータベースをプールの内外に移動することもできます。詳細については、「[エラスティック データベース プールのリファレンス - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL)」をご覧ください。*

既存のデータベースをプールに移動するには、以下の手順を実行します。

    
    // Update database service objective to add the database to a pool
    
    // Retrieve current database properties 
    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;
    
    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };
    
    // Update the database
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);
    
    


## エラスティック データベース プールでの新しいデータベースの作成

*プールを作成したら、Transact-SQL を使用してプールに新しいエラスティック データベースを作成することもできます。詳細については、「[エラスティック データベース プールのリファレンス - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL)」をご覧ください。*

プール内で新しいデータベースを直接作成するには、以下の手順を実行します。

    
    // Create a new database in the pool
    
    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };
    
    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);



## エラスティック データベース プールのすべてのデータベースの一覧表示

プール内のすべてのデータベースを一覧表示するには、以下の手順を実行します。

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## サーバーの削除

サーバーを削除するには (サーバーを削除すると、サーバー上のデータベースおよびエラスティック データベース プールも削除されます)、次のコードを実行します。

    var serverOperationResponse = sqlClient.Servers.Delete("resourcegroup-name", "server-name");


## リソース グループの削除

リソース グループを削除するには、以下の手順を実行します。

    // Delete the resource group
    var resourceOperationResponse = resourceClient.ResourceGroups.Delete("resourcegroup-name");



## コンソール アプリケーションのサンプル


    using Microsoft.Azure;
    using Microsoft.Azure.Insights;
    using Microsoft.Azure.Insights.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Security;

    namespace AzureSqlDatabaseRestApiExamples
    {
    class Program
    {
        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */ 
                + "domain.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */, 
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/, 
                new Uri("urn:ietf:wg:oauth:2.0:oob") /* redirect URI */, 
                PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }

        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "YOU.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                userCredential);

            return token;
        }
        private static SecureString convertToSecureString(string secret)
        {
            var secureStr = new SecureString();
            if (secret.Length > 0)
            {
                foreach (var c in secret.ToCharArray()) secureStr.AppendChar(c);
            }
            return secureStr;
        }

        static void Main(string[] args)
        {
            var token = GetAccessToken();
            
            // Who am I?
            Console.WriteLine("Identity is {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = "South Central US"
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = "ServerAdmin",
                    AdministratorLoginPassword = "P@ssword1",
                    Version = "12.0"
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);

            var serverGetResult = sqlClient.Servers.Get("resourcegroup-name", "server-name");


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Basic",
                    RequestedServiceObjectiveName = "Basic",
                    MaxSizeBytes = 2147483648,
                    Collation = "SQL_Latin1_General_CP1_CI_AS"
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);

            // ...
            // Update database: retrieve current database properties 
            var currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updateDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "S0", // alternatively set the RequestedServiceObjectiveId
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation
                }
            };

            // Update the database
            dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updateDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
                    DatabaseDtuMin = 0,
                    DatabaseDtuMax = 100
                }
            };

           // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update pool: retrieve existing pool properties
            var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

            // Update pool: configure create or update parameters with existing property values, override those to be changed.
            ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentPool.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = currentPool.Properties.Edition,
                    DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
                    DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
                    Dtu = (int)currentPool.Properties.Dtu,
                    StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
                }
            };
            newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update database service objective to add the database to a pool

            // Update database: retrieve current database properties 
            currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation,
                }
            };

            // Update the database
            var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", dbUpdateResponse.Database.Name, dbUpdateResponse.StatusCode, dbUpdateResponse.Database.Properties.ServiceObjective, dbUpdateResponse.Database.Properties.ElasticPoolName);

            // Create a new database in the pool

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = 268435456000, // 250 GB,
                    Collation = "SQL_Latin1_General_CP1_CI_AS"
                }
            };

            var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", poolDbResponse.Database.Name, poolDbResponse.StatusCode, poolDbResponse.Database.Properties.ServiceObjective, poolDbResponse.Database.Properties.ElasticPoolName);

            // List databases on the server
            DatabaseListResponse dbListOnServer = sqlClient.Databases.List("resourcegroup-name", "server-name");
            Console.WriteLine("Databases on Server {0}", "server-name");
            foreach (Database db in dbListOnServer)
            {
                Console.WriteLine("  Database {0}, Service Objective {1}", db.Name, db.Properties.ServiceObjective);
            }

            // List all servers, pools and databases in the resource group
            ServerListResponse serverList = new ServerListResponse();
            ElasticPoolListResponse poolList = new ElasticPoolListResponse();
            DatabaseListResponse dbListInPool = new DatabaseListResponse();

            Console.WriteLine("Servers in resource group {0}", "resourcegroup-name");
            serverList = sqlClient.Servers.List("resourcegroup-name");
            foreach (Server server in serverList)
            {
                Console.WriteLine("  Server '{0}' location: {1}", server.Name, server.Location);
                poolList = sqlClient.ElasticPools.List("resourcegroup-name", server.Name);
                foreach (ElasticPool pool in poolList)
                {
                    Console.WriteLine("    Elastic Pool '{0}' edition: {1} eDTU: {2} storage GB: {3} database eDTU min: {4} database eDTU max: {5}", pool.Name, pool.Properties.Edition, pool.Properties.Dtu, (pool.Properties.StorageMB/1024), pool.Properties.DatabaseDtuMin, pool.Properties.DatabaseDtuMax);
                    dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", server.Name, pool.Name);
                    foreach(Database db in dbListInPool)
                    {
                        Console.WriteLine("      Database '{0}'", db.Name);                       
                    }
                }
            }

            // Metrics

            var endTime = String.Format(DateTime.Now.ToUniversalTime().ToString("s")) + "Z"; // as UTC in sortable time format yyyy-mm-ddThh:mm:ssZ
            var duration = TimeSpan.FromHours(2);
            var startTime = String.Format(DateTime.Now.Subtract(duration).ToUniversalTime().ToString("s")) + "Z";  // as UTC in sortable time format yyyy-mm-ddThh:mm:ssZ

            Console.WriteLine("");
            Console.WriteLine("Elastic pool metrics for 'ElasticPool1'");

            ElasticPoolMetricDefinitions poolMetricDefinition = new ElasticPoolMetricDefinitions();
            poolMetricDefinition = sqlClient.ElasticPools.ListMetricDefinitions("resourcegroup-name", "server-name", "ElasticPool1");

            Console.WriteLine("  Metric definitions: ");
            foreach (Microsoft.Azure.Management.Sql.Models.MetricDefinition metricDefinition in poolMetricDefinition.MetricDefinitions)
            {
                Console.WriteLine("    '{0}' unit: {1} aggregation type: {2}", metricDefinition.Name.LocalizedValue, metricDefinition.Unit, metricDefinition.PrimaryAggregationType);
            }
            Console.WriteLine("  Metric values: ");
            ElasticPoolMetrics elasticPoolMetrics = new ElasticPoolMetrics();
            elasticPoolMetrics = sqlClient.ElasticPools.ListMetrics("resourcegroup-name", "server-name", "ElasticPool1", "name.value eq 'dtu_consumption_percent'", "PT5M", startTime, endTime);
            foreach (Microsoft.Azure.Management.Sql.Models.Metric metric in elasticPoolMetrics.Metrics)
            {
                Console.WriteLine("    '{0}' unit: {1} time grain: {2} start time: {3} end time: {4} values: {5}", metric.Name.LocalizedValue, metric.Unit, metric.TimeGrain, metric.StartTime, metric.EndTime, metric.Values.Count);
                foreach (Value metricValue in metric.Values)
                {
                    Console.WriteLine("      Timestamp: {0} average: {1} minimum: {2} maximum {3} total {4}", metricValue.Timestamp, metricValue.Average, metricValue.Maximum, metricValue.Minimum, metricValue.Total);
                }
            }

            // List database metrics
            Console.WriteLine("");
            Console.WriteLine("Database metrics for 'Database1'");
            Console.WriteLine("  Metric definitions"); 

            Microsoft.Azure.Insights.InsightsClient insightsClient = new InsightsClient(tokenCredentials);

            Microsoft.Azure.Insights.Models.MetricDefinitionListResponse metricDefinitionListResponse = insightsClient.MetricDefinitionOperations.GetMetricDefinitions("subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/resourcegroup-name/providers/microsoft.sql/servers/server-name/databases/Database1/", "");
            foreach (Microsoft.Azure.Insights.Models.MetricDefinition metricDefinition in metricDefinitionListResponse.MetricDefinitionCollection.Value)
            {
                Console.WriteLine("    {0} unit: {1} aggregation: {2}" , metricDefinition.Name.LocalizedValue, metricDefinition.Unit, metricDefinition.PrimaryAggregationType);
            }
            var resourceURI = "subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/resourcegroup-name/providers/microsoft.sql/servers/server-name/databases/Database1/";
            var filter = "(name.value eq 'dtu_consumption_percent') and startTime eq " + startTime + " and endTime eq " + endTime + " and timeGrain eq duration'PT5M'";

            Console.WriteLine("  Metric values");
            MetricListResponse mlr = insightsClient.MetricOperations.GetMetrics(resourceURI,filter);
            foreach (Microsoft.Azure.Insights.Models.Metric metric in mlr.MetricCollection.Value)
            {
                Console.WriteLine("    {0}", metric.Name.LocalizedValue);
                foreach (MetricValue metricValue in metric.MetricValues)
                {
                    Console.WriteLine("      Timestamp: {0} minimum: {1} maximum: {2} average: {3}", metricValue.Timestamp, metricValue.Minimum, metricValue.Maximum, metricValue.Average);
                }
            }

            Console.WriteLine("");
            Console.WriteLine("Press any key to delete the server and resource group, which will also delete the databases and the elastic pool.");
            Console.ReadKey();

            // Delete the server which deletes the databases and then the elastic pool
            var serverOperationResponse = sqlClient.Servers.Delete("resourcegroup-name", "server-name");
            Console.WriteLine("");
            Console.WriteLine("Server {0} delete completed with status code {1}.", "server-name", serverOperationResponse.StatusCode);

            // Delete the resource group
            var resourceOperationResponse = resourceClient.ResourceGroups.Delete("resourcegroup-name");
            Console.WriteLine("");
            Console.WriteLine("Resource {0} delete completed with status code {1}.", "resourcegroup-name", resourceOperationResponse.StatusCode);

            Console.WriteLine("");
            Console.WriteLine("Execution complete.  Press any key to continue.");
            Console.ReadKey();
        }
    }
    }





## その他のリソース

[SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)

[Azure リソース管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[エラスティック データベース プールのリファレンス](sql-database-elastic-pool-reference.md)


<!--Image references-->
[1]: ./media/sql-database-client-library/aad.png
[2]: ./media/sql-database-client-library/permissions.png
[3]: ./media/sql-database-client-library/getdomain.png
[4]: ./media/sql-database-client-library/aad2.png
[5]: ./media/sql-database-client-library/aad-applications.png
[6]: ./media/sql-database-client-library/add.png
[7]: ./media/sql-database-client-library/add-application.png
[8]: ./media/sql-database-client-library/add-application2.png
[9]: ./media/sql-database-client-library/clientid.png

<!---HONumber=AcomDC_1203_2015-->