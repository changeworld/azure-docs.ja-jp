---
title: 'クイック スタート: 最初のコンテナー アプリをデプロイする'
description: 最初のアプリケーションを Azure Container Apps プレビューにデプロイします。
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: bb2b24c080f7f8173280d5c31ca711d7241d04f1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552719"
---
# <a name="quickstart-deploy-your-first-container-app"></a>クイック スタート: 最初のコンテナー アプリをデプロイする

Azure Container Apps プレビューを使用すると、サーバーレス プラットフォームでマイクロサービスとコンテナー化されたアプリケーションを実行できます。 Container Apps を使用すると、コンテナーを実行するメリットが得られますが、クラウド インフラストラクチャと複雑なコンテナー オーケストレーターを手動で管理するという問題が残ります。

このクイックスタートでは、セキュリティで保護された Container Apps 環境を作成し、最初のコンテナー アプリをデプロイします。

## <a name="prerequisites"></a>前提条件

- [Azure CLI](/cli/azure/install-azure-cli) をインストールします。

## <a name="setup"></a>セットアップ

まず、CLI から Azure にサインインします。 次のコマンドを実行し、プロンプトに従って認証プロセスを完了します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az login
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az login
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

```azurecli
az provider register --namespace Microsoft.Web
```

---

次に、次の環境変数を設定します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
RESOURCE_GROUP="my-container-apps"
LOCATION="canadacentral"
LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
CONTAINERAPPS_ENVIRONMENT="my-environment"
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-container-apps"
$LOCATION="canadacentral"
$LOG_ANALYTICS_WORKSPACE="my-container-apps-logs"
$CONTAINERAPPS_ENVIRONMENT="my-environment"
```

---

これらの変数を定義すると、新しいコンテナー アプリに関連するサービスを整理するためのリソース グループを作成できます。

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

Azure Container Apps 環境では、コンテナー アプリのグループを囲むセキュリティ保護された境界が作成されます。 同じ環境にデプロイされた Container Apps は、同じ仮想ネットワークにデプロイされ、同じ Log Analytics ワークスペースにログを書き込みます。

Azure Log Analytics は、Container Apps 環境を作成するときに必要なコンテナー アプリを監視するために使われます。

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

## <a name="create-a-container-app"></a>コンテナー アプリの作成

環境が作成されたので、最初のコンテナー アプリをデプロイできます。 `containerapp create` コマンドを使用して、コンテナー イメージを Azure Container Apps にデプロイします。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp create \
  --name my-container-app \
  --resource-group $RESOURCE_GROUP \
  --environment $CONTAINERAPPS_ENVIRONMENT \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
  --target-port 80 \
  --ingress 'external' \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp create `
  --name my-container-app `
  --resource-group $RESOURCE_GROUP `
  --environment $CONTAINERAPPS_ENVIRONMENT `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest `
  --target-port 80 `
  --ingress 'external' `
  --query configuration.ingress.fqdn
```

---

`--ingress` を `external` に設定すると、コンテナー アプリがパブリック要求で使用できるようになります。

ここでは、`create` コマンドによって、コンテナー アプリの完全修飾ドメイン名が返されます。 この場所を Web ブラウザーにコピーすると、次のメッセージが表示されます。

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="Your first Azure Container Apps deployment. \(Azure Container Apps:の最初のデプロイ\)":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、リソース グループを削除することで、Azure Container Apps インスタンスと関連付けられているすべてのサービスを削除できます。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group delete \
  --name $RESOURCE_GROUP
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az group delete `
  --name $RESOURCE_GROUP
```

---

> [!TIP]
> 問題がある場合は、 GitHub の [Azure Container Apps リポジトリ](https://github.com/microsoft/azure-container-apps)でイシューを開いて、お知らせください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Container Apps の環境](environment.md)
