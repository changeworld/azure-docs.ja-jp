---
title: リソース管理のベストプラクティス
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) でのリソース管理に関するアプリケーション開発者のベスト プラクティスについて説明します
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105274"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でリソースを管理するアプリケーション開発者のベスト プラクティス

Azure Kubernetes Service (AKS) でアプリケーションを開発して実行する際に、考慮すべき主な領域がいくつかあります。 アプリケーション デプロイの管理方法は、提供するサービスのエンド ユーザー エクスペリエンスに悪影響を与える場合があります。 適切に行えるように、AKS でアプリケーションを開発して実行する際に従うことができるいくつかのベスト プラクティスに留意してください。

この記事では、アプリケーション開発者の観点からクラスターとワークロードを実行することに焦点を当てています。 管理のベスト プラクティスについては、[Azure Kubernetes Service (AKS) での分離とリソース管理に関するクラスター オペレーターのベスト プラクティス][operator-best-practices-isolation]に関するページを参照してください。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * ポッド リソースの要求と制限
> * Bridge to Kubernetes と Visual Studio Code を使用したアプリケーションの開発およびデプロイ方法。
> * `kube-advisor` ツールを使用してデプロイに関する問題を確認する方法。

## <a name="define-pod-resource-requests-and-limits"></a>ポッドのリソースの要求と制限を定義する

> **ベスト プラクティスのガイダンス**
> 
> YAML マニフェストですべてのポッドにポッドの要求と制限を設定します。 AKS クラスターで "*リソース クォータ*" を使用しており、これらの値を定義していない場合に、デプロイが拒否される場合があります。

ポッドの要求と制限を使用して、AKS クラスター内のコンピューティング リソースを管理してください。 ポッドの要求と制限によって、Kubernetes スケジューラに、どのコンピュート リソースをポッドに割り当てるかを通知します。

### <a name="pod-cpumemory-requests"></a>ポッドの CPU とメモリ要求
"*ポッドの要求*" により、ポッドに通常必要な一定量の CPU およびメモリを定義します。

ポッドの指定では、上記の情報に基づいてこれらの要求と制限を定義することが **ベストプラクティスであり、非常に重要** です。 これらの値を含めないと、Kubernetes スケジューラは、スケジュール設定の決定を支援するためにアプリケーションが必要とするリソースを考慮に入れることができません。

アプリケーションのパフォーマンスを監視し、ポッドの要求を調整します。 
* ポッドの要求を過小評価すると、ノードのスケジュールが過剰になるため、アプリケーションのパフォーマンスが低下するおそれがあります。 
* 要求を過大に見積もると、アプリケーションのスケジュール設定がより困難になるおそれがあります。

### <a name="pod-cpumemory-limits"></a>ポッドの CPU とメモリの制限** 
"*ポッドの制限*" により、ポッドで使用できる CPU とメモリの最大量が設定されます。 

* "*メモリの制限*" により、リソース不足でノードが不安定になったときに、どのポッドを強制終了するかを定義します。 適切な制限が設定されていないと、リソース不足が解消されるまでポッドの強制終了が行われます。 
* ポッドは定期的に "*CPU の制限*" を超えることがありますが、CPU の制限を超えたからといってポッドが強制終了されることはありません。 

ポッドの制限により、ポッドがいつリソース消費の制御を失うかを定義します。 制限を超えると、ポッドには強制終了のマークが付けられます。 この動作により、ノードの正常性が維持され、ノードを共有するポッドへの影響が最小限に抑えられます。 ポッド制限を設定しないと、既定で、特定のノードで使用可能な最大値に設定されます。

ポッドの制限を、ノードでサポートできる制限より高く設定しないでください。 各 AKS ノードでは、主要な Kubernetes コンポーネント用に一定量の CPU とメモリが予約されます。 アプリケーションでは、ノードで他のポッドを正常に実行するのに過剰なリソースの使用が試行される場合があります。

1 日または 1 週間の異なる時間にアプリケーションのパフォーマンスを監視します。 需要のピーク時を判断し、最大のニーズを満たすために必要なリソースにポッドの制限を合わせます。

> [!IMPORTANT]
>
> ポッド仕様で、上記の情報に基づいてこれらの要求と制限を定義します。 これらの値を含めないと、Kubernetes スケジューラによって、スケジュールの決定を支援するためにアプリケーションに必要なリソースを考慮できなくなります。

スケジューラによって、リソースが不足しているノードにポッドが配置されると、アプリケーションのパフォーマンスが低下します。 クラスター管理者は、リソースの要求と制限を設定するために必要な名前空間に、"*リソース クォータ*" を設定する **必要があります**。 詳細については、[AKS クラスターでのリソース クォータ][resource-quotas]に関する記事を参照してください。

CPU の要求または制限を定義する場合、値は CPU 単位で測定されます。 
* *1.0* CPU は、ノード上の 1 つの基になる仮想 CPU コアに相当します。 
    * これと同じ測定が GPU で使用されます。
* 端数は、ミリコア単位で測定できます。 たとえば、*100m* は、基になる vCPU コアの *0.1* です。

1 つの NGINX ポッドの次の基本的な例では、ポッドで *100m* の CPU 時間と、*128Mi* のメモリが要求されます。 ポッドのリソース制限は、*250m* CPU および *256Mi* メモリに設定されます。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

リソースの測定と割り当ての詳細については、「[コンテナーのコンピューティング リソースの管理][k8s-resource-limits]」を参照してください。

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>AKS クラスターに対するアプリケーションの開発とデバッグを行う

> **ベスト プラクティスのガイダンス** 
>
> 開発チームでは、Bridge to Kubernetes を使用して AKS クラスターに対するデプロイおよびデバッグを行う必要があります。

Bridge to Kubernetes を使用すると、AKS クラスターに対して直接アプリケーションを開発、デバッグ、テストできます。 チーム内の開発者は共同作業でアプリケーション ライフサイクル全体を通してビルドとテストを行います。 Visual Studio や Visual Studio Code などの既存のツールを Bridge to Kubernetes 拡張機能で引き続き使用することができます。 

Bridge to Kubernetes を使用するこの統合された開発とテストのプロセスを使用すると、[minikube][minikube] などのローカル テスト環境の必要性が低くなります。 代わりに、AKS クラスターを開発してテストします。これは、セキュリティで保護され、分離されたクラスターであっても同様です。 

> [!NOTE]
> Bridge to Kubernetes は、Linux ポッドおよびノード上で実行されるアプリケーションで使用することを目的としています。

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>Kubernetes 用の Visual Studio Code (VS Code) 拡張機能を使用する

> **ベスト プラクティスのガイダンス** 
>
> YAML マニフェストを記述する場合、Kubernetes 用の VS Code 拡張機能をインストールして使用します。 統合されたデプロイ ソリューション用の拡張機能を使用することもできます。これは、AKS クラスターをあまり操作しないアプリケーション所有者に役立つ場合があります。

[Kubernetes 用の Visual Studio Code 拡張機能][vscode-kubernetes]は、アプリケーションの開発と AKS へのデプロイに役立ちます。 この拡張機能には、次の機能があります。
* Kubernetes リソース、Helm chart、およびテンプレートの IntelliSense。 
* VS Code 内からの Kubernetes リソースの閲覧、デプロイ、機能の編集。 
* ポッド仕様で設定されているリソースの要求や制限に関する IntelliSense チェック。

    ![メモリ制限の欠落について警告する Kubernetes 用の VS Code 拡張機能](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>kube-advisor を使用してアプリケーション問題を定期的に確認する

> **ベスト プラクティスのガイダンス** 
> 
> 最新バージョンの `kube-advisor` オープンソース ツールを定期的に実行して、クラスター内の問題を検出します。 既存の AKS クラスターでリソース クォータを適用する前に `kube-advisor` を実行し、リソースの要求と制限が定義されていないポッドを見つけます。

[kube-advisor][kube-advisor] ツールは、Kubernetes クラスターをスキャンし、特定された問題について報告する、関連する AKS オープンソース プロジェクトです。 確認の際に、リソースの要求と制限が存在しないポッドの特定もできるため便利です。

`kube-advisor` ツールを使用すると、Windows および Linux アプリケーション用の PodSpecs にないリソースの要求と制限をレポートすることができますが、`kube-advisor` 自体は Linux ポッド上でスケジュールする必要があります。 ポッドの構成で[ノード セレクター][k8s-node-selector]を使用して、特定の OS のノード プールで実行するようにポッドをスケジュールすることができます。

多くの開発チームとアプリケーションをホストする AKS クラスターでは、リソースの要求と制限を使用してポッドを追跡する方が簡単です。 ベスト プラクティスとして、AKS クラスターで `kube-advisor` を定期的に実行します。

## <a name="next-steps"></a>次のステップ

この記事では、クラスター オペレーターの観点からクラスターとワークロードを実行する方法に重点を置きました。 管理のベスト プラクティスについては、[Azure Kubernetes Service (AKS) での分離とリソース管理に関するクラスター オペレーターのベスト プラクティス][operator-best-practices-isolation]に関するページを参照してください。

これらのベスト プラクティスのいくつかを実装する場合は、次の記事を参照してください。

* [Bridge to Kubernetes を使用した開発][btk]
* [kube-advisor を使用した問題の確認][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
