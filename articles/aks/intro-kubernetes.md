---
title: "Kubernetes 向け Azure Container Service の概要"
description: "Kubernetes 向けの Azure Container Service では、Azure でコンテナー ベースのアプリケーションを簡単にデプロイして管理できます。"
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 62cd20474e7770c30353fc74dfc67086593fca7a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Azure Container Service (AKS) の概要

Azure Container Service (AKS) を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成および管理できます。 これにより、既存のスキルを使用して、または増え続けている大規模な専門家コミュニティの知恵に頼って、Microsoft Azure にコンテナーベースのアプリケーションをデプロイおよび管理できます。

AKS を使用することで、Kubernetes と Docker イメージ形式を介してアプリケーションの移植性を維持しながら、エンタープライズ レベルの Azure の機能を活用することができます。

## <a name="managed-kubernetes-in-azure"></a>Azure でのManaged Kubernetes

AKS は、責任の多くを Azure にオフロードすることで、Kubernetes クラスターの管理の複雑さと運用上のオーバーヘッドを軽減します。 ホストされた Kubernetes サービスとして、Azure は正常性監視やメンテナンスなどの重要なタスクを自動的に処理します。 さらに、料金は、マスターではなく、クラスター内のエージェント ノードに対してだけ発生します。 Managed Kubernetes サービスとして、AKS は次の機能を提供します。

> [!div class="checklist"]
> * 自動的な Kubernetes のバージョンのアップグレードと修正プログラムの適用
> * 簡単なクラスター スケーリング
> * ホストされているコントロール プレーン (マスター) の自己修復
> * コストの削減 - 実行しているエージェント プール ノードのみの課金

AKS クラスター内のノードの管理は Azure が処理するので、管理者は、クラスターのアップグレードなどの多くのタスクを手動で実行する必要がありません。 Azure がこれらの重要なメンテナンス タスクを自動的に処理するため、AKS ではクラスターへの直接的なアクセス (SSH など) は提供されていません。

## <a name="using-azure-container-service-aks"></a>Azure Container Service (AKS) の使用
AKS は、今日お客様に人気のあるオープンソースのツールとテクノロジを使用することで、コンテナー ホスト環境を提供することを目的としています。 この目的のために、標準の Kubernetes API エンドポイントを公開します。 これらの標準のエンドポイントを使用することにより、Kubernetes クラスターと通信できる任意のソフトウェアを活用できます。 たとえば、[kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)、[helm](https://helm.sh/)、[draft](https://github.com/Azure/draft) を選択できます。

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Azure Container Service (AKS) を使用した Kubernetes クラスターの作成
AKS の使用を開始するには、[Azure CLI](./kubernetes-walkthrough.md) を使用するか、ポータル (Marketplace で **Azure Container Service** を検索) 経由で、AKS クラスターをデプロイします。 Azure Resource Manager テンプレートを細かく制御する必要がある上級ユーザーの場合は、オープン ソースの [acs-engine](https://github.com/Azure/acs-engine) プロジェクトを使用して、独自のカスタム Kubernetes クラスターを構築し、これを `az` CLI 経由でデプロイできます。

### <a name="using-kubernetes"></a>Kubernetes の使用
この Kubernetes により、コンテナー化されたアプリケーションのデプロイ、スケーリング、および管理が自動化されます。 Kubernetes には、次に示す豊富な機能セットが用意されています。
* 自動ビンパッキング
* 自己復旧
* 水平スケーリング
* サービス検出と負荷分散
* 自動化されたロールアウトとロールバック
* シークレットと構成管理
* ストレージ オーケストレーション
* バッチ実行

## <a name="videos"></a>ビデオ

Azure Container Service (AKS) (Azure Friday、2017 年 10 月):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Kubernetes でのアプリケーションの開発およびデプロイに使用するツール (Azure OpenDev、2017 年 6 月):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

AKS のデプロイと管理の詳細を AKS クイックスタートで確認してください。

> [!div class="nextstepaction"]
> [AKS チュートリアル](./kubernetes-walkthrough.md)