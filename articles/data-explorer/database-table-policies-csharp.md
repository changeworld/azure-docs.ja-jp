---
title: Azure Data Explorer の C# SDK を使用してポリシーを作成する
description: この記事では、C# を使用してポリシーを作成する方法について説明します。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667305"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>C# を使用して Azure Data Explorer のデータベースとテーブルのポリシーを作成する

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 この記事では、C# を使用して Azure Data Explorer のデータベースとテーブルのポリシーを作成します。

## <a name="prerequisites"></a>前提条件

* Visual Studio 2019。 Visual Studio 2019 をお持ちでない場合は、*無料*の [Visual Studio Community 2019](https://www.visualstudio.com/downloads/) をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を選択してください。
* Azure サブスクリプション。 必要に応じて、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成できます。
* [テスト用のクラスターとデータベース](create-cluster-database-csharp.md)。
* [テスト用のテーブル](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)。

## <a name="install-c-nuget"></a>C# NuGet をインストールする

* [Azure Data Explorer (Kusto) NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)をインストールします。
* [Microsoft.Azure.Kusto.Data.NETStandard NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)をインストールします。 (省略可能。テーブルのポリシー変更用。)
* 認証用に、[Microsoft.IdentityModel.Clients.ActiveDirectory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)をインストールします。

## <a name="authentication"></a>認証
この記事の例を実行するには、リソースにアクセスできる Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルが必要です。 認証用に、[テスト用のクラスターとデータベース](create-cluster-database-csharp.md#authentication)と同じ Azure AD アプリケーションを使用できます。 別の Azure AD アプリケーションを使用する場合は、[Azure AD アプリケーションの作成](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)に関するページを参照して、無料の Azure AD アプリケーションを作成し、サブスクリプション スコープでロールの割り当てを追加します。 この記事では、`Directory (tenant) ID`、`Application ID`、および `Client secret` を取得する方法も示しています。 新しい Azure AD アプリケーションを、プリンシパルとしてデータベースに追加する必要が生じる場合があります。 詳細については、「[Azure Data Explorer のデータベース アクセス許可を管理する](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)」を参照してください。

## <a name="alter-database-retention-policy"></a>データベースの保持ポリシーを変更する
論理的な削除期間を 10 日間として、保持ポリシーを設定します。
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>データベースのキャッシュ ポリシーを変更する
データベースのキャッシュ ポリシーを設定します。 前の 5 日間のデータがクラスターの SSD に置かれます。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>テーブルのキャッシュ ポリシーを変更する
テーブルのキャッシュ ポリシーを設定します。 前の 5 日間のデータがクラスターの SSD に置かれます。

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>データベース用の新しいプリンシパルを追加する
新しい Azure AD アプリケーションをデータベースの管理者プリンシパルとして追加します。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>次の手順

* [データベースおよびテーブルのポリシーの詳細を確認する](https://docs.microsoft.com/azure/kusto/management/policies)
