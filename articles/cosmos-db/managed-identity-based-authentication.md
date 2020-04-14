---
title: システム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスする方法
description: Azure Cosmos DB のキーにアクセスするように Azure AD システム割り当てマネージド ID を構成する方法について説明します。 msi, マネージド サービス ID, aad, azure active directory, ID
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80416976"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>システム割り当てマネージド ID を使用して Azure Cosmos DB にアクセスする方法

この記事では、[マネージド ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) を利用して Azure Cosmos DB キーにアクセスするための、**堅牢でキー ローテーションに依存しない**ソリューションを設定します。 この記事の例では、Azure 関数を使用します。 ただし、このソリューションは、マネージド ID をサポートするすべてのサービスでご利用いただけます。 

ここでは、Azure Cosmos DB キーをコピーしなくても Azure Cosmos DB にアクセスできる Azure 関数を作成する方法について説明します。 この関数は、1分ごとにウェイクアップし、水族館の水槽の現在の気温を記録します。 タイマーでトリガーされる Azure 関数の設定方法については、「[Azure でタイマーによってトリガーされる関数を作成する](../azure-functions/functions-create-scheduled-function.md)」の記事を参照してください。

このシナリオを簡略化するため、古い温度ドキュメントのクリーンアップは、既に構成されている [有効期限](./time-to-live.md)の設定によって処理されます。 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Azure 関数にシステム割り当てマネージド ID を割り当てる

このステップでは、Azure 関数にシステム割り当てマネージド ID を割り当てます。

1. [Azure portal](https://portal.azure.com/) で、**Azure 関数**ウィンドウを開き、関数アプリに移動します。 

1. **[プラットフォーム機能]**  >  **[ID]** タブの順に開きます。 

   ![[ID] タブ](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. **[ID]** タブで、 **[システム ID]** 状態を **[オン]** にします。 必ず **[保存]** を選択し、システム ID をオンにすることを確認します。 最後に、 **[システム ID]** ウィンドウは次のようになります。  

   ![システム ID がオンの状態](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>マネージド ID に Azure Cosmos アカウントへのアクセス権を付与する

このステップでは、Azure 関数のシステム割り当てマネージド ID にロールを割り当てます。 Azure Cosmos DB には、マネージド ID に割り当てることが可能な複数の組み込みロールがあります。 このソリューションでは、次の 2 つのロールを使用します。

|組み込みのロール  |説明  |
|---------|---------|
|[DocumentDB Account Contributor](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB アカウントを管理できます。 読み取り/書き込みキーの取得を許可します。 |
|[Cosmos DB アカウント閲覧者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Cosmos DB アカウントのデータを読み取ることができます。 読み取りキーの取得を許可します。 |

> [!IMPORTANT]
> Azure Cosmos DB の RBAC サポートは、コントロール プレーン操作にのみ適用されます。 データ プレーン操作は、マスター キーまたはリソース トークンを使用してセキュリティで保護されています。 詳細については、「[データへのアクセスをセキュリティで保護する](secure-access-to-data.md)」の記事を参照してください。

> [!TIP] 
> ロールを割り当てるときは、必要なアクセス権のみを割り当ててください。 サービスがデータの読み取りのみを必要とする場合は、マネージド ID を **Cosmos DB アカウントの閲覧者**ロールに割り当てます。 最小特権アクセスの重要性についての詳細は、「[特権アカウントの公開時間を短縮する](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)」の記事を参照してください。

シナリオに合わせて、気温を読み取り、Azure Cosmos DB のコンテナーにそのデータを書き戻します。 データを書き込む必要があるため、**DocumentDB Account Contributor** ロールを使用します。 

1. Azure portal にサインインし、Azure Cosmos DB アカウントに移動します。 **[アクセス管理 (IAM)] ウィンドウ**、 **[ロールの割り当て]** タブの順に開きます。

   ![IAM ウィンドウ](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. **[+ 追加]** ボタン、 **[ロールの割り当てを追加する]** の順に選択します。

1. **[ロールの割り当ての追加]** ウィンドウが右側に表示されます。

   ![ロールの追加](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **[ロール]** - **[DocumentDB Account Contributor]** を選択します。
   * **[アクセスの割り当て先]** - [**システム割り当てマネージド ID]** の選択のサブセクションで、 **[関数アプリ]** を選択します。
   * **[選択]** - このウィンドウには、サブスクリプション内の、 **[マネージド システム ID]** を持つすべての関数アプリが表示されます。 ここでは、関数アプリ **SummaryService** を選択します。 

      ![割り当てを選択する](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. 関数アプリの ID を選択したら、 **[保存]** をクリックします。

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Azure 関数からプログラムを使用して Azure Cosmos DB キーにアクセスする

これで、システム割り当てマネージド ID を持つ関数アプリができました。 この ID には、Azure Cosmos DB のアクセス許可で **DocumentDB Account Contributor** ロールが付与されています。 次の関数アプリ コードは、Azure Cosmos DB キーを取得し、CosmosClient オブジェクトを作成し、気温を取得して、これを Cosmos DB に保存します。

このサンプルでは、[List Keys API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) を使用して、Azure Cosmos DB アカウント キーにアクセスします。

> [!IMPORTANT] 
> [**Cosmos DB アカウント閲覧者**](#grant-the-managed-identity-access-to-your-azure-cosmos-account)ロールを割り当てるには、読み取り専用の [List Keys api](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys) を使用する必要があります。 読み取り専用キーのみが設定されます。

List Keys API は `DatabaseAccountListKeysResult` オブジェクトを返します。 この型は C# ライブラリで定義されていません。 次のコードは、このクラスを実装する方法を示しています。  

```csharp 
namespace SummarizationService 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

また、この例では、次のように定義されている "TemperatureRecord" という単純なドキュメントも使用します。

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ライブラリを使用して、システム割り当てマネージド ID トークンを取得します。 トークンを取得する他の方法と、`Microsoft.Azure.Service.AppAuthentication`ライブラリの詳細については、「[サービス間認証](../key-vault/service-to-service-authentication.md)」の記事を参照してください。

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class TemperatureMonitor
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

これで [Azure 関数をデプロイする](../azure-functions/functions-create-first-function-vs-code.md)準備が整いました。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB と Active Directory Domain Services を使用した証明書ベースの認証](certificate-based-authentication.md)
* [Azure Key Vault を使用して Azure Cosmos キーをセキュリティで保護する](access-secrets-from-keyvault.md)
* [Azure Cosmos DB のセキュリティ ベースライン](security-baseline.md)
