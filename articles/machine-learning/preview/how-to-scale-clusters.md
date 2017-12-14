---
title: "Machine Learning 向けに Azure Container Service クラスターをスケーリングする方法 | Microsoft Docs"
description: "ACS クラスターのスケーリング - 自動スケールと静的スケール。クラスター内のノード数のスケーリング"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 8d709936bfba5c89091d7f26449d165bddb930de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Web サービスのスループットを管理するためのクラスターのスケーリング

## <a name="why-scale-the-cluster"></a>クラスターをスケーリングする理由

Azure Container Service (ACS) クラスターのスケーリングは、クラスターのサイズを最小限に抑えてコストを削減しながらサービスのスループットを最適化するために効果的な方法です。 

自動スケールの理解を深めるために、3 つの Web サービスを実行しているクラスターの次の例を考えてみます。

![例: クラスター上の 3 つのサービス](media/how-to-scale-clusters/three-services.png)

これらのサービスにはさまざまなピーク需要があります。サービス 1 (青色の線) はピーク需要時に 40 ポッド、サービス 2 (オレンジ色の線) はピーク時に 38 ポッド、サービス 3 (灰色の線) ではピーク時に 50 ポッドを必要とします。 サービスごとに必要なピーク時容量を個別に予約する場合、このクラスターには少なくとも合計で 40 + 38 + 50 = 128 個のポッドが必要です。

では、グラフ内の黒い破線で表される、任意の時点での実際のポッドの使用状況を考えてみましょう。 この場合、"*どの時点でも使用されるポッドの最大数*" は、サービス 3 がピークに達した 20:00 の時点の 64 です。 このとき、サービス 3 では 50 ポッドが使用されていますが、サービス 2 では 9 ポッド、サービス 1 ではわずか 5 ポッドしか使用されていません。 これがこのクラスターの "*ピーク時の使用量*" になることを覚えておいてください。 つまり、このクラスターでは 64 を超えるポッドが使用されることはありません。これは、3 つのサービスの個別のピーク時の使用量から求めた要件である 128 ポッドの半分です。

単純にすべてのサービスのピーク需要に十分なリソースを必要とするのではなく、各サービスの現在の需要を満たすためにクラスター内のポッドを再割り当て (再スケーリング) することにより、クラスターのサイズを小さくすることができます。 この簡単な例では、自動スケールにより、必要なポッド数が 128 から 64 に減少し、必要なクラスター サイズが半分に縮小されます。

ポッド数のスケーリングは比較的高速な操作で 1 分もかからないため、サービスの応答性に深刻な影響はありません。

> [!NOTE]
> クラスターのスケーリングは、要求の待ち時間の問題には役立ちません。 運用化の目的では、スケールアップによって成功回数が増加し、"サービス使用不能" エラーが減少します。 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>ACS クラスターの Web サービスをスケーリングする方法

モデル管理 CLI のクラスター設定オプションにより、既定では、2 つのエージェントと 1 つのポッドが環境内で構成されます。 また、Kubernetes CLI もインストールされます。

ACS にデプロイした Web サービスは、以下を調整することでスケーリングできます。

* クラスター内のエージェント ノードの数
* エージェント ノードで実行されている Kubernetes ポッド レプリカの数

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>クラスター内のノード数のスケーリング

次のコマンドを実行すると、クラスター内のエージェント ノードの数が設定されます。

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

これが完了するまでに数分かかります。 クラスター内のノード数のスケーリングの詳細については、「[Container Service クラスター内のエージェント ノードのスケーリング](https://docs.microsoft.com/azure/container-service/container-service-scale)」を参照してください。

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>クラスター内の Kubernetes ポッド レプリカ数のスケーリング
 
Azure Machine Learning CLI または [Kubernetes ダッシュボード](https: //kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)を使用して、クラスターに割り当てられたポッド レプリカの数をスケーリングできます。

Kubernetes レプリカ ポッドの詳細については、[Kubernetes ポッド](https://kubernetes.io/docs/concepts/workloads/pods/pod/)のドキュメントを参照してください。

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Azure Machine Learning CLI を使用したクラスターのスケーリング

CLI を使用したクラスターのスケーリングには 2 つの方法があります。

- 自動スケール
- 静的スケール

自動スケールは、サービスの作成時に既定でアクティブになり、ほとんどの状況で推奨されるスケーリング方法です。

##### <a name="autoscale"></a>自動スケール

次のコマンドでは、自動スケールを有効にし、サービスのレプリカの最小数と最大数を設定します。

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

たとえば、`autoscale-min-replicas` を 5 に設定すると、5 つのレプリカが作成されます。 Web サービスに最適な数を見つけるには、この数に 10 などの値を設定し、503 エラー メッセージの数を監視します。 その後、この数を適宜調整します。


| パラメーター名 | 型 | 説明 |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | boolean | 自動スケールを有効にするかどうかを指定します。 既定値: true |
| `autoscale-min-replicas` | integer | ポッドの最小数を指定します。 0 以上にする必要があります。 既定値: 1 |
| `autoscale-max-replicas` | integer | ポッドの最大数を指定します。 1 以上にする必要があります。 autoscale-max-replicas が autoscale-min-replicas より小さい場合は、autoscale-max-replicas が無視されます。 既定値: 10 |
| `autoscale-refresh-period-seconds` | integer | 自動スケールの更新間隔を秒単位で指定します。 既定値: 1 |
| `autoscale-target-utilization` | integer | 自動スケールで目標とする使用率を 1 ～ 100 の値で指定します。 既定値: 70 |

自動スケールは、次の 2 つの条件を満たすように動作します。

1. 目標使用率が達成される
2. スケーリングしても最小と最大の設定を超えない

クラスター内では、クラスター リソースを求めてサービス間の競合が発生します。 自動スケーリングされたサービスでは、1 秒あたりの要求数 (RPS) が増加するとクラスター リソースの使用量が増加し、RPS が減少すると徐々にリソースが解放されます。 クラスター リソースは、サービスが取得するリソースが存在する限り、必要に応じて取得されます。

自動スケール パラメーターの使用の詳細については、「[モデル管理のコマンドライン インターフェイスのリファレンス](model-management-cli-reference.md)」を参照してください。

##### <a name="static-scale"></a>静的スケール

一般に、静的スケールは避ける必要があります。これは、自動スケールによるクラスター サイズの縮小が許可されないためです。 そうであっても、状況によっては静的スケールが推奨される場合があります。 たとえば、クラスターが 1 つのサービス専用である場合は自動スケールを使用するメリットがありません。すべてのクラスター リソースがそのサービスに割り当てられるためです。

クラスターを静的にスケーリングするには、自動スケールを無効にする必要があります。 自動スケールを無効にするには、次のコマンドを使用します。

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

自動スケールを無効にした後、次のコマンドを使用すると、サービスのレプリカの数を直接スケーリングできます。

```
az ml service update realtime -i <service id> -z <replica count>
```
 
クラスター内のノード数のスケーリングの詳細については、「Container Service クラスター内のエージェント ノードのスケーリング」を参照してください。

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Kubernetes ダッシュボードを使用したレプリカ数のスケーリング

コマンド ラインで次のように入力します。

```
kubectl proxy
```

Windows の場合、Kubernetes のインストール場所は自動的にパスに追加されません。 最初に、インストール フォルダーに移動します。

```
c:\users\<user name>\bin
```

コマンドを実行すると、次の情報メッセージが表示されます。

```
Starting to serve on 127.0.0.1:8001
```

ポートが既に使用中の場合は、次の例のようなメッセージが表示されます。

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

代替ポート番号は、*--port* パラメーターを使用して指定できます。

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

ダッシュボード サーバーを起動したら、ブラウザーを開いて次の URL を入力します。

```
127.0.0.1:<port number>/ui
```

ダッシュボードのメイン画面で、左側のナビゲーション バーの **[Deployments]\(デプロイ\)** をクリックします。 ナビゲーション ウィンドウが表示されない場合は、左上のこのアイコン ![3 つの短い水平線からなるメニュー](media/how-to-scale-clusters/icon-hamburger.png) を選択してください。

変更するデプロイを見つけて、右側のこのアイコン ![縦に並んだ 3 つのドットからなるメニュー アイコン](media/how-to-scale-clusters/icon-kebab.png) を右クリックし、**[View/edi YAML]\(YAML の表示/編集\)** をクリックします。

デプロイの編集画面で、*[spec]\(仕様\)* ノードを見つけ、*[replicas]\(レプリカ数\)* の値を変更して **[Update]\(更新\)** をクリックします。
