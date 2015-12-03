<properties 
	pageTitle="SQL Database を試す: C# を使用して SQL Database を作成する | Microsoft Azure" 
	description="SQL Database で SQL および C# アプリケーションを開発し、C# と SQL Database Library for .NET を使用して、Azure SQL Database を作成します。" 
	keywords="sql を試す、sql c#"   
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/01/2015"
   ms.author="sstein"/>

# SQL Database を試す: C&#x23; を使用して SQL Database Library for .NET で SQL Database を作成する 

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



C# コマンドを使用して、[Azure SQL Database Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) で Azure SQL Database を作成する方法について説明します。

SQL と C# で 1 つのデータベースを作成して、SQL Database を試してみます。エラスティック データベースを作成するには、「[エラスティック データベース プールの作成](sql-database-elastic-pool-portal.md)」を参照してください。

各コード スニペットはわかりやすさを重視し、細かく分けて説明しています。また、サンプルのコンソール アプリケーションのすべてのコマンドは、この記事の下部にまとめられています。

Azure SQL Database Library for .NET は、[リソース マネージャー ベースの SQL Database REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) をラップする [Azure リソース マネージャー](resource-group-overview.md) ベースの API を提供します。このクライアント ライブラリは、リソース マネージャー ベースのクライアント ライブラリの一般的なパターンに従います。リソース マネージャーでは、リソース グループ、および [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD) を使用した認証が必要です。

<br>

> [AZURE.NOTE]SQL Database Library for .NET は現在プレビュー段階にあります。

<br>

この記事の手順を完了するには、次のものが必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- 見ることができます。Visual Studio の無償版については、「[Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs)」 ページを参照してください。


## 必要なライブラリのインストール

C# を使用して SQL Database を設定するには、[パッケージ マネージャー コンソール](http://docs.nuget.org/Consume/Package-Manager-Console)で次のパッケージをインストールし、必要な管理ライブラリを入手します。

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Azure Active Directory による認証の構成

まず、必要な認証を設定して、REST API にアクセスするクライアント アプリケーションを有効にする必要があります。

[Azure Resource Manager REST APIs](https://msdn.microsoft.com/library/azure/dn948464.aspx) は Azure Active Directory を認証に使用します。

現在のユーザーに基づいてクライアント アプリケーションを認証するには、まず、Azure のリソースが作成されたサブスクリプションに関連付けられている AAD ドメインにアプリケーションを登録する必要があります。職場アカウントまたは学校アカウントではなく、Microsoft アカウントで Azure サブスクリプションが作成されている場合は、既定の AAD ドメインが既に存在します。アプリケーションの登録は、[Azure ポータル](https://manage.windowsazure.com/)で実行できます。

新しいアプリケーションを作成し、適切な Active Directory に登録するには、以下を実行します。

1. 左側にあるメニューをスクロールして **Active Directory** サービスを探し、開きます。

    ![SQL Database を試す: Azure Active Directory (AAD) を設定する][1]

2. アプリケーションを認証するディレクトリを選択し、その**名前**をクリックします。

    ![SQL C# アプリケーションを認証するディレクトリを選択します。][4]

3. [ディレクトリ] ページで、**[アプリケーション]** をクリックします。

    ![アプリケーションがあるディレクトリ ページ。][5]

4. **[追加]** をクリックし、SQL Database の新しい C# アプリケーションを作成します。

    ![SQL C# アプリケーションを追加します。][6]

5. **[組織で開発中のアプリケーションを追加]** を選択します。

5. アプリの**名前**を指定し、**[ネイティブ クライアント アプリケーション]** を選択します。

    ![SQL C# アプリケーションに関する情報を指定します。][7]

6. **リダイレクト URI** を指定します。実際のエンドポイントである必要はありませんが、有効な URI である必要があります。

    ![SQL C# アプリケーションのリダイレクト URL を追加します。][8]

7. アプリの作成を完了し、**[構成]** をクリックして、**[クライアント ID]** をコピーします (クライアント ID は、コードで必要になります)。

    ![SQL C# アプリケーションのクライアント ID を取得します。][9]


1. ページの下部で **[アプリケーションの追加]** をクリックします。
1. **[Microsoft Apps]** を選択します。
1. **[Azure サービス管理 API]** を選択し、ウィザードを完了します。
2. API を選択した状態で、**[Access Azure Service Management (プレビュー)]** を選択し、この API へのアクセスに必要な特定のアクセス許可を付与する必要があります。

    ![アクセス許可を設定します。][2]

2. **[保存]** をクリックします。



### ドメイン名の指定

コードにはドメイン名が必要です。適切なドメイン名を指定する簡単な方法を以下に示します。

1. [Azure プレビュー ポータル](https://portal.azure.com)にアクセスします。
2. 右上隅の自分の名前にマウスを合わせ、ポップアップ ウィンドウに表示されるドメインをメモしてください。

    ![ドメイン名を指定します。][3]





**その他の AAD リソース**

認証向けの Azure Active Directory の使用に関する追加情報は、[この便利なブログの投稿](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/)で検索できます。


### 現在のユーザーのアクセス トークンの取得 

クライアント アプリケーションでは、現在のユーザーのアプリケーション アクセス トークンを取得する必要があります。ユーザーが最初にこのコードを実行すると、ユーザーの資格情報を入力するよう求められ、作成されたトークンがローカルにキャッシュされます。以降の実行では、キャッシュからトークンが取得されます。トークンの有効期限が切れている場合のみログインが必要になります。

    /// <summary>
    /// Prompts for user credentials when first run or if the cached credentials have expired.
    /// </summary>
    /// <returns>The access token from AAD.</returns>
    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                clientId,
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]この記事の例では、各 API 要求の同期フォームを使用し、基になるサービスでの REST 呼び出しが完了するまでブロックします。非同期の手法も利用できます。



## リソース グループの作成

リソース マネージャーでは、すべてのリソースをリソース グループに作成する必要があります。リソース グループは、1 つのアプリケーションの関連リソースを保持するコンテナーです。Azure SQL Database では、既存のリソース グループ内でデータベース サーバーを作成し、次にサーバー上でデータベースを作成する必要があります。その後、アプリケーションをサーバーまたはデータベースに接続し、ファイアウォール規則をサーバーで作成し、クライアントの IP アドレスからアクセスを開く必要があります。


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



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
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## サーバーのファイアウォール規則を作成し、サーバーへのアクセスを許可します。

既定では、任意の場所からサーバーに接続することはできません。サーバーまたはサーバー上の任意のデータベースに接続するには、クライアントの IP アドレスからのアクセスを許可する[ファイアウォール規則](https://msdn.microsoft.com/library/azure/ee621782.aspx)を定義する必要があります。

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

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




他の Azure サービスによるサーバーへのアクセスを許可するには、ファイアウォール規則を追加し、StartIpAddress と EndIpAddress を 0.0.0.0 に設定します。これにより、 *任意* の Azure サブスクリプションからの Azure トラフィックがサーバーへアクセスできるようになる点に注意してください。


## C&#x23; を使用して Basic SQL Database を作成する

次の C# コマンドは、サーバー上に同じ名前のデータベースが存在しない場合、新しい Basic SQL Database を作成します。同じ名前のデータベースが存在する場合は、データベースを更新します。

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## サンプル C&#x23; コンソール アプリケーション


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## 次のステップ
ここでは、SQL Database を試し、C# でデータベースを設定しました。次は以下の記事を参照してください。

- [C# を使用して SQL Database に接続し、照会する](sql-database-connect-query.md)
- [SQL Server Management Studio (SSMS) での接続](sql-database-connect-to-database.md)

## その他のリソース

- [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!----HONumber=Nov15_HO4-->