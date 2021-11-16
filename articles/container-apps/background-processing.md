---
title: 'チュートリアル: Azure Container Apps プレビューを使用してバックグラウンド処理アプリケーションをデプロイする'
description: Azure Container Apps を使ってバックグラウンドで継続的に実行されるアプリケーションを作成する方法について説明します
services: app-service
author: jorgearteiro
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: joarteir
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c5cda6e2793ca70c1d53b8a4b4ce4235fa07efd
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577671"
---
# <a name="tutorial-deploy-a-background-processing-application-with-azure-container-apps-preview"></a>チュートリアル: Azure Container Apps プレビューを使用してバックグラウンド処理アプリケーションをデプロイする

Azure Container Apps を使うと、パブリック エンドポイントを公開することなく、アプリケーションをデプロイできます。 このチュートリアルでは、Azure ストレージ キューからメッセージを読み取り、Azure Log Analytics ワークスペースにメッセージを記録するサンプル アプリケーションをデプロイします。 Container Apps のスケーリング ルールを使うと、Azure ストレージ キューの長さに基づいてアプリケーションをスケールアップおよびスケールダウンできます。 キューにメッセージがない場合、コンテナー アプリは 0 にスケールダウンされます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * コンテナー アプリをデプロイするための Container Apps 環境を作成する
> * コンテナー アプリにメッセージを送信するための Azure ストレージ キューを作成する
> * バックグラウンド処理アプリケーションをコンテナー アプリとしてデプロイする
> * キューのメッセージがコンテナー アプリによって処理されることを確認する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには以下のものが必要です。

* **Azure CLI**: ローカル コンピューターに Azure CLI バージョン 2.29.0 以降がインストールされている必要があります。
  * バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。
  
## <a name="setup"></a>セットアップ

このチュートリアルでは、次の環境変数を使います。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
```

---

ストレージ アカウント名用の変数を作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<MY_STORAGE_ACCOUNT_NAME>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

このスニペットを実行する前に、`<storage account name>` プレースホルダーを自分の値に置き換えます。 ストレージ アカウント名は、Azure 内で一意で、長さは 3 から 24 文字にする必要があり、数字と小文字のみを使用できます。 ストレージ アカウントは、次のステップで作成されます。

次に、CLI から Azure にサインインします。

次のコマンドを実行し、プロンプトに従って認証プロセスを完了します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az login
```

---

最新バージョンの CLI を実行していることを確認するには、`upgrade` コマンドを使います。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az upgrade
```

---

次に、CLI に Azure Container Apps 拡張機能をインストールします。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az extension add \
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az extension add `
  --source https://workerappscliextension.blob.core.windows.net/azure-cli-extension/containerapp-0.2.0-py2.py3-none-any.whl
```

---

拡張機能がインストールされたので、`Microsoft.Web` 名前空間を登録します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az provider register --namespace Microsoft.Web
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az provider register --namespace Microsoft.Web
```

---

リソース グループを使って、新しいコンテナー アプリに関連するサービスを整理します。 次のコマンドでグループを作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az group create `
  --name $RESOURCE_GROUP `
  --location $LOCATION
```

---

CLI がアップグレードされ、新しいリソース グループが使用可能になったら、Container Apps 環境を作成し、コンテナー アプリをデプロイできます。

## <a name="create-an-environment"></a>環境の作成

Azure Container Apps 環境は、コンテナー アプリのグループを囲むセキュリティ保護された境界として機能します。 同じ環境内の異なるコンテナー アプリは、同じ仮想ネットワークにデプロイされ、同じ Log Analytics ワークスペースにログを書き込みます。

Azure Log Analytics は、Container Apps 環境を作成するときに必要なコンテナー アプリを監視するために使われます。

次のコマンドを使用して、新しい Log Analytics ワークスペースを作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

次に、Log Analytics クライアント ID とクライアント シークレットを取得します。 要求が完了するのに十分な時間を与えるために、各クエリを個別に実行してください。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

```azurecli
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"')
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out json | tr -d '"'`
```

---

個々のコンテナー アプリが、Azure Container Apps 環境にデプロイされます。 環境を作成するために、次のコマンドを実行します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp env create \
  --name $CONTAINERAPPS_ENVIRONMENT \
  --resource-group $RESOURCE_GROUP \
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location $LOCATION
```

---

## <a name="set-up-a-storage-queue"></a>ストレージ キューを設定する

Azure ストレージ アカウントを作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage account create \
  --name $STORAGE_ACCOUNT \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION" \
  --sku Standard_RAGRS \
  --kind StorageV2
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location $LOCATION `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

次に、キューの接続文字列を取得します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
QUEUE_CONNECTION_STRING=`az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"'`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$QUEUE_CONNECTION_STRING=(az storage account show-connection-string -g $RESOURCE_GROUP --name $STORAGE_ACCOUNT --query connectionString --out json | tr -d '"')
```

---

これで、メッセージ キューを作成できます。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage queue create \
  --name "myqueue" \
  --account-name $STORAGE_ACCOUNT \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage queue create `
  --name "myqueue" `
  --account-name $STORAGE_ACCOUNT `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

最後に、キューにメッセージを送信できます。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az storage message put \
  --content "Hello Queue Reader App" \
  --queue-name "myqueue" \
  --connection-string $QUEUE_CONNECTION_STRING
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az storage message put `
  --content "Hello Queue Reader App" `
  --queue-name "myqueue" `
  --connection-string $QUEUE_CONNECTION_STRING
```

---

## <a name="deploy-the-background-application"></a>バックグラウンド アプリケーションをデプロイする

*queue.json* というファイルを作成し、次の構成コードをファイルに貼り付けます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "",
            "type": "String"
        },
        "queueconnection": {
            "defaultValue": "",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
    {
        "name": "queuereader",
        "type": "Microsoft.Web/containerApps",
        "apiVersion": "2021-03-01",
        "kind": "containerapp",
        "location": "[parameters('location')]",
        "properties": {
            "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
            "configuration": {
                "activeRevisionsMode": "single",
                "secrets": [
                {
                    "name": "queueconnection",
                    "value": "[parameters('queueconnection')]"
                }]
            },
            "template": {
                "containers": [
                    {
                        "image": "mcr.microsoft.com/azuredocs/containerapps-queuereader",
                        "name": "queuereader",
                        "env": [
                            {
                                "name": "QueueName",
                                "value": "myqueue"
                            },
                            {
                                "name": "QueueConnectionString",
                                "secretref": "queueconnection"
                            }
                        ]
                    }
                ],
                "scale": {
                    "minReplicas": 1,
                    "maxReplicas": 10,
                    "rules": [
                        {
                            "name": "myqueuerule",
                            "azureQueue": {
                                "queueName": "myqueue",
                                "queueLength": 100,
                                "auth": [
                                    {
                                        "secretRef": "queueconnection",
                                        "triggerParameter": "connection"
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    }]
}

```

これで、コンテナー アプリを作成してデプロイできます。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az deployment group create --resource-group "$RESOURCE_GROUP" \
  --template-file ./queue.json \
  --parameters \
    environment_name="$CONTAINERAPPS_ENVIRONMENT" \
    queueconnection="$QUEUE_CONNECTION_STRING" \
    location="$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az deployment group create --resource-group "$RESOURCE_GROUP" `
  --template-file ./queue.json `
  --parameters `
    environment_name="$CONTAINERAPPS_ENVIRONMENT" `
    queueconnection="$QUEUE_CONNECTION_STRING" `
    location="$LOCATION"
```

---

このコマンドを実行すると、`mcr.microsoft.com/azuredocs/containerapps-queuereader` というパブリック コンテナー イメージからデモ アプリケーションがデプロイされ、アプリケーションで使われるシークレットと環境変数が設定されます。

アプリケーションは、ARM テンプレートの `scale` セクションで定義されているように、キューの長さに基づいて最大 10 レプリカまでスケールアップされます。

## <a name="verify-the-result"></a>結果を確認する

Azure ストレージ キューからメッセージが到着すると、バックグラウンド プロセスとして実行されているコンテナー アプリにより、Log Analytics にログ エントリが作成されます。 ログに記録されたデータに対してクエリを実行する前に、初めて分析が到着するまでに数分かかることがあります。

ログに記録されたメッセージを表示するには、次のコマンドを実行します。 このコマンドには Log Analytics 拡張機能が必要なので、要求されたらプロンプトを受け入れて拡張機能をインストールします。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'queuereader' and Log_s contains 'Message ID'" `
  --out table
```

---

> [!TIP]
> 問題がある場合は、 GitHub の [Azure Container Apps リポジトリ](https://github.com/microsoft/azure-container-apps)で問題を開いて、お知らせください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

完了したら、次のコマンドを実行してリソース グループを削除し、Container Apps リソースをクリーンアップします。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az group delete `
  --resource-group $RESOURCE_GROUP
```

---

このコマンドを実行すると、Container Apps インスタンス、ストレージ アカウント、Log Analytics ワークスペース、およびリソース グループ内の他のすべてのリソースを含む、リソース グループ全体が削除されます。
