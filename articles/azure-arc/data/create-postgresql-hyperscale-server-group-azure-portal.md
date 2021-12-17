---
title: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを Azure portal から作成する
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを Azure portal から作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 382e4e855b0a4925cfaae2f7746587a7ab2fd951
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750318"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを Azure portal から作成する

このドキュメントでは、Azure portal から Azure Arc で PostgreSQL Hyperscale サーバー グループを作成する手順について説明します。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>作業の開始
下記のトピックについて既によく理解している場合は、この段落をスキップしてください。
作成に進む前に、次のような重要なトピックを参照してください。
- [Azure Arc 対応データ サービスの概要](overview.md)に関するページ
- [接続モードと要件](connectivity.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

お客様自身で完全な環境をプロビジョニングせずに試してみることを希望する場合は、Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。


## <a name="deploy-an-arc-data-controller-configured-to-use-the-direct-connectivity-mode"></a>直接接続モードを使用するよう構成された Arc データ コントローラーをデプロイする

要件: Azure portal から操作する Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをデプロイする前に、"*直接*" 接続モードを使用するように構成された Azure Arc データ コントローラーをデプロイする必要があります。
Arc データ コントローラーをデプロイするには、こちらの記事の手順を実行します。
1. [データ コントローラーのデプロイ - 直接接続モード (前提条件)](create-data-controller-direct-prerequisites.md)
1. [Azure portal から直接接続モードで Azure Arc データ コントローラーをデプロイする](create-data-controller-direct-azure-portal.md)


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

直接接続モードに対応する Arc データ コントローラーをデプロイすると、Azure Arc 対応 Postgres Hyperscale のサーバー グループをデプロイするために、以下の 3 つのオプションのいずれかを選択できます。

### <a name="option-1-deploy-from-the-azure-marketplace"></a>オプション 1: Azure Marketplace からデプロイする
1. ブラウザーで次の URL [https://portal.azure.com](https://portal.azure.com) を開きます
2. ページ上部の [検索] ウィンドウで「*azure arc postgres*」を Azure マーケットプレースで検索して、 **[Azure Database for PostgreSQL server groups - Azure Arc]\(Azure Database for PostgreSQL サーバー グループ - Azure Arc\)** を選択します。
3. 開いたページで、左上隅にある **[+ 作成]** をクリックします。 
4. フォームに、他の Azure リソースをデプロイするように入力します。

### <a name="option-2-deploy-from-the-azure-database-for-postgresql-deployment-option-page"></a>オプション 2: Azure Database for PostgreSQL のデプロイ オプション ページからデプロイする
1. ブラウザーで次の URL https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer を開きます。
2. 右下隅のタイルをクリックします。 「Azure Arc 対応 PostgreSQL Hyperscale (プレビュー)」というタイトルになっています。
3. フォームに、他の Azure リソースをデプロイするように入力します。

### <a name="option-3-deploy-from-the-azure-arc-center"></a>オプション 3: Azure Arc センターからデプロイする
1. ブラウザーで次の URL https://ms.portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/overview を開きます
1. ページの中央から、 *[Azure サービスのデプロイ]* というタイトルのタイルの下にある [デプロイ] をクリックし、[PostgreSQL Hyperscale (プレビュー)]というタイトルのタイルの [デプロイ] をクリックします。
2. または、左側ページのナビゲーション ウィンドウの [サービス] セクションで [PostgreSQL Hyperscale (プレビュー)] をクリックし、ウィンドウ左上の [＋作成] をクリックします。


#### <a name="important-parameters-you-should-consider"></a>考慮する必要がある重要なパラメーター

- スケールアウトによるパフォーマンスの向上を目的としてデプロイしようとしている **ワーカー ノードの数**。 ここに進む前に、[Postgres Hyperscale の概念](concepts-distributed-postgres-hyperscale.md)に関するページを参照してください。 次の表は、サポートされている値の範囲と、それを使用して取得できる Postgres デプロイの形式を示しています。 たとえば、2 つのワーカー ノードを含むサーバー グループをデプロイする場合は、2 を指定します。 これで 3 つのポッドが作成されます。1 つはコーディネーター ノード/インスタンス用、2 つはワーカー ノード/インスタンス用です (各ワーカーに 1 つ)。



|以下のものが必要です。   |デプロイするサーバー グループの形状   |指定するワーカー ノードの数   |Note   |
|---|---|---|---|
|アプリケーションのスケーラビリティ ニーズを満たすための、Postgres のスケールアウト形式。   |3 つ以上の Postgres インスタンス (コーディネーターが 1 つで、ワーカーが 2 つ以上)。   |n 個 (n は 2 以上)。   |ハイパースケール機能を提供する Citus 拡張機能が読み込まれます。   |
|最小限のコストでアプリケーションの機能検証を行うための、Postgres Hyperscale の基本形式。 パフォーマンスとスケーラビリティの検証には無効です。 そのためには、上記のデプロイの種類を使用する必要があります。   |コーディネーターでもありワーカーでもある 1 つの Postgres インスタンス。   |0 個。読み込む拡張機能の一覧に Citus を追加します。   |ハイパースケール機能を提供する Citus 拡張機能が読み込まれます。   |
|必要に応じてスケールアウトできる Postgres の単純なインスタンス。   |1 つの Postgres インスタンス。 コーディネーターとワーカーのセマンティックはまだ認識されていません。 デプロイ後にスケールアウトするには、構成を編集してワーカー ノードの数を増やし、データを分散させます。   |0   |Hyperscale 機能を提供する Citus 拡張機能はデプロイに存在しますが、まだ読み込まれていません。   |
|   |   |   |   |

1 つのワーカーを指定することはできますが、これを使用することはお勧めしません。 このデプロイでは、あまり大きな価値は提供されません。 これにより、Postgres の 2 つのインスタンス (1 つのコーディネーターと 1 つのワーカー) が取得されます。 この設定では、1 つのワーカーがデプロイされるため、実際にはデータをスケールアウトできません。 そのため、パフォーマンスとスケーラビリティは向上しません。 今後のリリースでは、このデプロイのサポートは削除される予定です。

- サーバー グループで使おうとしている **ストレージ クラス**。 ストレージ クラスはデプロイ後に変更することはできないため、サーバー グループをデプロイする時点で設定することが重要です。 もしデプロイ後にストレージ クラスを変更するとすれば、データの抽出、サーバー グループの削除、新しいサーバー グループの作成、データのインポートが必要になります。 データ、ログ、バックアップ用に使用するストレージ クラスを指定することもできます。 既定では、ストレージ クラスを指定しない場合、データ コントローラーのストレージ クラスが使用されます。
    - データ用のストレージ クラスを設定するには、パラメーター `--storage-class-data` または `-scd` を指定し、その後にストレージ クラスの名前を指定します。
    - ログ用のストレージ クラスを設定するには、パラメーター `--storage-class-logs` または `-scl` を指定し、その後にストレージ クラスの名前を指定します。
    - バックアップ用のストレージ クラスを設定するには: Azure Arc 対応 PostgreSQL Hyperscale のこのプレビューでは、実行しようとしているバックアップ/復元操作の種類に応じて、ストレージ クラスを設定する方法が 2 つあります。 Microsoft は、このエクスペリエンスの簡素化に取り組んでいます。 ストレージ クラスまたはボリューム要求マウントのいずれかを指定します。 ボリューム要求マウントは、(同じ名前空間内の) 既存の永続的ボリューム要求と、コロンで区切られたボリュームの種類 (およびボリュームの種類に応じたオプションのメタデータ) のペアです。 永続的ボリュームは、PostgreSQL サーバー グループの各ポッドにマウントされます。
        - 実行する予定があるのはデータベースの完全復元だけである場合は、パラメーター `--storage-class-backups` または `-scb` を設定し、その後にストレージ クラスの名前を指定します。
        - データベースの完全復元とポイントインタイム リストアの両方を行う予定の場合は、パラメーター `--volume-claim-mounts` または `--volume-claim-mounts` を設定し、その後にボリューム要求の名前とボリュームの種類を指定します。


## <a name="next-steps"></a>次のステップ

- Azure Arc 対応 PostgreSQL Hyperscale に接続します。[接続エンドポイントと接続文字列の取得](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)に関するページを参照してください
- 複数の PostgreSQL Hyperscale ノードにわたってデータを分散させ、パフォーマンス向上の可能性があるという利点を得るため、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \*上記のドキュメントの「**Azure portal にサインインする**」と「**Azure Database for PostgreSQL - Hyperscale (Citus) を作成する**」セクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

- [Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-in-postgresql-hyperscale-server-group.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [永続ボリューム要求の拡張](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)


