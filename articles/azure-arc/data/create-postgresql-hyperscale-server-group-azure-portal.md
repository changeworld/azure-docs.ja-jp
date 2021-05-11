---
title: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを Azure portal から作成する
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを Azure portal から作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 04/28/2021
ms.topic: how-to
ms.openlocfilehash: 5cc99acf303b99e138edd0418d2d49824bbf27fe
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294052"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを Azure portal から作成する

このドキュメントでは、Azure portal から Azure Arc で PostgreSQL Hyperscale サーバー グループを作成する手順について説明します。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>作業の開始
下記のトピックについて既によく理解している場合は、この段落をスキップしてください。
作成に進む前に、次のような重要なトピックを参照してください。
- [Azure Arc 対応データ サービスの概要](overview.md)
- [接続モードと要件](connectivity.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

お客様自身で完全な環境をプロビジョニングせずに試してみることを希望する場合は、Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。


## <a name="deploy-an-arc-data-controller-configured-to-use-the-direct-connectivity-mode"></a>直接接続モードを使用するよう構成された Arc データ コントローラーをデプロイする

要件: Azure portal から操作する Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをデプロイする前に、"*直接*" 接続モードを使用するように構成された Azure Arc データ コントローラーをデプロイする必要があります。
Arc データ コントローラーをデプロイするには、こちらの記事の手順を実行します。
1. [データ コントローラーのデプロイ - 直接接続モード (前提条件)](deploy-data-controller-direct-mode-prerequisites.md)
1. [Azure Arc データ コントローラーをデプロイする | 直接接続モード](deploy-data-controller-direct-mode.md)


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift ユーザー専用の暫定的および一時的な手順
この手順を実装してから、次の手順に進んでください。 既定以外のプロジェクトで PostgreSQL Hyperscale サーバー グループを Red Hat OpenShift にデプロイするには、クラスターに対して次のコマンドを実行して、セキュリティ制約を更新する必要があります。 このコマンドでは、PostgreSQL Hyperscale サーバー グループを実行するサービス アカウントに必要な特権が付与されます。 arc-data-scc というセキュリティ コンテキスト制約 (SCC) は、Azure Arc データ コントローラーのデプロイ時に追加したものです。

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-group-name は、次の手順で作成するサーバー グループの名前です。**

OpenShift の SCC の詳細については、[OpenShift のドキュメント](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)を参照してください。 

次の手順に進みます。

## <a name="deploy-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを Azure portal からデプロイする

Azure Arc 対応 Postgres Hyperscale サーバー グループを Azure portal からデプロイして操作するには、それを、"*直接*" 接続モードを使用するよう構成された Arc データ コントローラーにデプロイする必要があります。 

> [!IMPORTANT]
> Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを "*間接*" 接続モードを使用するよう構成された Azure Arc データ コントローラーにデプロイした場合は、それを Azure portal から操作することはできません。 

直接接続モードが有効になっている Arc データ コントローラーをデプロイした後に、以下を行います。
1. ブラウザーを開いて、次の URL に移動します: [https://portal.azure.com](https://portal.azure.com)
2. ページ上部の [検索] ウィンドウで「*azure arc postgres*」を Azure マーケットプレースで検索して、 **[Azure Database for PostgreSQL server groups - Azure Arc]\(Azure Database for PostgreSQL サーバー グループ - Azure Arc\)** を選択します。
3. 開いたページで、左上隅にある **[+ 作成]** をクリックします。 
4. フォームに、他の Azure リソースをデプロイするように入力します。


### <a name="important-parameters-you-should-consider-are"></a>考慮する必要がある重要なパラメーターを以下に示します。

- スケールアウトによるパフォーマンスの向上を目的としてデプロイしようとしている **ワーカー ノードの数**。 先へ進む前に、[Postgres Hyperscale の概念](concepts-distributed-postgres-hyperscale.md)に関するページを参照してください。 たとえば、デプロイするサーバー グループに 2 つのワーカー ノードがある場合、デプロイによって 3 つのポッドが作成されます。1 つがコーディネーター ノードまたはインスタンス用で、2 つがワーカー ノードまたはインスタンス用 (各ワーカーに 1 つ) です。

## <a name="next-steps"></a>次のステップ

- Azure Arc 対応 PostgreSQL Hyperscale に接続します。[接続エンドポイントと接続文字列の取得](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)に関する記事を参照してください。
- 複数の PostgreSQL Hyperscale ノードにわたってデータを分散させ、パフォーマンス向上の可能性があるという利点を得るため、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \*上記のドキュメントの「**Azure portal にサインインする**」と「**Azure Database for PostgreSQL - Hyperscale (Citus) を作成する**」セクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

- [Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-postgresql-hyperscale-server-group.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [永続ボリューム要求の拡張](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)


