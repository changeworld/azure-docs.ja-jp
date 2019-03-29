---
title: クイック スタート:C# を使用して Azure Data Explorer クラスターとデータベースを作成する
description: C# を使用して Azure Data Explorer クラスターとデータベースを作成する方法を学習します
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: d861eba6ce905ccaf0d08a08cdd9998a199889da
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287373"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>C# を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


このクイック スタートでは、C# を使用して、Azure Data Explorer クラスターとデータベースを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

- このクイック スタートを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="install-c-nuget"></a>C# nuget をインストールする

- Azure Data Explorer (Kusto) 用の nuget パッケージが必要になります。Nuget はここから入手できます: https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/
- 認証用に nuget Microsoft.IdentityModel.Clients.ActiveDirectory も必要です: https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/


## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

1. 次のコードを使用してクラスターを作成します。

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    ar authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | clusterName | *mykustocluster* | クラスターの任意の名前。|
   | sku | *D13_v2* | クラスターに使用される SKU。 |
   | resourceGroupName | *testrg* | クラスターが作成されるリソース グループの名前。 |

    使用できる省略可能なパラメーターが他にも存在します (クラスターの容量など)。
    
    "credentials" には自分の資格情報を設定してください (詳細については、 https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet を参照してください)

2. クラスターが正常に作成されたかどうかを確認するには、次のコマンドを実行します。

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

結果に値が `Succeeded` の `ProvisioningState` が含まれている場合、クラスターは正常に作成されています。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

1. 次のコードを使用してデータベースを作成します。

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | clusterName | *mykustocluster* | データベースの作成先となるクラスターの名前。|
   | databaseName | *mykustodatabase* | データベースの名前。|
   | resourceGroupName | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | softDeletePeriod | *3650:00:00:00* | データをクエリに使用できるようにしておく時間。 |
   | hotCachePeriod | *3650:00:00:00* | データをキャッシュに保持する時間。 |

2. 次のコマンドを実行して、作成したデータベースを確認します。

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

* 他のクイック スタートやチュートリアルを行う場合は、作成したリソースをそのままにします。
* リソースをクリーンアップするには、クラスターを削除します。 クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 クラスターを削除するには次のコマンドを使います。

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート:Azure Data Explorer .NET Standard SDK (プレビュー) を使用してデータを取り込む](net-standard-ingest-data.md)
