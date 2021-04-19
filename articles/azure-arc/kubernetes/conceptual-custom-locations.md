---
title: カスタムの場所 - Azure Arc 対応 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事では、Azure Arc 対応 Kubernetes のカスタムの場所機能の概念的な概要について説明します
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450820"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes でのカスタムの場所

Azure の場所の構造の拡張としての "*カスタムの場所*" を使用すると、テナント管理者は、Azure サービス インスタンスをデプロイするターゲットの場所として、Azure Arc 対応 Kubernetes クラスターを使用することができます。 Azure のリソースの例としては、Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale があります。

Azure の場所と同様に、カスタムの場所にアクセスできるテナント内のエンド ユーザーは、会社のプライベート コンピューティングを使用してリソースをそこにデプロイできます。

[ ![Arc プラットフォームのレイヤー](./media/conceptual-arc-platform-layers.png) ](./media/conceptual-arc-platform-layers.png#lightbox)

カスタムの場所は、Azure Arc 対応 Kubernetes クラスター、クラスター接続、クラスター拡張機能の上にある抽象化レイヤーと考えることができます。 カスタムの場所によって、他の Azure サービスがクラスターにアクセスするために必要な粒度の細かい [RoleBinding と ClusterRoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) が作成されます。 顧客が自分のクラスターにデプロイするリソースを管理するため、このような他の Azure サービスでクラスターにアクセスする必要があります。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architecture

管理者がクラスターでカスタムの場所機能を有効にすると、クラスター上に ClusterRoleBinding が作成されて、カスタムの場所リソース プロバイダー (RP) によって使用される Azure AD アプリケーションが承認されます。 承認されると、カスタムの場所 RP は、このクラスターにカスタム リソースを作成するために他の Azure RP で必要になる ClusterRoleBinding や RoleBinding を作成できます。 クラスターにインストールされているクラスター拡張機能により、承認する RP の一覧が決まります。

[ ![カスタムの場所を使用する](./media/conceptual-custom-locations-usage.png) ](./media/conceptual-custom-locations-usage.png#lightbox)

ユーザーがクラスター上にデータ サービス インスタンスを作成すると、以下のことが行われます。 
1. PUT 要求が Azure Resource Manager に送信されます。
1. PUT 要求が、Azure Arc 対応 Data Services RP に転送されます。 
1. RP により、カスタムの場所が存在する Azure Arc 対応 Kubernetes クラスターに関連付けられている `kubeconfig` ファイルがフェッチされます。 
   * カスタムの場所が、元の PUT 要求で `extendedLocation` として参照されます。 
1. Azure Arc 対応 Data Services RP により、`kubeconfig` を使用してクラスターとの通信が行われ、カスタムの場所にマップされている名前空間に、Azure Arc 対応 Data Services 型のカスタム リソースが作成されます。 
   * Azure Arc 対応 Data Services オペレーターは、カスタムの場所が存在する前に、クラスター拡張機能の作成によってデプロイされています。 
1. Azure Arc 対応 Data Services オペレーターにより、クラスター上に作成された新しいカスタム リソースが読み取られ、データ コントローラーが作成されて、クラスター上の目的の状態の実現に変換されます。 

SQL マネージド インスタンスと PostgreSQL インスタンスが作成される手順は、前述の手順と同じです。

## <a name="next-steps"></a>次の手順

* クイックスタートを利用して、[Kubernetes クラスターを Azure Arc に接続](./quickstart-connect-cluster.md)する。
* Azure Arc 対応 Kubernetes クラスターに[カスタムの場所を作成](./custom-locations.md)する。