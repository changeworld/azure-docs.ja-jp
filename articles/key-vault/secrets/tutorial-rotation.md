---
title: シングル ユーザーまたはシングル パスワードのローテーションのチュートリアル
description: このチュートリアルを使用して、シングル ユーザーまたはシングル パスワードの認証を使用するリソースで、シークレットのローテーションを自動化する方法について学習します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f9c0dca29d173eb2c7893a20b2ab41dd31522e1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183213"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>シングル ユーザーまたはシングル パスワードの認証を使用するリソースのシークレットのローテーションを自動化する

Azure サービスに対する認証を行う最善の方法は[マネージド ID](../general/managed-identity.md) を使用することですが、この方法を選択できないシナリオもあります。 このような場合は、アクセス キーまたはシークレットが使用されます。 アクセス キーやシークレットは定期的にローテーションする必要があります。

このチュートリアルでは、シングル ユーザーまたはシングル パスワードの認証を使用するデータベースとサービスを対象に、シークレットの定期的なローテーションを自動化する方法について説明します。 具体的には、このチュートリアルでは Azure Event Grid の通知によってトリガーされる関数を使用して、Azure Key Vault に格納されている SQL Server のパスワードをローテーションします。

![ローテーション ソリューションの図](../media/rotate1.png)

1. シークレットの有効期限が切れる 30 日前に、Key Vault から "有効期限が近づいている" ことを示すイベントが Event Grid に発行されます。
1. Event Grid がイベント サブスクリプションを確認し、HTTP POST を使用して、このイベントをサブスクライブしている関数アプリ エンドポイントを呼び出します。
1. 関数アプリがシークレットの情報を受け取り、新たにランダムなパスワードを生成してから、新しいパスワードを使用して Key Vault にシークレットの新しいバージョンを作成します。
1. 関数アプリが新しいパスワードを使用して SQL Server を更新します。

> [!NOTE]
> ステップ 3 とステップ 4 の間にタイム ラグが生じることがあります。 その間、Key Vault 内のシークレットは、SQL Server に対して認証を行うことができません。 いずれかのステップで障害が発生した場合、Event Grid は 2 時間にわたって再試行します。

## <a name="create-a-key-vault-and-sql-server-instance"></a>キー コンテナーと SQL Server インスタンスを作成する

まず、キー コンテナーを作成し、SQL Server インスタンスとデータベースを作成して、SQL Server の管理者パスワードを Key Vault に保存します。

このチュートリアルでは、既存の Azure Resource Manager テンプレートを使用して、コンポーネントを作成します。 コードは、こちらで参照できます:[基本的なシークレット ローテーションのテンプレート サンプル](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates)に関するページ。

1. Azure テンプレートのデプロイのリンクを選択します。
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. **[リソース グループ]** で、 **[新規作成]** を選択します。 このグループに **simplerotation** という名前を付けます。
1. **[購入]** を選択します。

    ![リソース グループを作成する](../media/rotate2.png)

これで、キー コンテナー、SQL Server インスタンス、SQL データベースが完成しました。 このセットアップは、Azure CLI から次のコマンドを実行して検証できます。

```azurecli
az resource list -o table
```

次のような出力結果が得られます。

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Function App を作成する

システム マネージド ID を使用する関数アプリと、その他の必須コンポーネントを作成します。

関数アプリには次のコンポーネントが必要です。
- Azure App Service プラン
- ストレージ アカウント
- 関数アプリのマネージド ID を使用して Key Vault 内のシークレットにアクセスするためのアクセス ポリシー

1. Azure テンプレートのデプロイのリンクを選択します。
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. **[リソース グループ]** 一覧から **[simplerotation]** を選択します。
1. **[購入]** を選択します。

   ![[購入] を選択する](../media/rotate3.png)

上記の手順を完了すると、ストレージ アカウント、サーバー ファーム、関数アプリを使用できるようになります。 このセットアップは、Azure CLI から次のコマンドを実行して検証できます。

```azurecli
az resource list -o table
```

次のような出力結果が得られます。

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

関数アプリを作成し、マネージド ID を使用して Key Vault にアクセスする方法については、「[Azure portal から関数アプリを作成する](../../azure-functions/functions-create-function-app-portal.md)」と「[マネージド ID で Key Vault の認証を提供する](../general/managed-identity.md)」を参照してください。

### <a name="rotation-function"></a>ローテーション関数
この関数は、イベントを使用して、Key Vault と SQL データベースを更新することにより、シークレットのローテーションをトリガーします。

#### <a name="function-trigger-event"></a>関数のトリガー イベント

この関数は、イベント データを読み取り、ローテーション ロジックを実行します。

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>シークレットのローテーション ロジック
このローテーション メソッドでは、シークレットからデータベース情報を読み取り、新しいバージョンのシークレットを作成し、新しいシークレットを使用してデータベースを更新します。

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
[GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function) に完全なサンプル コードがあります。

#### <a name="function-deployment"></a>関数のデプロイ

1. [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip) から、関数アプリの ZIP ファイルをダウンロードします。

1. simplerotationsample-fn.zip というファイルを Azure Cloud Shell にアップロードします。

   ![ファイルをアップロードする](../media/rotate4.png)
1. 次の Azure CLI コマンドを使用して、関数アプリに ZIP ファイルをデプロイします。

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

関数のデプロイ後、simplerotation-fn に 2 つの関数が確認できます。

![SimpleRotation 関数と SimpleRotationHttpTest 関数](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>SecretNearExpiry イベントのイベント サブスクリプションを追加する

関数アプリの `eventgrid_extension` キーをコピーします。

   ![[Function App の設定] を選択する](../media/rotate6.png)

   ![eventgrid_extension キー](../media/rotate7.png)

コピーした `eventgrid_extension` キーとサブスクリプション ID を次のコマンドで使用して、`SecretNearExpiry` イベントの Event Grid サブスクリプションを作成します。

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Key Vault にシークレットを追加する
*シークレットの管理*権限をユーザーに付与するようにアクセス ポリシーを設定します。

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

SQL データベースのデータ ソースとユーザー ID を含むタグを使用して、新しいシークレットを作成します。 有効期限は翌日に設定します。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

有効期限が短いシークレットを作成すると、すぐに `SecretNearExpiry` イベントが発行されます。これにより、関数がトリガーされてシークレットがローテーションされます。

## <a name="test-and-verify"></a>テストして検証する
数分後、`sqluser` シークレットが自動的にローテーションされます。

シークレットのローテーションが完了したことを確認するために、 **[Key Vault]**  >  **[シークレット]** に移動します。

![[シークレット] に移動する](../media/rotate8.png)

**sqluser** シークレットを開き、元のバージョンとローテーション後のバージョンを確認します。

![sqluser シークレットを開く](../media/rotate9.png)

### <a name="create-a-web-app"></a>Web アプリを作成する

SQL の資格情報を確認するために、Web アプリを作成します。 この Web アプリは、Key Vault からシークレットを取得し、SQL データベースの情報と資格情報をシークレットから抽出して、SQL Server への接続をテストします。

Web アプリには次のコンポーネントが必要です。
- システム マネージド ID が付与されている Web アプリ
- Web アプリのマネージド ID を使用して Key Vault 内のシークレットにアクセスするためのアクセス ポリシー

1. Azure テンプレートのデプロイのリンクを選択します。
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. **simplerotation** リソース グループを選択します。
1. **[購入]** を選択します。

### <a name="deploy-the-web-app"></a>Web アプリのデプロイ

この Web アプリのソース コードは [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp) にあります。

Web アプリをデプロイするには、次の手順に従います。

1. [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip) から、関数アプリの ZIP ファイルをダウンロードします。
1. simplerotationsample-app.zip というファイルを Azure Cloud Shell にアップロードします。
1. 次の Azure CLI コマンドを使用して、関数アプリに ZIP ファイルをデプロイします。

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Web アプリを開く

デプロイされたアプリケーションに移動して URL を選択します。
 
![URL を選択する](../media/rotate10.png)

アプリケーションをブラウザーで開いている場合、**生成されたシークレット値**が表示され、**データベース接続済み**の値が *true* と表示されます。

## <a name="learn-more"></a>詳細情報

- 概要:[Azure Event Grid での Key Vault の監視 (プレビュー)](../general/event-grid-overview.md)
- 方法:[キー コンテナーのシークレットが変更されたときにメールを受信する](../general/event-grid-logicapps.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ (プレビュー)](../../event-grid/event-schema-key-vault.md)
