---
title: KEDA を使用した Kubernetes での Azure Functions
description: クラウドまたはオンプレミスで KEDA (Kubernetes ベースのイベント ドリブン自動スケーリング) を使用して Kubernetes で Azure Functions を実行する方法について説明します。
author: eamonoreilly
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: eamono
ms.openlocfilehash: 736945109cd18bd7c6f3a6b9a16b6549f7c65652
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741394"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>KEDA を使用した Kubernetes での Azure Functions

Azure Functions ランタイムにより、必要な場所と方法でのホスティングにおける柔軟性が提供されます。  [KEDA](https://keda.sh) (Kubernetes ベースのイベント ドリブン自動スケーリング) は、Azure Functions ランタイムおよびツールにシームレスに組み合わされ、Kubernetes でのイベント ドリブンな自動スケーリングを提供します。

## <a name="how-kubernetes-based-functions-work"></a>Kubernetes ベースの関数の動作

Azure Functions サービスは 2 つの主要コンポーネントで構成されています。ランタイムとスケール コントローラーです。  Functions ランタイムでは、ご自分のコードを実行します。  ランタイムには、関数の実行をトリガー、ログ、および管理する方法のロジックが含まれています。  Azure Functions ランタイムは、*どこでも* 実行できます。  もう 1 つのコンポーネントは、スケール コントローラーです。  スケール コントローラーによって、関数をターゲットにしているイベントの割合が監視され、アプリを実行しているインスタンスの数がプロアクティブにスケーリングされます。  詳細については、「[Azure Functions のスケールとホスティング](functions-scale.md)」を参照してください。

Kubernetes ベースの Functions では、KEDA によるイベント ドリブン スケーリングを使用して、[Docker コンテナー](functions-create-function-linux-custom-image.md)内に Functions ランタイムが提供されます。  KEDA では、0 インスタンスまでのスケールイン (インスタンスが発生していないとき) と *n* インスタンスまでのスケールアウトが可能です。 これは、Kubernetes 自動スケーラー (ポッドの水平自動スケーラー) 用のカスタム メトリックを公開することによって行われます。  KEDA で Functions のコンテナーを使用すると、任意の Kubernetes クラスターにおいてサーバーレス関数の機能をレプリケートできるようになります。  これらの関数は、サーバーレス インフラストラクチャ用の [Azure Kubernetes Services (AKS) 仮想ノード](../aks/virtual-nodes-cli.md)機能を使用してデプロイすることもできます。

## <a name="managing-keda-and-functions-in-kubernetes"></a>Kubernetes での KEDA と関数の管理

Kubernetes クラスター上で Functions を実行するには、KEDA コンポーネントをインストールする必要があります。 このコンポーネントは、次のいずれかの方法でインストールできます。

+ Azure Functions Core Tools: [`func kubernetes install` コマンド](functions-core-tools-reference.md#func-kubernetes-install)を使用します。

+ Helm: Kubernetes クラスターに KEDA をインストールするには、Helm を使用する方法を含め、さまざまな方法があります。  デプロイ オプションについては、[KEDA サイト](https://keda.sh/docs/deploy/)をご覧ください。

## <a name="deploying-a-function-app-to-kubernetes"></a>Kubernetes への関数アプリのデプロイ

KEDA を実行する Kubernetes クラスターには、あらゆる関数アプリをデプロイできます。  対象の関数は Docker コンテナー内で実行されるため、プロジェクトには Dockerfile が必要です。  Dockerfile は、`func init` を呼び出してプロジェクトを作成するときに [`--docker` オプション][func init]を使用することで作成できます。 これを忘れた場合は、Functions プロジェクトのルートからいつでも `func init` を再度呼び出すことができます。ただし、その際は、次の例に示すように [`--docker-only` オプション][func init]を使用します。 

```command
func init --docker-only
```

Dockerfile の生成の詳細については、[`func init`][func init] のリファレンスを参照してください。 

イメージをビルドして関数を Kubernetes にデプロイするには、次のコマンドを実行します。

```command
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

この例では、`<name-of-function-deployment>` をお使いの関数アプリの名前に置き換えます。

deploy コマンドによって、次の処理が実行されます。

1. 先ほど作成した Dockerfile は、関数アプリのローカル イメージを構築するために使用されます。
1. ローカル イメージにタグが付けられ、ユーザーがログインしたコンテナー レジストリにプッシュされます。
1. マニフェストが作成され、Kubernetes `Deployment` リソース、`ScaledObject` リソース、`local.settings.json` からインポートされた環境変数を含む `Secrets` を定義するクラスターに適用されます。

詳細については、[`func kubernetes deploy` コマンド](functions-core-tools-reference.md#func-kubernetes-deploy)に関するセクションを参照してください。

### <a name="deploying-a-function-app-from-a-private-registry"></a>プライベート レジストリから関数アプリをデプロイする

前述のフローは、プライベート レジストリにも使用できます。  コンテナー イメージをプライベート レジストリからプルする場合は、`func kubernetes deploy` を実行する際に `--pull-secret` フラグを指定して、プライベート レジストリの資格情報を保持する Kubernetes シークレットを参照します。

## <a name="removing-a-function-app-from-kubernetes"></a>Kubernetes からの関数アプリの削除

デプロイ後は、作成された関連する `Deployment`、`ScaledObject`、`Secrets` を削除することによって、関数を削除できます。

```command
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Kubernetes からの KEDA のアンインストール

次のいずれかの方法で、対象のクラスターから KEDA を削除できます。

+ Azure Functions Core Tools: [`func kubernetes remove` コマンド](functions-core-tools-reference.md#func-kubernetes-remove)を使用します。

+ Helm: [KEDA のサイト](https://keda.sh/docs/deploy/)上のアンインストール手順を参照してください。

## <a name="supported-triggers-in-keda"></a>KEDA でサポートされているトリガー

KEDA は、次の Azure Function トリガーをサポートしています。

* [Azure Storage キュー](functions-bindings-storage-queue.md)
* [Azure Service Bus キュー](functions-bindings-service-bus.md)
* [Azure Event/IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ キュー](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP トリガーのサポート

HTTP トリガーを公開する Azure Functions は使用することはできますが、KEDA では直接管理されません。  KEDA prometheus トリガーを利用すると、[HTTP Azure Functions を 1 から *n* インスタンスにスケーリングできます](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)。

## <a name="next-steps"></a>次の手順
詳細については、次のリソースを参照してください。

* [カスタム イメージを使用して関数を作成する](functions-create-function-linux-custom-image.md)
* [Azure Functions をローカルでコーディングしてテストする](functions-develop-local.md)
* [Azure Functions の従量課金プランのしくみ](functions-scale.md)

[func init]: functions-core-tools-reference.md#func-init