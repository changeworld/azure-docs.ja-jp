---
title: シングル ユーザーまたはパスワードのローテーションのチュートリアル
description: シングル ユーザーまたはパスワードのローテーションを自動化するには、このチュートリアルを利用してください
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223359"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>シングル ユーザーまたはパスワードの認証を使用してリソースのシークレットのローテーションを自動化する

Azure サービスに対する認証を行う最善の方法は[マネージド ID](managed-identity.md) を使用することですが、この方法を選択できないシナリオもあります。 このような場合は、アクセス キーまたはシークレットが使用されます。 アクセス キーまたはシークレットは定期的にローテーションする必要があります。

このチュートリアルでは、シングル ユーザーまたはパスワードの認証を使用してデータベースとサービスのシークレットの定期的なローテーションを自動化する方法について説明します。 具体的には、このシナリオでは Event Grid の通知によってトリガーされる関数を使用して、キー コンテナーに格納されている SQL Server のパスワードをローテーションします。

![ローテーション図](./media/rotate1.png)

1. シークレットの有効期限が切れる 30 日前に、Key Vault から "有効期限が近づいている" ことを示すイベントが Event Grid に発行されます。
1. Event Grid がイベント サブスクリプションを確認し、HTTP POST を使用して、このイベントをサブスクライブしている関数アプリ エンドポイントを呼び出します。
1. 関数アプリがシークレットの情報を受け取り、新たにランダムなパスワードを生成してから、新しいパスワードを使用して Key Vault にシークレットの新しいバージョンを作成します。
1. 関数アプリが新しいパスワードを使用して SQL を更新します。

> [!NOTE]
> 手順 3. と 4. の間には遅延が発生する可能性があります。その間、Key Vault 内のシークレットは、SQL に対する認証を行ううえで有効ではありません。 いずれかの手順で障害が発生した場合、Event Grid は 2 時間にわたって再試行します。

## <a name="setup"></a>セットアップ

## <a name="create-a-key-vault-and-sql-server"></a>キー コンテナーと SQL サーバーを作成する

開始する前に、キー コンテナーを作成し、SQL サーバーと SQL データベースを作成して、SQL Server の管理者パスワードを Key Vault に保存する必要があります。

このチュートリアルでは、事前に作成された Azure Resource Manager テンプレートを使用して、コンポーネントを作成します。 コードの全体はこちらで参照できます: [基本的なシークレット ローテーションのテンプレート サンプル](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates)に関するページ。

1. Azure テンプレートのデプロイのリンクをクリックします。
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. [リソース グループ] で [新規作成] を選択し、"simplerotation" という名前を付けます。
1. [購入] を選択します。

    ![新しいリソース グループの作成](./media/rotate2.png)

これらの手順を完了すると、キー コンテナー、SQL サーバー、および SQL データベースを使用できるようになります。 これを確認するには、Azure CLI ターミナルで次を実行します。

```azurecli
az resource list -o table
```

結果は次のようになります。

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>関数アプリを作成する

システムのマネージド ID を使用する関数アプリと、追加の必須コンポーネントを作成します。 

関数アプリでは、次のコンポーネントと構成が必要です。
- App Service プラン
- ストレージ アカウント
- 関数アプリのマネージド ID を使用して Key Vault 内のシークレットにアクセスするためのアクセス ポリシー

1. Azure テンプレートのデプロイのリンクをクリックします。
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. [リソース グループ] で、[simplerotation] を選択します。
1. [購入] を選択します。

   ![購入画面](./media/rotate3.png)

上記の手順を完了すると、ストレージ アカウント、サーバー ファーム、および関数アプリを使用できるようになります。  これを確認するには、Azure CLI ターミナルで次を実行します。

```azurecli
az resource list -o table
```

結果は次のようになります。

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

関数アプリを作成し、マネージド ID を使用して Key Vault にアクセスする方法の詳細については、「[Azure portal から関数アプリを作成する](../azure-functions/functions-create-function-app-portal.md)」と「[マネージド ID で Key Vault の認証を提供する](managed-identity.md)」を参照してください。

### <a name="rotation-function-and-deployment"></a>ローテーションの関数とデプロイ
関数は、イベントをトリガーとして使用し、Key Vault と SQL データベースを更新しながらシークレットのローテーションを実行します。

#### <a name="function-event-trigger-handler"></a>関数イベント トリガー ハンドラー

次の関数は、イベント データを読み取り、ローテーション ロジックを実行します。

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
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
コードの全体はこちらで参照できます: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function 。

#### <a name="function-deployment"></a>関数のデプロイ

1. 関数アプリの ZIP ファイルをダウンロードします: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip 。

1. simplerotationsample-fn.zip というファイルを Azure Cloud Shell にアップロードします。
 
1. 次の CLI コマンドを使用して、関数アプリに ZIP ファイルをデプロイします。

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![購入画面](./media/rotate4.png)

デプロイ後は、"simplerotation-fn" の下に次の 2 つの関数が表示されます。

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>"SecretNearExpiry" イベントのイベント サブスクリプションを追加する

関数アプリの eventgrid_extension キーをコピーします。

![Azure Cloud Shell](./media/rotate6.png)

![テストして検証する](./media/rotate7.png)

コピーした eventgrid 拡張キーとサブスクリプション ID を次のコマンドで使用して、SecretNearExpiry イベントの Event Grid サブスクリプションを作成します。

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>キー コンテナーにシークレットを追加する
ユーザーに "シークレットの管理" 権限を付与するようにアクセス ポリシーを設定します。

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

次に、SQL データベースのデータソースとユーザー ID を含むタグを持つ新しいシークレットを作成します。有効期限は明日に設定します。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

有効期限が短いシークレットを作成すると、すぐに SecretNearExpiry イベントが発行されます。これにより、関数がトリガーされてシークレットがローテーションされます。

### <a name="test-and-verify"></a>テストして検証する
数分後、sqluser シークレットが自動的にローテーションされます。

シークレット ローテーションを検証するには、[Key Vault]、[シークレット] の順に移動します。

![テストして検証する](./media/rotate8.png)

"sqluser" シークレットを開き、元のバージョンとローテーション後のバージョンを確認します。

![テストして検証する](./media/rotate9.png)

## <a name="create-web-app"></a>Create Web App

SQL の資格情報を確認するには、Web アプリケーションを作成します。 この Web アプリケーションは、Key Vault からシークレットを取得し、SQL データベースの情報と資格情報をシークレットから抽出して、SQL への接続をテストします。

この Web アプリでは、次のコンポーネントと構成が必要です。
- システムのマネージド ID が付与されている Web アプリ
- Web アプリのマネージド ID を使用して Key Vault 内のシークレットにアクセスするためのアクセス ポリシー

1. Azure テンプレートのデプロイのリンクをクリックします。
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. **simplerotation** リソース グループを選択します。
1. [購入] をクリックします。

### <a name="deploy-web-app"></a>Web アプリをデプロイする

Web アプリのソースコードは、 https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp で確認できます。Web アプリをデプロイするには、次の手順を実行します。

1. https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip から、関数アプリの ZIP ファイルをダウンロードします。
1. `simplerotationsample-app.zip` というファイルを Azure Cloud Shell にアップロードします。
1. 次の Azure CLI コマンドを使用して、関数アプリに ZIP ファイルをデプロイします。

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Web アプリケーションを開く

デプロイされたアプリケーションに移動し、[URL] をクリックします。
 
![テストして検証する](./media/rotate10.png)

生成されたシークレット値が表示され、[Database Connected]\(データベース接続済み\) が true と示されます。

## <a name="learn-more"></a>詳細情報:

- 概要:[Azure Event Grid での Key Vault の監視 (プレビュー)](event-grid-overview.md)
- 方法:[キー コンテナーのシークレットが変更されたときにメールを受信する](event-grid-logicapps.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ (プレビュー)](../event-grid/event-schema-key-vault.md)
