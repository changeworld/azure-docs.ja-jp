---
title: クラスター分離に関するベスト プラクティス
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) での分離に関するクラスター オペレーターのベスト プラクティスについて説明します
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105172"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) でのクラスターの分離に関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理する際は、多くの場合、チームとワークロードを分離する必要があります。 AKS では、柔軟にマルチ テナント クラスターを実行してリソースを分離することができます。 Kubernetes への投資を最大限に活用するには、まず AKS マルチテナントおよび分離機能を理解して実装します。

このベスト プラクティスの記事では、クラスター オペレーターを対象とした分離に重点を置いています。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * マルチ テナント クラスターとリソースの分離の計画
> * AKS クラスターでの論理的または物理的な分離の使用

## <a name="design-clusters-for-multi-tenancy"></a>マルチ テナント用にクラスターを設計する

Kubernetes では、同じクラスター内のチームとワークロードを論理的に分離できます。 目標は、各チームが必要とするリソースに限定して、最小限の数の権限を提供することです。 Kubernetes の[名前空間][k8s-namespaces]では、論理的な分離境界が作成されます。 その他の Kubernetes の機能および分離とマルチテナントに関する考慮事項には、次の領域が含まれます。

### <a name="scheduling"></a>スケジュール設定

*"スケジュール設定"* では、リソース クォータやポッドの中断バジェットなどの基本的な機能を使用します。 これらの機能の詳細については、[AKS の基本的なスケジューラ機能のベスト プラクティス][aks-best-practices-scheduler]に関するページを参照してください。

より高度なスケジューラ機能は次のとおりです。
* テイントと容認
* ノードのセレクター
* ノードとポッドのアフィニティまたは非アフィニティ。 

これらの機能の詳細については、[AKS の高度なスケジューラ機能のベスト プラクティス][aks-best-practices-advanced-scheduler]に関するページを参照してください。

### <a name="networking"></a>ネットワーク

*"ネットワーク"* では、ポッド内外のトラフィックのフローを制御するためのネットワーク ポリシーが使用されます。

### <a name="authentication-and-authorization"></a>認証と権限承認

*"認証と認可"* では次のものが使用されます。
* ロール ベースのアクセス制御 (RBAC)
* Azure Active Directory (AD) 統合
* ポッド ID
* Azure Key Vault のシークレット 

これらの機能の詳細については、[AKS の認証と承認のベスト プラクティス][aks-best-practices-identity]に関するページを参照してください。

### <a name="containers"></a>Containers
*"コンテナー"* には次のものが含まれます。
* ポッド セキュリティを適用するための AKS 用の Azure Policy アドオン。
* Pod セキュリティ コンテキストの使用。
* イメージとランタイムの両方での脆弱性のスキャン。 
* 基になるノードへのコンテナー アクセスを制限するための App Armor や Seccomp (セキュア コンピューティング) の使用。

## <a name="logically-isolate-clusters"></a>クラスターを論理的に分離する

> **ベスト プラクティスのガイダンス**
>
> *"論理的な分離"* を使用して、チームとプロジェクトを分離します。 チームまたはアプリケーションを分離するためにデプロイする物理 AKS クラスターの数を最小限にします。

論理的な分離では、1 つの AKS クラスターを、複数のワークロード、チーム、環境で使用できます。 Kubernetes の[名前空間][k8s-namespaces]では、ワークロードとリソースの論理的な分離境界を形成します。

![AKS での Kubernetes クラスターの論理的な分離](media/operator-best-practices-cluster-isolation/logical-isolation.png)

クラスターを論理的に分離すると、通常、物理的に分離されたクラスターよりもポッド密度が高くなり、クラスター内でアイドル状態の過剰なコンピューティング容量が少なくなります。 Kubernetes クラスターの自動スケーラーを組み合わせることで、ノード数をスケール アップまたはスケール ダウンして需要を満たすことできます。 自動スケーリングを行うためのこのベスト プラクティスの方法を使用すれば、必要な数のノードのみを実行することでコストが最小限に抑えられます。

現在、Kubernetes 環境は悪意のあるマルチテナントの使用に対して完全に安全ではありません。 マルチテナント環境では、共通の共有インフラストラクチャ上で複数のテナントが動作しています。 すべてのテナントを信頼できない場合は、テナントが他のテナントのセキュリティやサービスに影響を与えることを防ぐために、追加の計画が必要になります。

*"ポッドのセキュリティ ポリシー"* やノード用の Kubernetes RBAC などの追加のセキュリティ機能により、悪用を効率的にブロックします。 悪意のあるマルチテナント ワークロードを実行する場合の真のセキュリティのために、ハイパーバイザーのみを信頼する必要があります。 Kubernetes 用のセキュリティ ドメインは、個々のノードではなく、クラスター全体になります。 

この種の悪意のあるマルチテナント ワークロードでは、物理的に分離されたクラスターを使用する必要があります。

## <a name="physically-isolate-clusters"></a>クラスターを物理的に分離する

> **ベスト プラクティスのガイダンス** 
>
> 個々のチームまたはアプリケーションのデプロイごとの物理的な分離の使用を最小限に抑えます。 代わりに、前のセクションで説明したように、*論理的な* 分離を使用します。

AKS クラスターを物理的に分離することが、クラスターを分離するための一般的な方法です。 この分離モデルでは、チームまたはワークロードに独自の AKS クラスターが割り当てられます。 物理的な分離は、ワークロードやチームを分離する最も簡単な方法のようですが、管理と財務のオーバーヘッドが増えます。 ここでこれらの複数のクラスターを維持し、個別にアクセスを提供してアクセス許可を割り当てる必要があります。 また、個々のノードについて課金されます。

![AKS での個々の Kubernetes クラスターの物理的な分離](media/operator-best-practices-cluster-isolation/physical-isolation.png)

物理的に分離されたクラスターのポッドの密度は通常、低くなります。 各チームまたはワークロードには独自の AKS クラスターがあるため、クラスターは多くの場合、コンピューティング リソースで過剰にプロビジョニングされます。 多くの場合、ノード上には少数のポッドがスケジュールされます。 ノード上の未使用の容量は、他のチームによって開発中のアプリケーションやサービスで使用することはできません。 これらの過剰なリソースは、物理的に分離されたクラスターでの追加コストの原因となります。

## <a name="next-steps"></a>次のステップ

この記事ではクラスターの分離に重点を置きました。 AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]
* [Kubernetes スケジューラの高度な機能][aks-best-practices-advanced-scheduler]
* [認証と権限承認][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
