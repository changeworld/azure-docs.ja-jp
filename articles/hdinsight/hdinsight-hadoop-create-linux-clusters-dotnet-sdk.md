<properties
   	pageTitle="cURL と Azure REST API を使用した HDInsight 用の Linux ベースの Hadoop、HBase、または Storm クラスターの作成 | Microsoft Azure"
   	description="cURL と Azure REST API を使用して、HDInsight 用の Linux ベースの Hadoop、HBase、または Storm クラスターを作成する方法について説明します。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/14/2015"
   	ms.author="jgao"/>

#.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-create-linux-cluster-selector.md)]

HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。このドキュメントでは、.NET SDK を使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。

##前提条件

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- __Visual Studio 2013 または 2015__

##コンソール アプリケーションの作成

1. Visual Studio 2013 または 2015 を開きます。

2. 次の設定を使用して、新しい Visual Studio プロジェクトを作成します。

    |プロパティ|値|
    |--------|-----|
    |テンプレート|テンプレート/Visual C#/Windows/コンソール アプリケーション|
    |名前|CreateHDICluster|

5. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

        Install-Package Microsoft.Azure.Common.Authentication -pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre

    これらのコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

6. ソリューション エクスプローラーで、**Program.cs** をダブルクリックして開き、次のコードを貼り付け、変数の値を指定します。

        using System;
        using System.Security;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        
        namespace CreateHDICluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
                
                //Replace SUBSCRIPTIONID with your Azure subscription ID
                private static Guid SubscriptionId = new Guid(SUBSCRIPTIONID);
                
                //Replace GROUPNAME with the name of the resource group to create the cluster in
                private const string ResourceGroupName = "GROUPNAME";
                
                //Replace CLUSTERNAME with the name of the HDInsight cluster you wish to create
                private const string NewClusterName = "CLUSTERNAME";
                private const int NewClusterNumNodes = 1;
                
                //Replace LOCATION with the geographic region that the resource group, storage account, and HDInsight cluster are in
                private const string NewClusterLocation = "LOCATION";
                private const string NewClusterVersion = "3.2";
                
                //Replace STORAGENAME with the name of the storage account to use
                private const string ExistingStorageName = "STORAGENAME.blob.core.windows.net";
                
                //Replace STORAGEKEY with the key for the storage account
                private const string ExistingStorageKey = "STORAGEKEY";
                
                //Replace CONTAINERNAME with the container to use for HDInsight's default storage
                private const string ExistingContainer = "CONTAINTERNAME";
                private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
            private const OSType NewClusterOSType = OSType.Linux;
                private const string NewClusterUsername = "admin";
                
                //Replace ADMINPASSWORD with the password for the admin account
                private const string NewClusterPassword = "ADMINPASSWORD";
                
                //Replace SSHUSER with the user name you want to use with logging in to the cluster through SSH
                private const string NewClusterSshUserName = "SSHUSER";
                
                //Replace SSHPUBLICKEY with the public key certificate to use when authenticating the SSH user. For more information on generating and using SSH keys with HDInsight, see https://azure.microsoft.com/JA-JP/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/ and https://azure.microsoft.com/JA-JP/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/
                private const string NewClusterSshPublicKey = @"SSHPUBLICKEY";
        
                private static void Main(string[] args)
                {
                    System.Console.WriteLine("Running");
                    
                    //Authenticate to your subscription
                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    //Get an HDIManagement client
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    //Create a new cluster
                    CreateCluster();
                }
        
                public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();
        
                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };
        
                    if (username != null && password != null)
                        account.Id = username;
        
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
        
                    var accessToken =
                        authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                            .AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
        
        
                private static void CreateCluster()
                {
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        Location = NewClusterLocation,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,
        
                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingContainer,
        
                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,
                        SshUserName = NewClusterSshUserName,
                        SshPublicKey = NewClusterSshPublicKey
                    };
        
                    ScriptAction rScriptAction = new ScriptAction("Install R",
                        new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");
        
                    parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
                parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });
                    
                    _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
                }
        
            }
        }

		
10. クラス メンバーの値を置き換えます。

7. **F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。Azure アカウントの資格情報の入力も求められます。HDInsight クラスターの作成は数分かかる場合があります。通常は 15 分程度です。

##次のステップ

HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックをご覧ください。

###Hadoop クラスター

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

###HBase クラスター

* [HDInsight での Hbase の使用](hdinsight-hbase-tutorial-get-stared-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux)

###Storm クラスター

* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight での Storm の Python コンポーネントの使用](hdinsight-storm-develop-python.md)
* [HDInsight での Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO3-->