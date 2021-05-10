---
title: Azure Kubernetes Services (AKS) における Kubernetes の基本概念
description: Kubernetes の基本のクラスターおよびワークロードについてと、クラスターおよびワークロードが Azure Kubernetes Service (AKS) の機能にどのように関連しているかを説明します。
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105937"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) における Kubernetes の中心概念

アプリケーション開発はコンテナーベースの手法へと移行し続けており、リソースを調整して管理する必要性が増しています。 Kubernetes は、主要プラットフォームとして、フォールト トレラントなアプリケーション ワークロードの信頼性の高いスケジュール機能を提供しています。 マネージド Kubernetes オファリングである Azure Kubernetes Service (AKS) を使用すると、コンテナーベースのアプリケーションのデプロイと管理がさらに簡素化されます。

この記事では、次の内容について説明します。
* Kubernetes インフラストラクチャのコア コンポーネント:
    * *コントロール プレーン*
    * *ノード*
    * *ノード プール*
* ワークロード リソース: 
    * *ポッド*
    * *deployments*
    * *セット* 
* リソースを "*名前空間*" にグループ化する方法。

## <a name="what-is-kubernetes"></a>Kubernetes とは

Kubernetes は、コンテナー ベース アプリケーションとそれに関連するネットワークおよびストレージ コンポーネントを管理するプラットフォームであり、急速に進化しています。 Kubernetes では、基になるインフラストラクチャ コンポーネントではなく、アプリケーション ワークロードに重点が置かれています。 Kubernetes は、管理操作のための一連の堅牢な API によって支援され、デプロイに対して宣言型のアプローチを提供しています。

アプリケーション コンポーネントの可用性を調整して管理する Kubernetes の機能を使用して、モダンかつポータブルなマイクロサービス ベースのアプリケーションを構築して実行できます。 チームがマイクロサービス ベース アプリケーションを採用して作業を進める場合、Kubernetes は、ステートフルおよびステートレスの両方のアプリケーションをサポートします。

Kubernetes はオープン プラットフォームとして、使いたいプログラミング言語、OS、ライブラリ、またはメッセージング バスでアプリケーションを開発することを可能にします。 既存の継続的インテグレーションと継続的デリバリー (CI/CD) ツールは、リリースをスケジュール設定してデプロイするために、Kubernetes と連携できます。

AKS は、アップグレードの調整など、デプロイおよびコア管理タスクの複雑さを軽減する、マネージド Kubernetes サービスを提供します。 AKS コントロール プレーンは Azure プラットフォームによって管理され、お使いのアプリケーションを実行する AKS ノードに対してのみ課金されます。 AKS は、オープンソースの Azure Kubernetes Service Engine ([aks-engine][aks-engine]) の最上位に構築されます。

## <a name="kubernetes-cluster-architecture"></a>Kubernetes クラスターのアーキテクチャ

Kubernetes クラスターは、次の 2 つのコンポーネントに分割されています。

- "*コントロール プレーン*": 主要な Kubernetes サービスと、アプリケーション ワークロードのオーケストレーションを提供します。
- "*ノード*": アプリケーション ワークロードを実行します。

![Kubernetes のコントロール プレーンとノードのコンポーネント](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>コントロール プレーン

AKS クラスターを作成すると、コントロール プレーンが自動的に作成されて構成されます。 このコントロール プレーンは、ユーザーから抽象化されたマネージド Azure リソースとして無料で提供されます。 AKS クラスターに接続されているノードに対してのみ課金されます。 コントロール プレーンとそのリソースは、クラスターを作成したリージョンにのみ存在します。

コントロール プレーンには、次の主要な Kubernetes コンポーネントが含まれます。

| コンポーネント | 説明 |  
| ----------------- | ------------- |  
| *kube-apiserver*                                                                                 | API サーバーは、基になる Kubernetes API が公開される方法となっています。 このコンポーネントは、`kubectl` や Kubernetes ダッシュボードなど、管理ツールに対する操作を提供します。                                                        |  
| *etcd* | お使いの Kubernetes クラスターと構成の状態を維持するために、高可用性を備えた *etcd* が Kubernetes 内のキー値ストアとなります。                                      |  
| *kube-scheduler*                                                                            | スケジューラでは、アプリケーションを作成またはスケーリングするときに、どのノードがワークロードを実行して開始できるかを判断します。                                                                                    |  
| *kube-controller-manager*                                                                            | コントローラー マネージャーでは、ポッドのレプリケートやノード調整の処理などのアクションを実行する、より小規模な多数のコントローラーを監視します。                                                                  |  

AKS は、専用の API サーバーやスケジューラなどを備えたシングル テナント コントロール プレーンを提供します。ノードの数とサイズを定義すると、Azure プラットフォームによって、コントロール プレーンとノードの間のセキュリティで保護された通信が構成されます。 コントロール プレーンとの対話は、`kubectl` や Kubernetes ダッシュボードなどの Kubernetes API を介して行われます。

このマネージド コントロール プレーンによって、コンポーネント (高可用性を備えた *etcd* ストアなど) を構成する必要はなくなりますが、コントロール プレーンには直接アクセスできません。 Kubernetes のコントロール プレーンとノードのアップグレードは、Azure CLI または Azure portal を使用して調整されます。 発生する可能性のある問題のトラブルシューティングを行うために、Azure Monitor ログからコントロール プレーンのログを確認できます。

コントロール プレーンを構成したり、それに直接アクセスしたりする場合は、[aks-engine][aks-engine] を使用して独自の Kubernetes クラスターをデプロイします。

関連付けられているベスト プラクティスについては、[AKS でのクラスターのセキュリティとアップグレードに関するベスト プラクティス][operator-best-practices-cluster-security]のページを参照してください。

## <a name="nodes-and-node-pools"></a>ノードとノード プール

アプリケーションとサポート対象のサービスを実行するには、Kubernetes の *ノード* が必要です。 AKS クラスターには少なくとも 1 つのノードがあり、これは Kubernetes ノードのコンポーネントとコンテナー ランタイムを実行する Azure Vrtual Machine (VM) となります。

| コンポーネント | 説明 |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | コントロール プレーンからのオーケストレーション要求と、要求されたコンテナーの実行のスケジュール設定を処理する Kubernetes エージェントです。                                                        |  
| *kube-proxy* | 各ノード上で仮想ネットワークを処理します。 プロキシはネットワーク トラフィックをルーティングして、サービスとポッドの IP アドレスを管理します。                                      |  
| *コンテナー ランタイム*                                                                            | コンテナー化されたアプリケーションが仮想ネットワークやストレージなどの追加リソースを実行して操作できるようにします。 Kubernetes バージョン 1.19 以降のノード プールを使用する AKS クラスターでは、コンテナー ランタイムとして `containerd` が使用されます。 Kubernetes バージョン 1.19 よりも前のノード プールを使用する AKS クラスターでは、コンテナー ランタイムとして [Moby](https://mobyproject.org/) (アップストリーム Docker) が使用されます。                                                                                    |  


![Kubernetes ノードの Azure 仮想マシンとサポート対象リソース](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

ノードの Azure VM サイズでは、ストレージの CPU 数、メモリ、サイズ、利用可能な種類 (高性能の SSD や通常の HDD など) を定義します。 お使いのアプリケーションで大容量の CPU やメモリ、または高性能ストレージが必要かどうかに応じてノード サイズを計画してください。 お使いの AKS クラスター内のノード数は、需要に合わせてスケールアウトしてください。

AKS では、クラスターのノードに対する VM イメージは Ubuntu Linux または Windows Server 2019 に基づいています。 AKS クラスターを作成したり、ノード数をスケールアウトしたりすると、Azure プラットフォームによって、要求された数の VM が作成され構成されます。 エージェント ノードは標準の VM として課金されるので、VM サイズの割引 ([Azure の予約][reservation-discounts]を含む) はすべて自動的に適用されます。

別のホスト OS やコンテナー ランタイムを使用したり、さまざまなカスタム パッケージを含めたりする場合は、[aks-engine][aks-engine] を使用して独自の Kubernetes クラスターをデプロイします。 アップストリームの `aks-engine` リリースでは構成オプションに注目し、AKS クラスターでのサポートに先立ってそれらを提供しています。 そのため、`containerd` または [Moby](https://mobyproject.org/) 以外のコンテナー ランタイムの使用を検討している場合は、`aks-engine` を実行して、現在のニーズに合った Kubernetes クラスターを構成してデプロイできます。

### <a name="resource-reservations"></a>リソース予約

AKS では、ノード リソースを使用して、お使いのクラスターの一部としてノードを機能させることができます。 このような使い方では、お使いのノードの合計リソースと AKS での割り当て可能リソースが一致しなくなることがあります。 ユーザーがデプロイするポッドに要求と上限を設定するときは、この情報を念頭に置いてください。

ノードの割り当てできるリソースを見つけるには、次を実行します。
```kubectl
kubectl describe node [NODE_NAME]
```

ノードのパフォーマンスと機能を維持するために、AKS では各ノード上でリソースを予約します。 あるノードでリソース数が増えると、ユーザーがデプロイするポッドの管理の必要性が高くなるため、リソース予約が増大します。

>[!NOTE]
> Container Insights (OMS) などの AKS アドオンを使用すると、追加のノード リソースが消費されます。

2 種類のリソースが予約されています。

- **CPU**  
    予約された CPU は、ノードの種類とクラスター構成に依存します。追加の機能が実行されている場合は、割り当て可能な CPU が少なくなることがあります。

   | ホスト上の CPU コア数 | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube 予約 (ミリコア)|60|100|140|180|260|420|740|

- **[メモリ]**  
    AKS で使用されるメモリには、2 つの値の合計が含まれます。

   1. **`kubelet` デーモン**   
       `kubelet` デーモンは、コンテナーの作成と終了を管理するために、すべての Kubernetes エージェント ノードにインストールされます。 
   
        AKS の既定では、`kubelet` デーモンには、*memory.available<750Mi* という削除規則があり、ノードでは常に少なくとも 750 Mi のメモリが割り当て可能になっている必要があります。 ホストがその使用可能メモリのしきい値を下回ると、`kubelet` によって、実行中のいずれかのポッドを終了して、ホスト コンピューター上のメモリを解放する処理がトリガーされます。

   2. kubelet デーモンが適切に機能するための **予約されているメモリの回帰率** (*kube-reserved*)。
      - 最初の 4 GB のメモリの 25%
      - 次の 4 GB のメモリの 20% (最大 8 GB)
      - 次の 8 GB のメモリの 10% (最大 16 GB)
      - 次の 112 GB のメモリの 6% (最大 128 GB)
      - 128 GB を超えるメモリの 2%

メモリと CPU の割り当て規則:
* エージェント ノードを正常な状態に保ちます (クラスターの正常性に不可欠ないくつかのホスティング システム ポッドを含む)。 
* ノードで報告される割り当て可能なメモリと CPU は、それが Kubernetes クラスターの一部でない場合よりも少なくなります。 

上記のリソースの予約を変更することはできません。

たとえば、ノードで 7 GB が提供される場合、750Mi のハード削除しきい値を含めて、メモリの 34% が割り当て不可として報告されます。

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Kubernetes 自体の予約に加えて、基になるノード OS も OS 機能を維持するための CPU とメモリ リソースの量を予約します。

関連付けられているベスト プラクティスについては、[AKS の基本的なスケジューラ機能のベスト プラクティス][operator-best-practices-scheduler]に関するページを参照してください。

### <a name="node-pools"></a>ノード プール

同じ構成のノードは、*ノード プール* にグループ化できます。 1 つの Kubernetes クラスターには、少なくとも 1 つのノード プールが含まれています。 ノードとサイズの最初の数は、*既定のノード プール* の作成が行われる AKS クラスターの作成時に定義されています。 AKS のこの既定のノード プールには、お使いのエージェント ノードを実行する基本の VM が含まれています。

> [!NOTE]
> クラスターが確実に動作するようにするには、既定のノード プール内で少なくとも 2 つのノードを実行する必要があります。

AKS クラスターのスケーリングまたはアップグレードは既定のノード プールに対して行います。 特定のノード プールのスケーリングまたはアップグレードを選択できます。 アップグレード操作では、すべてのノードが正常にアップグレードされるまで、実行中のコンテナーはノード プ―ル内の他のノード上にスケジュールされます。

AKS での複数のノード プールの使用方法の詳細については、[AKS でのクラスターの複数のノード プールの作成と管理][use-multiple-node-pools]に関する記事をご覧ください。

### <a name="node-selectors"></a>ノードのセレクター

複数のノード プールを含む AKS クラスターでは、特定のリソースに使用するノード プールを、Kubernetes スケジューラに指示することが必要になる場合があります。 たとえば、Windows Server ノード上でイングレス コントローラーを実行することはできません。 

ノード セレクターでは、ノード OS などのさまざまなパラメーターを定義して、ポッドがスケジュールされる場所を制御できます。

次の基本的な例は、ノード セレクター *"beta.kubernetes.io/os": linux* を使用して Linux ノード上の NGINX インスタンスをスケジュールします。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

ポッドがスケジュールされる場所を制御する方法の詳細については、「[Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス][operator-best-practices-advanced-scheduler]」を参照してください。

## <a name="pods"></a>ポッド

Kubernetes は *ポッド* を使用して、お使いのアプリケーションのインスタンスを実行します。 ポッドは、アプリケーションの単一のインスタンスを表します。 

通常、ポッドにはコンテナーとの 1 対 1 のマッピングがあります。 高度なシナリオでは、1 つのポッドに複数のコンテナーが含まれる場合があります。 マルチコンテナー ポッドは、同じノード上にまとめてスケジュールされ、関連するリソースをコンテナーで共有できるようにします。

ポッドを作成する場合、"*リソース要求*" を定義して、一定量の CPU やメモリ リソースを要求できます。 Kubernetes スケジューラでは、利用可能なリソースを備えたノード上で実行されるようにポッドをスケジュールして、その要求を満たそうとします。 また、あるポッドで基になるノードからのコンピューティング リソース消費量が過大にならないように、リソースの上限を指定することもできます。 ベスト プラクティスは、必要な許可されているリソースを Kubernetes スケジューラで特定できるように、すべてのポッドにリソース制限を組み入れることです。

詳細については、[Kubernetes ポッド][kubernetes-pods]と [Kubernetes ポッドのライフサイクル][kubernetes-pod-lifecycle]に関するページをご覧ください。

ポッドは論理リソースですが、アプリケーション ワークロードはコンテナー上で実行されます。 ポッドは通常、短期間の破棄可能なリソースです。 個々にスケジュール設定されたポッドには、Kubernetes の機能である高可用性および冗長性の一部がありません。 代わりに、ポッドは、デプロイ コントローラーなどの Kubernetes の *コントローラー* によってデプロイされ管理されます。

## <a name="deployments-and-yaml-manifests"></a>デプロイと YAML マニフェスト

*デプロイ* は、Kubernetes デプロイ コントローラーによって管理される同一のポッドを表します。 デプロイでは、作成するポッドの "*レプリカ* " の数が定義されます。 Kubernetes スケジューラでは、ポッドまたはノードで問題が発生した場合に追加のポッドが正常なノード上にスケジュールされるようにします。

デプロイを更新して、ポッドの構成、使用されるコンテナー イメージの構成、またはアタッチされたストレージの構成を変更できます。 デプロイ コントローラーの機能は以下のとおりです。
* 指定された数のレプリカをドレインして終了します。
* 新しいデプロイ定義からレプリカを作成します。
* デプロイ内のすべてのレプリカが更新されるまで、プロセスを続行します。

AKS にある最もステートレスなアプリケーションでは、個々のポッドをスケジュール設定するのではなく、デプロイ モデルを使用する必要があります。 Kubernetes では、必要な数のレプリカがクラスター内で確実に実行されるように、デプロイの正常性と状態を監視できます。 個々にスケジュール設定すると、ポッドは問題が発生した場合に再起動されず、その現在のノードで問題が発生した場合に正常なノード上に再スケジュールされません。

お使いのアプリケーションで最低限の数の利用可能なインスタンスが必要とされる場合、管理上の判断が更新プロセスによって中断されることは望ましくありません。 "*ポッドの中断バジェット*" では、更新やノードのアップグレードの期間中にデプロイ内で停止できるレプリカの数を定義します。 たとえば、デプロイ内に *5* つのレプリカがある場合、ポッド中断数を *4* と定義して、一度に削除または再スケジュールできるレプリカを 1 つのみにすることが可能です。 ポッドのリソース制限と同様に、ベスト プラクティスは、最低限の数のレプリカが常に存在する必要があるアプリケーション上に、ポッドの中断バジェットを定義することです。

デプロイは通常、`kubectl create` または `kubectl apply` で作成および管理されます。 デプロイを作成するには、YAML 形式のマニフェスト ファイルを定義します。 

次の例では、NGINX Web サーバーの基本のデプロイを作成します。 デプロイでは、*3* 個のレプリカを作成すること、またポート *80* をコンテナー上に開くことを指定します。 また、CPU とメモリに対しては、リソース要求と制限が定義されます。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

YAML マニフェスト内にサービス (ロード バランサーなど) を含めると、より複雑なアプリケーションを作成できます。

詳細については、[Kubernetes のデプロイ][kubernetes-deployments]に関するページをご覧ください。

### <a name="package-management-with-helm"></a>Helm によるパッケージの管理

Kubernetes では、アプリケーションの管理に [Helm][helm] がよく使用されます。 リソースをデプロイするには、アプリケーション コードのパッケージ化バージョンと、Kubernetes YAML マニフェストを含む既存のパブリック Helm *Chart* を構築して使用します。 Helm Chart はローカルにも、リモート リポジトリ ([Azure Container Registry の Helm Chart リポジトリ][acr-helm]など) 内にも保存できます。

Helm を使用するには、コンピューターに Helm クライアントをインストールするか、[Azure Cloud Shell][azure-cloud-shell] で Helm クライアントを使用します。 Helm Chart を検索または作成してから、お使いの Kubernetes クラスターにインストールします。 詳細については、「[AKS で Helm を使用して既存のアプリケーションをインストールする][aks-helm]」を参照してください。

## <a name="statefulsets-and-daemonsets"></a>StatefulSet と DaemonSet

デプロイ コントローラーでは、Kubernetes スケジューラを使って、利用可能なリソースを備えた利用可能な任意のノード上でレプリカを実行します。 ステートレス アプリケーションにはこの方法で十分ですが、以下を必要とするアプリケーションにはデプロイ コントローラーは適していません。
* 永続的な名前付け規則またはストレージ。 
* クラスター内の各選択ノード上にレプリカが存在する。

ただし、次の 2 つの Kubernetes リソースを使用すると、こうした種類のアプリケーションを管理できます。

- *StatefulSet* は、ストレージなど、個々のポッド ライフサイクルを超えて、アプリケーションの状態を保守します。
- *DaemonSet* は、Kubernetes ブートストラップ プロセスの初期段階で、各ノード上で実行されるインスタンスを確保します。

### <a name="statefulsets"></a>StatefulSet

モダン アプリケーション開発は、多くの場合、ステートレス アプリケーションを対象としています。 ステートフル アプリケーション (データベース コンポーネントを含むものなど) には、*StatefulSets* を使用できます。 デプロイと同様に、StatefulSet では少なくとも 1 つの同一ポッドを作成して管理します。 デプロイ、スケーリング、アップグレード、および強制終了において、StatefulSet 内のレプリカは、適切な順序付けの手法に従います。 StatefulSet でレプリカが再スケジュールされるときに、名前付け規則、ネットワーク名、およびストレージが永続化されます。

`kind: StatefulSet` を使用して、アプリケーションを YAML 形式で定義します。 そこから、StatefulSet コントローラーによって、必要なレプリカのデプロイと管理が処理されます。 データは、Azure Managed Disks または Azure Files によって提供された永続的なストレージに書き込まれます。 複数の StatefulSet を使用すると、StatefulSet が削除されても、基になる永続化ストレージは残ります。

詳細については、[Kubernetes の StatefulSet][kubernetes-statefulsets] に関するページを参照してください。

StatefulSet 内のレプリカは、1 つの AKS クラスター内にある任意の利用可能なノード全体にスケジュールされて、実行されます。 セット内の少なくとも 1 つのポッドがノード上で実行されるようにするには、代わりに DaemonSet を使用します。

### <a name="daemonsets"></a>DaemonSet

特定のログ コレクションや監視のために、すべてのノードまたは選択されたノード上で 1 つのポッドを実行する必要が生じる場合があります。 *DaemonSet* を使用して 1 つ以上の同一ポッドをデプロイすることもできますが、DaemonSet コントローラーでは、指定された各ノードでそのポッドのインスタンスが確実に実行されるようにします。

DaemonSet コントローラーでは、既定の Kubernetes スケジューラが起動済みになる前に、クラスターのブート プロセスの初期段階でノード上にポッドをスケジュール設定できます。 この機能によって、1 つのデプロイまたは StatefulSet 内の従来のポッドがスケジュールされる前に、確実に DaemonSet 内のポッドが開始されます。

StatefulSet と同様に、DaemonSet は `kind: DaemonSet` を使用して、YAML 定義の一部として定義されています。

詳細については、[Kubernetes の DaemonSet][kubernetes-daemonset] に関するページを参照してください。

> [!NOTE]
> [仮想ノードのアドオン](virtual-nodes-cli.md#enable-virtual-nodes-addon)を使用している場合は、DaemonSet によって仮想ノード上にポッドが作成されることはありません。

## <a name="namespaces"></a>名前空間

AKS クラスターを分割し、リソースへのアクセスの作成、表示、管理を制限するために、ポッドやデプロイなどの Kubernetes リソースは、論理的に 1 つの "*名前空間*" にグループ化されます。 たとえば、名前空間を作成して業務グループを分けることができます。 ユーザーは、割り当てられている名前空間内のリソースのみを操作できます。

![リソースとアプリケーションを論理的に分割する Kubernetes の名前空間](media/concepts-clusters-workloads/namespaces.png)

AKS クラスターを作成すると、次の名前空間が利用可能になります。

| 名前空間 | 説明 |  
| ----------------- | ------------- |  
| *default*                                                                                 | 何も指定しない場合に、既定でポッドやデプロイが作成される場所です。 より小規模な環境では、追加の論理分割を作成せずに、既定の名前空間にアプリケーションを直接デプロイできます。 `kubectl get pods` などの Kubernetes API を操作する場合、何も指定しないと、既定の名前空間が使用されます。                                                        |  
| *kube-system* | DNS やプロキシのようなネットワーク機能、または Kubernetes ダッシュボードなど、コア リソースが置かれている場所です。 通常は、この名前空間に独自のアプリケーションをデプロイしません。                                      |  
| *kube-public*                                                                            | 通常は使用されませんが、クラスター全体でリソースを表示可能にして、ユーザーが確認できるようにするために使用できます。                                                                                    |  


詳細については、[Kubernetes の名前空間][kubernetes-namespaces] に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、Kubernetes の主要なコンポーネントの一部と、それらのコンポーネントを AKS クラスターに適用する方法について説明しました。 Kubernetes と AKS の中心概念の詳細については、次の記事を参照してください。

- [Kubernetes/AKS のアクセスと ID][aks-concepts-identity]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
