---
title: Azure Container Registry webhook
description: レジストリ リポジトリで特定のアクションが発生したときに、webhook を使用してイベントをトリガーする方法について説明します。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: e85b69e452f4d76dfdf974698fa7d3b5cdbc0c30
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426090"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry webhook の使用

Docker Hub で公開 Docker イメージを格納するように、Azure コンテナー レジストリではプライベート Docker コンテナー イメージを格納および管理します。 レジストリ リポジトリで特定のアクションが発生した場合に、webhook を使用してイベントをトリガーできます。 webhook では、レジストリ レベルでイベントに応答したり、特定のリポジトリ タグに範囲を絞ったりできます。

webhook 要求の詳細については、「[Azure Container Registry webhook スキーマ リファレンス](container-registry-webhook-reference.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Container Registry - コンテナー レジストリを Azure サブスクリプションで作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用します。
* Docker CLI - ローカル コンピューターを Docker ホストとして設定し、Docker CLI コマンドにアクセスするには、[Docker エンジン](https://docs.docker.com/engine/installation/)をインストールします。

## <a name="create-webhook-azure-portal"></a>Azure Portal での Webhook の作成

1. [Azure ポータル](https://portal.azure.com)
1. webhook を作成するコンテナー レジストリに移動します。
1. **[サービス]** で **[webhook]** を選択します。
1. webhook ツールバーで **[追加]** を選択します。
1. 次の情報を利用して、*[webhook の作成]* フォームを完成させます。

| 値 | 説明 |
|---|---|
| Name | Webhook に付与する名前。 使用できる文字は英小文字と数字のみです。文字数は 5 ～ 50 文字にする必要があります。 |
| サービス URI | Webhook が POST 通知を送信する URI。 |
| カスタム ヘッダー | POST 要求と共に渡すヘッダー。 "キー: 値" の形式にする必要があります。 |
| トリガー アクション | Webhook をトリガーするアクション。 現在、webhook はイメージのプッシュまたは削除アクションによってトリガーできます。 |
| Status | Webhook の作成後の状態。 既定で有効です。 |
| Scope | Webhook が動作するスコープです。 既定では、スコープはレジストリ内のすべてのイベントです。 "リポジトリ:タグ" の形式を使用して、スコープを 1 つのリポジトリまたはタグに指定することができます。 |

Webhook フォームの例 :

![Azure Portal の ACR webhook の作成の UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Azure CLI での Webhook の作成

Azure CLI を使用して Webhook を作成するには、[az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create) コマンドを使用します。

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook をテストする

### <a name="azure-portal"></a>Azure ポータル

コンテナー イメージのプッシュおよび削除アクションで webhook を使用する前に、**[Ping]** ボタンを使って webhook をテストできます。 Ping を実行すると、指定されたエンドポイントに一般的な POST 要求が送信され、応答がログに記録されます。 ping 機能を使用して、webhook が正しく構成されていることを確認できます。

1. テストする Webhook を選択します。
2. 上部のツールバーで、**[Ping]** を選択します。
3. **[HTTP の状態]** 列でエンドポイントの応答を確認します。

![Azure Portal の ACR webhook の作成の UI](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して ACR Webhook をテストするには、[az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) コマンドを使用します。

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

結果を表示するには、[az acr webhook list-events](/cli/azure/acr/webhook#list-events) コマンドを使用します。

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook の削除

### <a name="azure-portal"></a>Azure ポータル

Azure Portal で webhook を選択してから **[削除]** ボタンをクリックすると、各 webhook を削除できます。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>次の手順

[Azure Container Registry webhook スキーマ リファレンス](container-registry-webhook-reference.md)