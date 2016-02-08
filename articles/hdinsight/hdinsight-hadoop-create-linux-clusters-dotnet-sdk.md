<properties
   	pageTitle="HDInsight .NET SDK を使用した HDInsight 用の Linux ベースの Hadoop、HBase、Storm、または Spark クラスターの作成 | Microsoft Azure"
   	description="HDInsight .NET SDK を使用して、HDInsight 用の Linux ベースの Hadoop、HBase、Storm、または Spark クラスターを作成する方法について説明します。"
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
   	ms.date="01/05/2016"
   	ms.author="jgao"/>

#.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-create-linux-cluster-selector.md)]

HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。このドキュメントでは、.NET SDK を使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。

> [AZURE.IMPORTANT] このドキュメントの手順では、worker ノードが 1 つあるクラスターを作成します。クラスター作成または作成後の拡大で 32 以上の worker ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。
>
> ノードのサイズと関連コストに関する詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

###前提条件

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- __Visual Studio 2013 または 2015__

## クラスターの作成

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

        namespace CreateHDInsightCluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;

                private static Guid SubscriptionId = new Guid("<Enter Your Subscription ID>");
                private const string ExistingResourceGroupName = "<Enter Resource Group Name>";
                private const string ExistingStorageName = "<Enter Default Storage Account Name>.blob.core.windows.net";
                private const string ExistingStorageKey = "<Enter Default Storage Account Key>";
                private const string ExistingBlobContainer = "<Enter Default Bob Container Name>";
                private const string NewClusterName = "<Enter HDInsight Cluster Name>";
                private const int NewClusterNumNodes = 1;
                private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
                private const OSType NewClusterOSType = OSType.Linux;
                private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
                private const string NewClusterVersion = "3.2";
                private const string NewClusterUsername = "admin";
                private const string NewClusterPassword = "<Enter HTTP User Password>";
                private const string NewClusterSshUserName = "sshuser";
                private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
                    Comment: ""rsa-key-20150731""
                    AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
                    gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
                    yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
                    WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
                    pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
                    zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
                    ---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

                static void Main(string[] args)
                {
                    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        UserName = NewClusterUsername,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,

                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingBlobContainer,

                        Password = NewClusterPassword,
                        Location = NewClusterLocation,

                        SshUserName = NewClusterSshUserName,
                        SshPublicKey = NewClusterSshPublicKey
                    };

                    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

                    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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

                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), creds.Token);

                }
            }
        }

		
10. クラス メンバーの値を置き換えます。

7. **F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。Azure アカウントの資格情報の入力も求められます。HDInsight クラスターの作成は数分かかる場合があります。通常は 15 分程度です。

## ブートストラップの使用

詳細については、「[ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap)」を参照してください。

[クラスターの作成](#create-clusters)のサンプルを変更し、Hive の設定を構成します。

    static void Main(string[] args)
    {
        System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

        var tokenCreds = GetTokenCloudCredentials();
        var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

        _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

        var extendedParameters = new ClusterCreateParametersExtended
        {
            Location = NewClusterLocation,
            Properties = new ClusterCreateProperties
            {
                ClusterDefinition = new ClusterDefinition
                {
                    ClusterType = NewClusterType.ToString()
                },
                ClusterVersion = NewClusterVersion,
                OperatingSystemType = NewClusterOSType
            }
        };

        var coreConfigs = new Dictionary<string, string>
        {
            {"fs.defaultFS", string.Format("wasb://{0}@{1}", ExistingBlobContainer, ExistingStorageName)},
            {
                string.Format("fs.azure.account.key.{0}", ExistingStorageName),
                ExistingStorageKey
            }
        };

        // bootstrap
        var hiveConfigs = new Dictionary<string, string>
        {
            { "hive.metastore.client.socket.timeout", "90"}
        };

        var gatewayConfigs = new Dictionary<string, string>
        {
            {"restAuthCredential.isEnabled", "true"},
            {"restAuthCredential.username", NewClusterUsername},
            {"restAuthCredential.password", NewClusterPassword}
        };

        var configurations = new Dictionary<string, Dictionary<string, string>>
        {
            {"core-site", coreConfigs},
            {"gateway", gatewayConfigs},
            {"hive-site", hiveConfigs}
        };

        var serializedConfig = JsonConvert.SerializeObject(configurations);
        extendedParameters.Properties.ClusterDefinition.Configurations = serializedConfig;

        var sshPublicKeys = new List<SshPublicKey>();
        var sshPublicKey = new SshPublicKey
        {
            CertificateData =
                string.Format("ssh-rsa {0}", NewClusterSshPublicKey)
        };
        sshPublicKeys.Add(sshPublicKey);

        var headNode = new Role
        {
            Name = "headnode",
            TargetInstanceCount = 2,
            HardwareProfile = new HardwareProfile
            {
                VmSize = "Large"
            },
            OsProfile = new OsProfile
            {
                LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
                {
                    UserName = NewClusterSshUserName,
                    Password = NewClusterSshPassword //,
                    // When use a SSH pulbic key, make sure to remove comments, headers and trailers, and concatenate the key into one line 
                    //SshProfile = new SshProfile
                    //{
                    //    SshPublicKeys = sshPublicKeys
                    //}
                }
            }
        };

        var workerNode = new Role
        {
            Name = "workernode",
            TargetInstanceCount = NewClusterNumNodes,
            HardwareProfile = new HardwareProfile
            {
                VmSize = "Large"
            },
            OsProfile = new OsProfile
            {
                LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
                {
                    UserName = NewClusterSshUserName,
                    Password = NewClusterSshPassword //,
                    //SshProfile = new SshProfile
                    //{
                    //    SshPublicKeys = sshPublicKeys
                    //}
                }
            }
        };

        extendedParameters.Properties.ComputeProfile = new ComputeProfile();
        extendedParameters.Properties.ComputeProfile.Roles.Add(headNode);
        extendedParameters.Properties.ComputeProfile.Roles.Add(workerNode);

        _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, extendedParameters);

        System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
        System.Console.ReadLine();
    }


## Script Action の使用

詳細については、「[Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

[クラスターの作成](#create-clusters)のサンプルを変更し、Script Action を呼び出して、R をインストールします。

    static void Main(string[] args)
    {
        System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

        var tokenCreds = GetTokenCloudCredentials();
        var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

        _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
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

        System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
        System.Console.ReadLine();
    }

##次のステップ

HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックをご覧ください。

###Hadoop クラスター

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

###HBase クラスター

* [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-stared-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux)

###Storm クラスター

* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

###Spark クラスター

* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark と BI: HDInsight で BI ツールと Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0128_2016-->