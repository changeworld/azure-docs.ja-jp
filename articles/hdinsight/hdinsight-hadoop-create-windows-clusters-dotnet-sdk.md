---
title: ".NET SDK を使用した HDInsight での Windows ベースの Hadoop クラスターの作成 | Microsoft Docs"
description: ".NET SDK テンプレートを使用して Azure HDInsight の HDInsight クラスターを作成する方法について説明します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 134fbcdf-8f62-492f-84fd-8e2a3e0cd896
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 21f2a14c1514c3f70787613c5f0962dc3eabd7b1


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-net-sdk"></a>.NET SDK を使用した HDInsight での Windows ベースの Hadoop クラスターの作成
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

.NET SDK を使用して HDInsight クラスターを作成する方法について説明します。 その他のクラスター作成のツールと機能については、このページの上部にあるタブ セレクターをクリックするか、「 [クラスターの作成方法](hdinsight-provision-clusters.md#cluster-creation-methods)」を参照してください。

## <a name="prerequisites"></a>前提条件:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

この記事の手順を開始する前に、次の項目を用意する必要があります。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* Visual Studio 2013 または 2015

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>クラスターの作成
HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。 Visual Studio コンソール アプリケーションを作成し、クラスターを作成するためのコードを貼り付けるには、次の手順に従います。

アプリケーションには、Azure リソース グループと既定のストレージ アカウントが必要です。  「 [付録 A](#appx-a-create-dependent-components) 」では、依存コンポーネントを作成する PowerShell スクリプトについて説明します。

**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。
2. NuGet パッケージ管理コンソールで次の Nuget コマンドを実行します。
   
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight
3. ソリューション エクスプローラーで、 **Program.cs** をダブルクリックして開き、次のコードを貼り付け、変数の値を指定します。
   
        using System;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Net.Http;
   
        namespace CreateHDInsightCluster
        {
            class Program
            {
                // The client for managing HDInsight
                private static HDInsightManagementClient _hdiManagementClient;
                // Replace with your AAD tenant ID if necessary
                private const string TenantId = UserTokenProvider.CommonTenantId; 
                private const string SubscriptionId = "<Your Azure Subscription ID>";
                // This is the GUID for the PowerShell client. Used for interactive logins in this example.
                private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
                private const string ExistingResourceGroupName = "<Azure Resource Group Name>";
                private const string ExistingStorageName = "<Default Storage Account Name>.blob.core.windows.net";
                private const string ExistingStorageKey = "<Default Storage Account Key>";
                private const string ExistingBlobContainer = "<Default Blob Container Name>";
                private const string NewClusterName = "<HDInsight Cluster Name>";
                private const int NewClusterNumWorkerNodes = 2;
                private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
                private const OSType NewClusterOsType = OSType.Windows;
                private const string NewClusterType = "Hadoop";
                private const string NewClusterVersion = "3.2";
                private const string NewClusterUsername = "admin";
                private const string NewClusterPassword = "<HTTP User password>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

                    // Authenticate and get a token
                    var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                    // Flag subscription for HDInsight, if it isn't already.
                    EnableHDInsight(authToken);
                    // Get an HDInsight management client
                    _hdiManagementClient = new HDInsightManagementClient(authToken);

                    // Set parameters for the new cluster
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumWorkerNodes,
                        UserName = NewClusterUsername,
                        Password = NewClusterPassword,
                        Location = NewClusterLocation,
                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingBlobContainer,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOsType
                    };
                    // Create the cluster
                    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

                    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                /// <summary>
                /// Authenticate to an Azure subscription and retrieve an authentication token
                /// </summary>
                /// <param name="TenantId">The AAD tenant ID</param>
                /// <param name="ClientId">The AAD client ID</param>
                /// <param name="SubscriptionId">The Azure subscription ID</param>
                /// <returns></returns>
                static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
                    var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                        ClientId, 
                        new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                        PromptBehavior.Always, 
                        UserIdentifier.AnyUser);
                    return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
                }
                /// <summary>
                /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
                /// </summary>
                /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
                /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
                /// <param name="authToken">An authentication token for your Azure subscription</param>
                static void EnableHDInsight(TokenCloudCredentials authToken)
                {
                    // Create a client for the Resource manager and set the subscription ID
                    var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
                    resourceManagementClient.SubscriptionId = SubscriptionId;
                    // Register the HDInsight provider
                    var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
                }
            }
        }

1. **F5** キーを押してアプリケーションを実行します。 コンソール ウィンドウが開き、アプリケーションの状態が表示されます。 Azure アカウントの資格情報の入力も求められます。 HDInsight クラスターの作成は数分かかる場合があります。

## <a name="next-steps"></a>次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。 詳細については、次の記事を参照してください。

* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight クラスターの使用方法について説明しています。
* [.NET SDK を使用して HDInsight で Hive ジョブを実行する](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [.NET SDK を使用して HDInsight で Pig ジョブを実行する](hdinsight-hadoop-use-pig-dotnet-sdk.md)
* [.NET SDK を使用して HDInsight で Sqoop ジョブを実行する](hdinsight-hadoop-use-sqoop-dotnet-sdk.md)
* [HDInsight で Oozie ジョブを実行する](hdinsight-use-oozie.md)
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation] - HDInsight SDK の検出

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx


## <a name="appx-a-create-dependent-components"></a>付録 A 依存コンポーネントの作成
次の Azure PowerShell スクリプトを使用して、このチュートリアルでの .NET アプリケーションで必要な依存コンポーネントを作成できます。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    Write-Host "Use the following names in your .NET application" -ForegroundColor Green
    Write-host "Resource Group Name: $resourceGroupName"
    Write-host "Default Storage Account Name: $defaultStorageAccountName"
    Write-host "Default Storage Account Key: $defaultStorageAccountKey"
    Write-host "Default Blob Container Name: $defaultBlobContainerName"



<!--HONumber=Nov16_HO3-->


