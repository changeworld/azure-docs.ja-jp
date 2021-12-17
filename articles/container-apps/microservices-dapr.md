---
title: 'チュートリアル: Azure CLI を使用して Dapr アプリケーションを Azure Container Apps にデプロイする'
description: Azure CLI を使用して、Dapr アプリケーションを Azure Container Apps にデプロイします。
services: app-service
author: asw101
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aawislan
ms.custom: ignite-fall-2021
ms.openlocfilehash: cbca0dbd5449e5461e938d7c252c06f2ef0c18e3
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578919"
---
# <a name="tutorial-deploy-a-dapr-application-to-azure-container-apps-using-the-azure-cli"></a>チュートリアル: Azure CLI を使用して Dapr アプリケーションを Azure Container Apps にデプロイする

[Dapr](https://dapr.io/) (Distributed Apps Runtime) は、回復性がある、ステートレス、ステートフルのマイクロサービスを構築するのに役立つランタイムです。 このチュートリアルでは、サンプルの Dapr アプリケーションを Azure Container Apps にデプロイします。

学習内容は次のとおりです。

> [!div class="checklist"]
> * コンテナー アプリのための Container Apps 環境を作成する
> * コンテナー アプリのための Azure Blob Storage 状態ストアを作成する
> * メッセージを生成および使用し、それらを状態ストアを使用して永続化する 2 つのアプリをデプロイする
> * 2 つのマイクロサービス間の相互作用を確認する。

Azure Container Apps は、マイクロサービスを構築するときに、Dapr API のフル マネージド バージョンを提供します。 Azure Container Apps で Dapr を使用する場合は、マイクロサービスの横で豊富な機能を提供する、サイドカーを有効にすることができます。 使用可能な Dapr API には、[サービス間の呼び出し](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/)、[Pub/Sub](https://docs.dapr.io/developing-applications/building-blocks/pubsub/)、[イベント バインド](https://docs.dapr.io/developing-applications/building-blocks/bindings/)、[状態ストア](https://docs.dapr.io/developing-applications/building-blocks/state-management/)、[アクター](https://docs.dapr.io/developing-applications/building-blocks/actors/)が含まれます。

このチュートリアルでは、Dapr の [Hello World](https://github.com/dapr/quickstarts/tree/master/hello-kubernetes) クイックスタートと同じアプリケーションをデプロイします。これは、メッセージを生成するクライアント (Python) アプリと、それらのメッセージを使用して構成済みの状態ストアに保持するサービス (Node) アプリで構成されます。 次のアーキテクチャ図は、このチュートリアルを構成するコンポーネントを示しています。

:::image type="content" source="media/microservices-dapr/azure-container-apps-microservices-dapr.png" alt-text="Azure Container Apps での Dapr Hello World マイクロサービスのアーキテクチャ図":::

## <a name="prerequisites"></a>前提条件

* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>開始する前に

このガイドでは、次の環境変数を使用します。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOCATION="canadacentral"
CONTAINERAPPS_ENVIRONMENT="containerapps-env"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"
STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOCATION="canadacentral"
$CONTAINERAPPS_ENVIRONMENT="containerapps-env"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"
$STORAGE_ACCOUNT_CONTAINER="mycontainer"
```

---

上のスニペットを使用すると、bash、zsh、または PowerShell を使用して環境変数を設定できます。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT="<storage account name>"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT="<storage account name>"
```

---

`STORAGE_ACCOUNT` の名前を選択します。 これは後の手順で作成されます。 ストレージ アカウント名は、Azure 内で一意で、長さは 3 から 24 文字にする必要があり、数字と小文字のみを使用できます。

## <a name="setup"></a>セットアップ

まず、CLI から Azure にサインインします。

次のコマンドを実行し、プロンプトに従って認証プロセスを完了します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
```

---

upgrade コマンドを使用して、最新バージョンの CLI を実行していることを確認します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az upgrade
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
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

```azurecli
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

新しいコンテナー アプリに関連するサービスを整理するためのリソース グループを作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create \
  --name $RESOURCE_GROUP \
  --location "$LOCATION"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group create `
  --name $RESOURCE_GROUP `
  --location "$LOCATION"
```

---

CLI がアップグレードされ、新しいリソース グループが使用可能になったら、Container Apps 環境を作成し、コンテナー アプリをデプロイできます。

## <a name="create-an-environment"></a>環境の作成

Azure Container Apps 環境は、コンテナー アプリのグループ間の分離境界として機能します。 同じ環境にデプロイされたコンテナー アプリは、同じ仮想ネットワークにデプロイされ、同じ Log Analytics ワークスペースにログを書き込みます。

Azure Log Analytics は、コンテナー アプリを監視するために使用されます。これは Container Apps 環境を作成するときには必須です。

次のコマンドを使用して、新しい Log Analytics ワークスペースを作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics workspace create \
  --resource-group $RESOURCE_GROUP \
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics workspace create `
  --resource-group $RESOURCE_GROUP `
  --workspace-name $LOG_ANALYTICS_WORKSPACE
```

---

次に、Log Analytics クライアント ID とクライアント シークレットを取得します。

# <a name="bash"></a>[Bash](#tab/bash)

要求が完了するのに十分な時間を与えるために、各クエリを個別に実行してください。

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

```bash
LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要求が完了するのに十分な時間を与えるために、各クエリを個別に実行してください。

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=(az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
```

```powershell
$LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=(az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv)
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

```azurecli
az containerapp env create `
  --name $CONTAINERAPPS_ENVIRONMENT `
  --resource-group $RESOURCE_GROUP `
  --logs-workspace-id $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --logs-workspace-key $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET `
  --location "$LOCATION"
```

---

## <a name="set-up-a-state-store"></a>状態ストアを設定する

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage アカウントを作成する

次のコマンドを使用して、新しい Azure Storage アカウントを作成します。

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

```azurecli
az storage account create `
  --name $STORAGE_ACCOUNT `
  --resource-group $RESOURCE_GROUP `
  --location "$LOCATION" `
  --sku Standard_RAGRS `
  --kind StorageV2
```

---

Azure Blob Storage アカウントを作成したら、このチュートリアルの後続の手順のために次の値が必要になります。

* `storage_account_name` は、上で選択した `STORAGE_ACCOUNT` 変数の値です。

* `storage_container_name` は、上で定義した `STORAGE_ACCOUNT_CONTAINER` の値です (たとえば、`mycontainer`)。 Dapr により、この名前のコンテナーが作成されます (Azure Storage アカウントにまだ存在していない場合)。

次のコマンドを使用して、ストレージ アカウント キーを取得します。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
STORAGE_ACCOUNT_KEY=`az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query '[0].value' --out tsv`
```

```bash
echo $STORAGE_ACCOUNT_KEY
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$STORAGE_ACCOUNT_KEY=(az storage account keys list --resource-group $RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query '[0].value' --out tsv)
```

```powershell
echo $STORAGE_ACCOUNT_KEY
```

---

### <a name="configure-the-state-store-component"></a>状態ストア コンポーネントを構成する

上の手順でソースとして指定したプロパティを使用して、*components.yaml* という名前の構成ファイルを作成します。 このファイルにより、Dapr アプリが状態ストアにアクセスできるようになります。 次の例は、Azure Blob Storage アカウント用に構成された場合に、*components.yaml* ファイルがどのようになるかを示しています。

```yaml
# components.yaml for Azure Blob storage component
- name: statestore
  type: state.azure.blobstorage
  version: v1
  metadata:
  # Note that in a production scenario, account keys and secrets 
  # should be securely stored. For more information, see
  # https://docs.dapr.io/operations/components/component-secrets
  - name: accountName
    value: <YOUR_STORAGE_ACCOUNT_NAME>
  - name: accountKey
    value: <YOUR_STORAGE_ACCOUNT_KEY>
  - name: containerName
    value: <YOUR_STORAGE_CONTAINER_NAME>
```

このファイルを使用するには、`<>` ブラケット内のプレースホルダー値を実際の値に置き換えてください。

> [!NOTE]
> 現在、Container Apps では、ネイティブの [Dapr コンポーネント スキーマ](https://docs.dapr.io/operations/components/component-schema/)はサポートされていません。 上の例では、サポートされているスキーマを使用しています。
>
> 運用レベルのアプリケーションでは、[シークレット管理](https://docs.dapr.io/operations/components/component-secrets)の手順に従って、シークレットを安全に管理します。


## <a name="deploy-the-service-application-http-web-server"></a>サービス アプリケーションをデプロイする (HTTP Web サーバー)

*components.yaml* ファイルを保存したディレクトリに移動し、下のコマンドを実行してサービス コンテナー アプリをデプロイします。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name nodeapp \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image dapriosamples/hello-k8s-node:latest \
  --target-port 3000 \
  --ingress 'external' \
  --min-replicas 1 \
  --max-replicas 1 \
  --enable-dapr \
  --dapr-app-port 3000 \
  --dapr-app-id nodeapp \
  --dapr-components ./components.yaml
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name nodeapp `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image dapriosamples/hello-k8s-node:latest `
  --target-port 3000 `
  --ingress 'external' `
  --min-replicas 1 `
  --max-replicas 1 `
  --enable-dapr `
  --dapr-app-port 3000 `
  --dapr-app-id nodeapp `
  --dapr-components ./components.yaml
```

---

このコマンドは、サービス (Node) アプリ サーバーを、サービスの検出と呼び出しのために `--dapr-app-id nodeapp` と `--dapr-app-port 3000` を使用して構成された、付随する Dapr サイドカーと共に `--target-port 3000` (アプリのポート) にデプロイします。 状態ストアは、`--dapr-components ./components.yaml` を使用して構成されます。これにより、サイドカーで状態を永続化することができます。


## <a name="deploy-the-client-application-headless-client"></a>クライアント アプリケーション (ヘッドレス クライアント) をデプロイする

下のコマンドを実行して、クライアント コンテナー アプリをデプロイします。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name pythonapp \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image dapriosamples/hello-k8s-python:latest \
  --min-replicas 1 \
  --max-replicas 1 \
  --enable-dapr \
  --dapr-app-id pythonapp
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name pythonapp `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image dapriosamples/hello-k8s-python:latest `
  --min-replicas 1 `
  --max-replicas 1 `
  --enable-dapr `
  --dapr-app-id pythonapp
```

---

このコマンドは、`pythonapp` をデプロイします。これも、`nodeapp` のための Dapr サイドカーを検索して安全に呼び出すために使用される Dapr サイドカーと共に実行されます。 このアプリはヘッドレスであるため、サーバーを起動するための `--target-port` がありません。また、イングレスを有効にする必要もありません。

## <a name="verify-the-result"></a>結果を確認する

### <a name="confirm-successful-state-persistence"></a>状態が正常に永続化されたことを確認する

Azure Storage アカウントのデータを表示することで、サービスが正常に動作していることを確認できます。

1. ブラウザーで [Azure portal](https://portal.azure.com) を開き、お使いのストレージ アカウントに移動します。

1. 左側の **[コンテナー]** を選びます。

1. **[mycontainer]** を選びます。

1. コンテナーに `order` という名前のファイルが表示されていることを確認します。

1. ファイルをクリックします。

1. **[編集]** タブをクリックします。

1. **[更新]** ボタンをクリックして、データがどのように自動的に更新されるかを確認します。

### <a name="view-logs"></a>ログの表示

コンテナー アプリを使用してログされたデータは、Log Analytics ワークスペースの `ContainerAppConsoleLogs_CL` カスタム テーブルに格納されます。 Azure portal または CLI を使用してログを表示できます。 ログに記録されたデータに対してクエリを実行する前に、初めて分析が到着するまでに数分かかることがあります。

次の CLI コマンドを使用して、コマンド ラインでログを表示します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'nodeapp' and (Log_s contains 'persisted' or Log_s contains 'order') | project ContainerAppName_s, Log_s, TimeGenerated | take 5" `
  --out table
```

---

次の出力は、CLI コマンドから返される応答の種類を示しています。

```console
ContainerAppName_s    Log_s                            TableName      TimeGenerated
--------------------  -------------------------------  -------------  ------------------------
nodeapp               Got a new order! Order ID: 61    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T21:31:46.184Z
nodeapp               Got a new order! Order ID: 62    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Successfully persisted state.    PrimaryResult  2021-10-22T22:01:57.174Z
nodeapp               Got a new order! Order ID: 63    PrimaryResult  2021-10-22T22:45:44.618Z
```

> [!TIP]
> 問題がある場合は、 GitHub の [Azure Container Apps リポジトリ](https://github.com/microsoft/azure-container-apps)で問題を開いて、お知らせください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

完了したら、次のコマンドを実行してリソース グループを削除し、コンテナー アプリのリソースをクリーンアップします。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
    --resource-group $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
    --resource-group $RESOURCE_GROUP
```

---

このコマンドは、コンテナー アプリ、ストレージ アカウント、コンテナー アプリ環境、およびリソース グループ内の他のすべてのリソースを削除します。

> [!NOTE]
> `pythonapp` は、構成された状態ストアに永続化されるメッセージを使用して `nodeapp` を継続的に呼び出します。そのため、継続的な課金対象の操作を回避するために、これらのクリーンアップ手順を完了することが重要です。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーション ライフサイクル管理](application-lifecycle-management.md)
