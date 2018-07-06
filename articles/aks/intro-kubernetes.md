---
title: Azure Kubernetes Service の概要
description: Azure Kubernetes Service では、Azure でコンテナー ベースのアプリケーションを簡単にデプロイして管理できます。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: cb38285a009d8dfba175de6e3037970e6111d929
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096129"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) を使用すると、マネージド Kubernetes クラスターを Azure 内に簡単にデプロイできます。 AKS では、責任の多くを Azure にオフロードすることで、Kubernetes の管理の複雑さと運用上のオーバーヘッドを軽減します。 ホストされた Kubernetes サービスとして、Azure は正常性監視やメンテナンスなどの重要なタスクを自動的に処理します。 さらに、このサービスは無料であるため、料金は、マスターではなく、クラスター内のエージェント ノードに対してのみ発生します。

このドキュメントでは、Azure Kubernetes Service (AKS) の機能の概要を説明します。

## <a name="flexible-deployment-options"></a>柔軟なデプロイ オプション

Azure Kubernetes Service では、ポータル、コマンドライン、およびテンプレート駆動型のデプロイ オプション (Resource Manager テンプレートおよび Terraform) が用意されています。 AKS クラスターをデプロイすると、Kubernetes マスターとすべてのノードが自動的にデプロイされ、構成されます。 デプロイ プロセス中に、高度なネットワーク、Azure Active Directory 統合、監視などの追加機能も構成できます。

詳細については、[AKS ポータルのクイックスタート][aks-portal]と [AKS CLI のクイックスタート][aks-cli]に関するページを両方とも参照してください。

## <a name="identity-and-security-management"></a>ID とセキュリティ管理

AKS クラスターでは、[ロールベースのアクセス制御 (RBAC)][kubernetes-rbac] がサポートされています。 AKS クラスターは、Azure Active Directory と統合されるように構成することもできます。 この構成では、Azure Active Directory ID およびグループ メンバーシップに基づいて、Kubernetes アクセスを構成できます。

詳細については、[Azure Active Directory と AKS の統合][aks-aad]に関するページをご覧ください。

## <a name="integrated-logging-and-monitoring"></a>ログ記録と監視の統合

コンテナーの正常性では、コンテナー、ノード、およびコントローラーからメモリやプロセッサのメトリックを収集することにより、パフォーマンスが可視化されます。 コンテナーのログも収集されます。 このデータは、Log Analytics ワークスペースに格納され、Azure portal、Azure CLI、または REST エンドポイントを介して使用できます。

詳細については、[Azure Kubernetes Service コンテナーの正常性の監視][container-health]に関するページを参照してください。

## <a name="cluster-node-scaling"></a>クラスター ノードのスケーリング

リソースに対する需要が高まると、それに対応するために AKS クラスターのノードをスケール アウトできます。 リソースの需要が低下すると、クラスター内でスケーリングすることでノードを削除できます。 AKS スケールの操作は、Azure portal または Azure CLI を使用して実行できます。

詳細については、「[Azure Kubernetes Service (AKS) クラスターのスケーリング][aks-scale]」を参照してください。

## <a name="cluster-node-upgrades"></a>クラスター ノードのアップグレード

Azure Kubernetes Service では、複数の Kubernetes バージョンを提供しています。 AKS で新しいバージョンが使用できるようになると、Azure portal または Azure CLI を使用してクラスターをアップグレードできます。 アップグレード プロセス中、実行中のアプリケーションの中断を最小限に抑えるために、ノードは慎重に切断およびドレインされます。

詳細については、「[Azure Kubernetes Service (AKS) クラスターのアップグレード][aks-upgrade]」をご覧ください。

## <a name="http-application-routing"></a>HTTP アプリケーション ルーティング

HTTP アプリケーション ルーティング ソリューションを使用すると、AKS クラスターに展開されたアプリケーションに簡単にアクセスできます。 有効にすると、HTTP アプリケーション ルーティング ソリューションによって AKS クラスター内のイングレス コントローラーが構成されます。 アプリケーションがデプロイされると、パブリック アクセス可能な DNS 名が自動的に構成されます。

詳細については、「[HTTP アプリケーション ルーティング][aks-http-routing]」を参照してください。

## <a name="gpu-enabled-nodes"></a>GPU 対応ノード

AKS は GPU 対応ノード プールの作成をサポートしています。 Azure は現在、単一または複数の GPU に対応する VM を提供しています。 GPU 対応 VM は、コンピューティング処理やグラフィック処理の負荷が高い視覚化ワークロードを想定して設計されています。

詳細については、「[AKS での GPU の使用][aks-gpu]」を参照してください。

## <a name="development-tooling-integration"></a>開発ツールの統合

Kubernetes には、Helm、Draft、Visual Studio Code 用の Kubernetes 拡張機能など、開発および管理ツールの豊富なエコシステムがあります。 これらのツールは、Azure Kubernetes Service とシームレスに動作します。

その上、Azure Dev Spaces では、高速で反復的な Kubernetes 開発エクスペリエンスをチームに提供しています。 最小限の構成で、Azure Kubernetes Service (AKS) 内での直接的なコンテナーの実行とデバッグを繰り返し実行できます。

詳細については、「[Azure Dev Spaces][azure-dev-spaces]」を参照してください。

Azure DevOps プロジェクトでは、既存のコードおよび Git リポジトリを Azure に取り込むための簡単なソリューションを提供しています。 この DevOps プロジェクトによって、AKS (CI のビルド定義が含まれる VSTS 内のリリース パイプライン) などの Azure リソースの作成、CD のリリース定義の設定、監視用の Azure Application Insights のリソースの作成が自動的に行われます。

詳細については、[Azure DevOps プロジェクト][azure-devops]に関するページを参照してください。

## <a name="virtual-network-integration"></a>仮想ネットワークの統合

AKS クラスターは、既存の VNet にデプロイできます。 この構成では、クラスター内の各ポッドに VNet の IP アドレスが割り当てられ、クラスター内の他のポッドや VNet 内の他のノードと直接通信できます。 ポッドは、ExpressRoute やサイト間 (S2S) VPN 接続経由で、ピアリングされた VNet 内の他のサービスやオンプレミス ネットワークと接続することもできます。

詳細については、[AKS ネットワークの概要][aks-networking]に関するページを参照してください。

## <a name="private-container-registry"></a>プライベート コンテナー レジストリ

Docker イメージのプライベート ストレージ用に Azure Container Registry (ACR) を統合します。

詳細については、[Azure Container Registry (ACR)][acr-docs] に関するページを参照してください。

## <a name="storage-volume-support"></a>ストレージ ボリュームのサポート

Azure Kubernetes Service (AKS) では、永続データのためにストレージ ボリュームのマウントをサポートしています。 AKS クラスターは、Azure Files と Azure ディスクをサポートするように作成されます。

詳細については、[Azure Files][azure-files] と [Azure ディスク][azure-disk] に関するページを参照してください。

## <a name="docker-image-support"></a>Docker イメージのサポート

Azure Kubernetes Service (AKS) では、Docker イメージ形式をサポートしています。

## <a name="kubernetes-certification"></a>Kubernetes 認定

Azure Kubernetes Service (AKS) は、Kubernetes 準拠として CNCF 認定されています。

## <a name="regulatory-compliance"></a>規制に対するコンプライアンス

Azure Kubernetes Service (AKS) は、SOC と ISO/HIPAA/HITRUST に準拠しています。

## <a name="next-steps"></a>次の手順

AKS のデプロイと管理の詳細を AKS クイックスタートで確認してください。

> [!div class="nextstepaction"]
> [AKS クイックスタート][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/vsts/pipelines/actions/azure-devops-project-aks?view=vsts
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

