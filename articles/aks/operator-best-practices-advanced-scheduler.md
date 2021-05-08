---
title: スケジューラの機能に関するベスト プラクティス
titleSuffix: Azure Kubernetes Service
description: テイントと容認、ノード セレクターとアフィニティ、ポッド間アフィニティと非アフィニティなど、Azure Kubernetes Service (AKS) の高度なスケジューラ機能の使用に関する、クラスター オペレーターのベスト プラクティスについて説明します
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 27b32d7d10b691ed806e4d7aa31a095630d2bfc9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103625"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理する際は、多くの場合、チームとワークロードを分離する必要があります。 Kubernetes スケジューラで提供される高度な機能を使用すると、次のことが制御できます。
* 特定のノードでスケジュールできるポッド。
* マルチポッド アプリケーションをクラスター全体へ適切に分散させる方法。 

このベスト プラクティス記事では、クラスター オペレーター向けに Kubernetes の高度なスケジュール機能について説明します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * テイントと容認を使用して、ノードでスケジュールできるポッドを制限します。
> * ノード セレクターまたはノード アフィニティを使用して、特定のノードで実行するポッドに優先順位を指定します。
> * ポッド間アフィニティまたは非アフィニティを使用して、ポッドを分割したりグループ化したりします。

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>テイントと容認を使用して専用のノードを提供する

> **ベスト プラクティスのガイダンス:** 
>
> イングレス コントローラーなどのリソースを多量に消費するアプリケーションへのアクセスを特定のノードに制限します。 ノードのリソースをそれが必要なワークロードで使用できるように保ち、そのノードでは他のワークロードをスケジュールできないようにします。

AKS クラスターを作成するときは、GPU のサポートや多数の強力な CPU を備えたノードをデプロイできます。 このようなノードは、機械学習 (ML) や人工知能 (AI) などの大規模なデータ処理ワークロードに使用できます。 

このノード リソース ハードウェアは通常、デプロイにコストがかかるため、これらのノードでスケジュールできるワークロードを制限します。 代わりに、クラスター内にある一部のノードをイングレス サービスの実行専用にして、他のワークロードを防ぐこともできます。

さまざまなノードに対するこのサポートは、複数のノード プールを使用して提供されます。 AKS クラスターでは、1 つ以上のノード プールが提供されます。

Kubernetes スケジューラでは、テイントと容認を使用して、ノードで実行できるワークロードを制限できます。

* **テイント** をノードに適用して、特定のポッドのみをノードにスケジュールできることを示します。
* 次に、ポッドに **容認** を適用して、ノードのテイントを *容認* できるようにします。

ポッドを AKS クラスターにデプロイすると、Kubernetes によってテイントが容認と一致するノードでのみポッドがスケジュールされます。 たとえば、GPU をサポートするノード用のノード プールが AKS クラスターにあるとします。 *gpu* などの名前と、スケジュールのための値を定義します。 この値を *NoSchedule* に設定すると、Kubernetes スケジューラによってノード上で未定義の容認を使用したポッドがスケジュールされることが制限されます。

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

ノードにテイントを適用したら、そのノードでのスケジュールを可能にする容認をポッドの仕様で定義します。 次の例では、前のステップでノードに適用したテイントを許容するための `sku: gpu` と `effect: NoSchedule` を定義しています。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

`kubectl apply -f gpu-toleration.yaml` を使用してこのポッドをデプロイすると、Kubernetes によってテイントが適用されたノードでポッドが正常にスケジュールできます。 この論理的な分離を使用することで、クラスター内のリソースへのアクセスを制御できます。

テイントを適用するときは、アプリケーションの開発者や所有者と協力して、デプロイで必要な容認を定義できるようにします。

AKS での複数のノード プールの使用方法の詳細については、[AKS でのクラスターの複数のノード プールの作成と管理][use-multiple-node-pools]に関する記事をご覧ください。

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>AKS でのテイントと容認の動作

AKS でノード プールをアップグレードすると、テイントと容認は新しいノードに適用されるときに、次のように設定されたパターンに従います。

#### <a name="default-clusters-that-use-vm-scale-sets"></a>仮想マシン スケール セットを使用するデフォルトのクラスター
AKS API から[ノード プールのテイントを設定][taint-node-pool]して、新たにスケールアウトされたノードが API で指定されたノードのテイントを受け取るようにすることができます。

次のような場合を考えてみましょう。
1. 2 ノード クラスター、*node1* と *node2* から始めます。 
1. ノード プールをアップグレードします。
1. *node3* と *node4* の 2 つの追加ノードが作成されます。 
1. テイントはそれぞれ渡されます。
1. 元の *node1* と *node2* は削除されます。

#### <a name="clusters-without-vm-scale-set-support"></a>仮想マシン スケール セットをサポートしていないクラスター

ここでも、次のことを想定してみましょう。
1. 2 つのノード クラスター、*node1* と *node2* があるとします。 
1. 次に、ノード プールをアップグレードします。
1. 追加のノード、*node3* が作成されます。
1. *node1* のテイントが *node3* に適用されます。
1. *node1* が削除されます。
1. 新しい *node1* が作成され、元の *node1* と置き換えられます。
1. *node2* のテイントが新しい *node1* に適用されます。 
1. *node2* が削除されます。

つまり、*node1* が *node3* になり、*node2* が新しい *node1* になります。

AKS でノード プールをスケーリングするとき、テイントと容認は設計により持ち越されません。

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>ノード セレクターとアフィニティを使用してポッドのスケジュールを制御する

> **ベスト プラクティスのガイダンス** 
> 
> ノード セレクター、ノードのアフィニティ、またはポッド間のアフィニティを使用して、ノード上のポッドのスケジュールを制御します。 これらの設定を使用すると、Kubernetes のスケジューラは、ノードのハードウェアなどによって、ワークロードを論理的に分離できます。

テイントと容認は、ハードカット オフでリソースを論理的に分離します。 ポッドがノードのテイントを許容していない場合、そのノードはノード上でスケーリングされていません。 

または、ノード セレクターを使用することもできます。 たとえば、ローカルにアタッチされた SSD ストレージや大容量のメモリを示すラベルをノードに付けてから、ポッドの仕様でノード セレクターを定義します。 Kubernetes は一致するノード上にこれらのポッドをスケジュールします。 

容認とは異なり、一致するノード セレクターのないポッドは、ラベルが付いているノードでもスケジュールできます。 この動作により、ノード上にある未使用のリソースを消費できますが、一致するノード セレクターが定義されているポッドが優先されます。

大容量メモリを装備したノードの例を見てみましょう。 これらのノードでは、大量のメモリを要求するポッドが優先されます。 リソースがアイドル状態にならないようにするために、他のポッドの実行も許可されます。

```console
kubectl label node aks-nodepool1 hardware=highmem
```

ポッドの仕様に、ノードで設定されているラベルと一致するノード セレクターを定義する `nodeSelector` プロパティを追加します。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

これらのスケジューラ オプションを使用するときは、アプリケーションの開発者および所有者と協力して、ポッドの仕様を正しく定義できるようにします。

ノード セレクターの使用について詳しくは、「[Assigning Pods to Nodes][k8s-node-selector]」 (ノードへのポッドの割り当て) をご覧ください。

### <a name="node-affinity"></a>ノード アフィニティ

ノード セレクターは、特定のノードにポッドを割り当てる際の基本的なソリューションです。 *ノードのアフィニティ* により柔軟性が向上し、ポッドがノードと一致できない場合に起こる事象を定義できるようになります。 次の操作を行います。 
* Kubernetes スケジューラがポッドをラベルの付いたホストと一致させる *必要* があります。 または、
* 一致させることを *優先* しますが、一致するものがない場合は、ポッドが別のホストでスケジュールできるようにします。

次の例では、ノード アフィニティを *requiredDuringSchedulingIgnoredDuringExecution* に設定しています。 このアフィニティでは、一致するラベルを持つノードを使用することが要求されます。 使用できるノードがない場合、ポッドはスケジュールの継続を待機する必要があります。 ポッドを別のノードでスケジュールできるようにするには、値を ***preferred** DuringSchedulingIgnoreDuringExecution* に設定します。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

設定の *IgnoredDuringExecution* の部分は、ノードのラベルが変更された場合に、ポッドをノードから削除してはならないことを示しています。 Kubernetes スケジューラでは、新しくスケジュールされるポッドに対してのみ更新されたノード ラベルが使用され、ノードで既にスケジュールされているポッドには使用されません。

詳しくは、「[Affinity and anti-affinity][k8s-affinity]」 (アフィニティと非アフィニティ) をご覧ください。

### <a name="inter-pod-affinity-and-anti-affinity"></a>ポッド間アフィニティと非アフィニティ

Kubernetes スケジューラでワークロードを論理的に分離する最後の方法では、ポッド間アフィニティまたは非アフィニティを使用します。 これらの設定は、一致するポッドが既にあるノードでポッドをスケジュールする *必要がない* か、あるいは *必要がある* かのいずれかを定義できます。 既定では、Kubernetes スケジューラは、ノードをまたぐレプリカ セットに複数のポッドのスケジュールを試みます。 この動作に関しては、さらに具体的なルールを定義できます。

たとえば、Azure Cache for Redis も使用する Web アプリケーションがあります。 
1. ポッドの非アフィニティ ルールを使用して、Kubernetes スケジューラによってノード間でレプリカを分散するように要求します。 
1. アフィニティ ルールを使用して、各 Web アプリ コンポーネントが対応するキャッシュと同じホストにスケジュールされるようにします。 

複数ノードへのポッドの分散は、次の例のようになります。

| **ノード 1** | **ノード 2** | **ノード 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

ポッド間のアフィニティと非アフィニティは、ノード セレクターまたはノード アフィニティよりも複雑なデプロイを提供します。 デプロイすることで、リソースを論理的に分離し、Kubernetes がノードでポッドをスケジュールする方法を制御できます。 

Azure Cache for Redis を使用するこの Web アプリケーションの完全な例については、[同じノードへのポッドの併置][k8s-pod-affinity]に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

この記事では、Kubernetes の高度なスケジューラ機能に注目しました。 AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [マルチ テナントとクラスター分離][aks-best-practices-scheduler]
* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]
* [認証と権限承認][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
