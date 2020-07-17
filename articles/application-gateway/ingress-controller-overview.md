---
title: Azure Application Gateway イングレス コントローラーとは
description: この記事では、Application Gateway イングレス コントローラーの概要について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335798"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Application Gateway イングレス コントローラーとは
Application Gateway イングレス コントローラー (AGIC) は Kubernetes アプリケーションであり、これにより、[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) のお客様が、Azure のネイティブ [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 ロード バランサーを活用してクラウド ソフトウェアをインターネットに公開できるようにします。 AGIC では、ホストされている Kubernetes クラスターを監視し、Application Gateway を継続的に更新して、選択されたサービスがインターネットに公開されるようにします。

イングレス コントローラーは、お客様の AKS の独自のポッドで実行されます。 AGIC では、Kubernetes リソースのサブセットに変更がないかを監視します。 AKS クラスターの状態は Application Gateway の特定の構成に変換され、[Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) に適用されます。

## <a name="benefits-of-application-gateway-ingress-controller"></a>Azure Application Gateway イングレス コントローラーの利点
AGIC を使用すると、Application Gateway イングレス コントローラーが 1 つの複数の AKS クラスターをデプロイで制御できます。 また、AGIC を使用すると、AKS クラスターの前に別のロードバランサー/パブリック IP を用意する必要がなくなり、要求が AKS クラスターに到達する前に、データパス内の複数のホップを回避できます。 Application Gateway は、プライベート IP を直接使用してポッドと通信し、NodePort および KubeProxy サービスを必要としません。 これにより、デプロイのパフォーマンスも向上します。

イングレス コントローラーは、Standard_v2 および WAF_v2 SKU でのみサポートされ、自動スケールの利点も得られます。 Application Gateway は、トラフィックの負荷の増減に反応し、それに応じてスケールでき、ご利用の AKS クラスターのリソースを消費しません。

AGIC に加えて Application Gateway を使用すると、TLS ポリシーと Web アプリケーション ファイアウォール (WAF) 機能を提供して、ご利用の AKS クラスターを保護することもできます。

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC は、サービスおよびデプロイ/ポッドと共に、Kubernetes の[イングレス リソース](https://kubernetes.io/docs/user-guide/ingress/)を使用して構成されます。 Azure のネイティブ Application Gateway L7 ロード バランサーを活用したさまざまな機能が用意されています。 以下にいくつか例を挙げます。
  - URL ルーティング
  - Cookie ベースのアフィニティ
  - TLS 終了
  - エンドツーエンド TLS
  - パブリック、プライベート、ハイブリッド Web サイトのサポート
  - Web アプリケーション ファイアウォールを統合

AGIC は、複数の名前空間を処理し、ProhibitedTargets を持つことができます。つまり、AGIC では、他の既存のバックエンドに影響を与えずに、AKS クラスター専用に Application Gateway を構成できます。 

## <a name="next-steps"></a>次の手順

- [**グリーンフィールド デプロイ**](ingress-controller-install-new.md):空のスレート インフラストラクチャに AGIC、AKS、Application Gateway をインストールする手順です。
- [**ブラウンフィールド デプロイ**](ingress-controller-install-existing.md):既存の AKS と Application Gateway に AGIC をインストールします。

