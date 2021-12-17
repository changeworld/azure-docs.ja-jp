---
title: Azure Kubernetes Service (AKS) 用の Dapr クラスター拡張機能 (プレビュー) を使用してアプリケーションをデプロイする
description: Azure Kubernetes Service (AKS) 用の Dapr クラスター拡張機能 (プレビュー) を使用してアプリケーションをデプロイする
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: quickstart
ms.date: 11/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: 1590bcc48260e4b6729cd12cb80b047ba9fe4acb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477949"
---
# <a name="quickstart-deploy-an-application-using-the-dapr-cluster-extension-preview-for-azure-kubernetes-service-aks"></a>クイックスタート: Azure Kubernetes Service (AKS) 用の Dapr クラスター拡張機能 (プレビュー) を使用してアプリケーションをデプロイする

このクイックスタートでは、AKS クラスターで [Dapr クラスター拡張機能][dapr-overview]を使用する方法について詳しく学習します。 hello world の例をデプロイします。これは、メッセージを生成する Python アプリケーションと、それを使用して永続化する Node アプリケーションで構成されます。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること](/cli/azure/install-azure-cli)。
* [Dapr クラスター拡張機能][dapr-overview]が有効になっている AKS クラスター

## <a name="clone-the-repository"></a>リポジトリの複製

サンプル アプリケーションをデプロイするために使用するファイルを取得するには、[Quickstarts リポジトリ][hello-world-gh]をクローンし、`hello-kubernetes` ディレクトリに変更します。

```bash
git clone https://github.com/dapr/quickstarts.git
cd quickstarts/hello-kubernetes
```

## <a name="create-and-configure-a-state-store"></a>状態ストアの作成と構成

Dapr は、さまざまな状態ストア (Redis、CosmosDB、DynamoDB、Cassandra など) を使用して、状態を永続化および取得できます。 この例では、Redis を使用します。

### <a name="create-a-redis-store"></a>Redis ストアを作成する

1. [Azure portal][azure-portal-cache] を開いて、Azure Redis Cache の作成フローを開始します。
2. 必要な情報を入力します。
3. [作成] をクリックして、Redis インスタンスのデプロイを開始します。
4. Redis インスタンスのホスト名をメモしておきます。これは、Azure の [概要] から取得できます。 `xxxxxx.redis.cache.windows.net:6380` のようになります。
5. インスタンスが作成されたら、アクセス キーを取得する必要があります。 [設定] の下にある [アクセス キー] に移動し、Redis パスワードを格納するための Kubernetes シークレットを作成します。

```bash
kubectl create secret generic redis --from-literal=redis-password=<your-redis-password>
```

### <a name="configure-the-dapr-components"></a>Dapr コンポーネントを構成する

ストアを作成したら、Hello World リポジトリの deploy ディレクトリにある redis.yaml ファイルにキーを追加する必要があります。 `redisHost` 値を独自の Redis マスター アドレスに、`redisPassword` を独自のシークレットに置き換えます。 詳細については、[ここ][dapr-component-secrets]を参照してください。

また、`redisPassword` の下の次の 2 行を追加して、TLS 経由の接続を有効にする必要があります。

```yml
- name: redisPassword
    secretKeyRef:
      name: redis
      key: redis-password
- name: enableTLS
  value: true
```

### <a name="apply-the-configuration"></a>構成を適用する

`redis.yaml` ファイルを適用します。

```bash
kubectl apply -f ./deploy/redis.yaml
``` 

次のように、状態ストアが出力で正常に構成されたことを確認します。

```bash
component.dapr.io/statestore created
```

## <a name="deploy-the-nodejs-app-with-the-dapr-sidecar"></a>Dapr サイドカーを使用して Node.js アプリをデプロイする

クラスターに Node.js アプリのデプロイを適用します。

```bash
kubectl apply -f ./deploy/node.yaml
```

> [!NOTE]
> Kubernetes デプロイは非同期です。 これは、デプロイが完了するまで待ってから次の手順に移動する必要があることを意味します。 これを行うには、次のコマンドを使います。
> ```bash
> kubectl rollout status deploy/nodeapp
> ```

これにより、Node.js アプリが Kubernetes にデプロイされます。 Dapr コントロール プレーンによって、Dapr サイドカーがポッドに自動的に挿入されます。 `node.yaml` ファイルを確認すると、そのデプロイに対して Dapr がどのように有効になっているかがわかります。

* `dapr.io/enabled: true` - これは、Dapr コントロール プレーンに対して、このデプロイにサイドカーを挿入するように指示しています。

* `dapr.io/app-id: nodeapp` - これにより、Dapr アプリケーションに一意の ID または名前が割り当てられます。そのため、他の Dapr アプリとの間でメッセージを送受信できるようになります。

サービスにアクセスするには、`kubectl` から `EXTERNAL-IP` を取得してメモします。

```bash
kubectl get svc nodeapp
```

### <a name="verify-the-service"></a>サービスを確認する

サービスを呼び出すには、次のように実行します。

```bash
curl $EXTERNAL_IP/ports
```

次のような出力が表示されます。

```bash
{"DAPR_HTTP_PORT":"3500","DAPR_GRPC_PORT":"50001"}
```

次に、アプリケーションに命令を送信します。

```bash
curl --request POST --data "@sample.json" --header Content-Type:application/json $EXTERNAL_IP/neworder
```

要求することによって、命令が保持されていることを確認します。

```bash
curl $EXTERNAL_IP/order
```

次のような出力が表示されます。

```bash
{ "orderId": "42" }
```

> [!TIP]
> Dapr ダッシュボードを使ってみるとよいでしょう。Dapr で実行されているアプリケーションの状態、情報、ログを確認するための便利なインターフェイスです。 次のコマンドを実行すると、`http://localhost:8080/` で使用できるようになります。
> ```bash
> kubectl port-forward svc/dapr-dashboard -n dapr-system 8080:8080
> ```

## <a name="deploy-the-python-app-with-the-dapr-sidecar"></a>Dapr サイドカーを使用して Python アプリをデプロイする

Python アプリを簡単に見てみましょう。 `hello-kubernetes` クイックスタートで Python アプリのディレクトリに移動し、`app.py` を開きます。

これは JSON メッセージを `localhost:3500` にポストする基本的な Python アプリで、Dapr の既定のリスニング ポートです。 `v1.0/invoke/nodeapp/method/neworder` にポストすることによって、Node.js アプリケーションの `neworder` エンドポイントを呼び出すことができます。 メッセージには、1 秒間に 1 回ずつ増分される `orderId` を持つデータが含まれています。

```python
n = 0
while True:
    n += 1
    message = {"data": {"orderId": n}}

    try:
        response = requests.post(dapr_url, json=message)
    except Exception as e:
        print(e)

    time.sleep(1)
```

Python アプリを Kubernetes クラスターにデプロイします。

```bash
kubectl apply -f ./deploy/python.yaml
```

> [!NOTE]
> 上記と同様に、次のコマンドはデプロイが完了するまで待機します。
> ```bash
> kubectl rollout status deploy/pythonapp
> ```

## <a name="observe-messages-and-confirm-persistence"></a>メッセージを監視し、永続化を確認する

Node.js と Python の両方のアプリケーションがデプロイされたので、メッセージを見ることができます。

Node.js アプリのログを取得します。

```bash
kubectl logs --selector=app=node -c node --tail=-1
```

デプロイが成功した場合は、次のようなログが表示されます。

```bash
Got a new order! Order ID: 1
Successfully persisted state
Got a new order! Order ID: 2
Successfully persisted state
Got a new order! Order ID: 3
Successfully persisted state
```

Node.js アプリの命令エンドポイントを呼び出して、最新の命令を取得します。 前に保存した外部 IP アドレスを取得し、"/order" を追加して、それに対して GET 要求を実行します (ブラウザーに入力するか、Postman を使用するか、`curl` を実行します)。

```bash
curl $EXTERNAL_IP/order
{"orderID":"42"}
```

応答に最新の JSON が表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[az group delete][az-group-delete] コマンドを使用して、リソース グループ、AKS クラスター、名前空間、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>次の手順

このサンプル アプリケーションを正常にデプロイした後、次のようになります。
> [!div class="nextstepaction"]
> [その他のクラスター拡張機能についての詳細情報][cluster-extensions]

<!-- LINKS -->
<!-- INTERNAL -->
[cluster-extensions]: ./cluster-extensions.md
[dapr-overview]: ./dapr.md
[az-group-delete]: /cli/azure/group#az_group_delete

<!-- EXTERNAL -->
[hello-world-gh]: https://github.com/dapr/quickstarts/tree/v1.4.0/hello-kubernetes
[azure-portal-cache]: https://ms.portal.azure.com/#create/Microsoft.Cache
[dapr-component-secrets]: https://docs.dapr.io/operations/components/component-secrets/