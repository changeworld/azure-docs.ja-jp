---
title: Azure Data Studio を使用して Azure Arc 対応 PostgreSQL Hyperscale を作成する
description: Azure Data Studio を使用して Azure Arc 対応 PostgreSQL Hyperscale を作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 3e71dc89ccf94462f83ce07e69bc57df86ae0a72
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752375"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Azure Data Studio を使用して Azure Arc 対応 PostgreSQL Hyperscale を作成する

このドキュメントでは、Azure Data Studio を使用して Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをプロビジョニングする手順について説明します。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift ユーザー専用の暫定的および一時的な手順

この手順を実装してから、次の手順に進んでください。 既定以外のプロジェクトで PostgreSQL Hyperscale サーバー グループを Red Hat OpenShift にデプロイするには、クラスターに対して次のコマンドを実行して、セキュリティ制約を更新する必要があります。 このコマンドでは、PostgreSQL Hyperscale サーバー グループを実行するサービス アカウントに必要な特権が付与されます。 **_arc-data-scc_** というセキュリティ コンテキスト制約 (SCC) は、Azure Arc データ コントローラーのデプロイ時に追加したものです。

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-group-name** は、次の手順でデプロイするサーバー グループの名前です。_
   
OpenShift の SCC の詳細については、[OpenShift のドキュメント](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)を参照してください。
この時点で、次の手順を実装できます。

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する

1. Azure Data Studio を起動する
1. [接続] タブで、左上にある 3 つのドットをクリックし、[新しい展開] を選択します
1. デプロイ オプションから、 **[PostgreSQL Hyperscale サーバー グループ - Azure Arc]** を選択します
    >[!NOTE]
    > [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] が現在インストールされていない場合は、ここでインストールするように求められることがあります。
1. プライバシーとライセンス条項に同意し、下部にある **[選択]** をクリックします
1. [Deploy PostgreSQL Hyperscale server group - Azure Arc]\(PostgreSQL Hyperscale サーバー グループをデプロイする - Azure Arc\) ブレードで、次の情報を入力します。
   - サーバー グループの名前を入力します
   - ワーカー ノードの数
   - サーバー グループの _postgres_ 管理者ユーザーのパスワードを入力して確認します
   - データに適したストレージ クラスを選択します
   - ログに適したストレージ クラスを選択します
   - バックアップに適したストレージ クラスを選択します
   - プロビジョニングするワーカー ノードの数を選択します
1. **[デプロイ]** ボタンをクリックします

これにより、データ コントローラーで Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの作成が開始されます。

作成が正常に完了するまでに数分かかる場合があります。

### <a name="important-parameters-you-should-consider"></a>考慮する必要がある重要なパラメーター

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
- [Azure Data Studio を使用してサーバー グループを管理する](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [サーバー グループを監視する](monitor-grafana-kibana.md)
- 複数の PostgreSQL Hyperscale ノードにデータを分散し、Azure Database for Postgres Hyperscale のすべての利点を活用するために、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。 :
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \*上記のドキュメントの「**Azure portal にサインインする**」と「**Azure Database for PostgreSQL - Hyperscale (Citus) を作成する**」セクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

- [Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-in-postgresql-hyperscale-server-group.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

