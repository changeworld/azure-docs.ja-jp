---
title: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを CLI から作成する
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを CLI から作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: c004ce9854d3a4397fed9064f90c345df5c04189
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561254"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-cli"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを CLI から作成する

このドキュメントでは、Azure Arc で PostgreSQL Hyperscale サーバー グループを作成し、それに接続する手順について説明します。

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


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift ユーザー専用の暫定的および一時的な手順
この手順を実装してから、次の手順に進んでください。 既定以外のプロジェクトで PostgreSQL Hyperscale サーバー グループを Red Hat OpenShift にデプロイするには、クラスターに対して次のコマンドを実行して、セキュリティ制約を更新する必要があります。 このコマンドでは、PostgreSQL Hyperscale サーバー グループを実行するサービス アカウントに必要な特権が付与されます。 arc-data-scc というセキュリティ コンテキスト制約 (SCC) は、Azure Arc データ コントローラーのデプロイ時に追加したものです。

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-group-name は、次の手順で作成するサーバー グループの名前です。**

OpenShift の SCC の詳細については、[OpenShift のドキュメント](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)を参照してください。 次の手順に進みます。


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する

Arc データ コントローラーで Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成するには、いくつかのパラメーターを渡す `az postgres arc-server create` コマンドを使用します。

作成時に設定できるすべてのパラメーターの詳細については、コマンドの出力を確認してください。
```azurecli
az postgres arc-server create --help
```

検討する必要がある主なパラメーターは以下のとおりです。
- デプロイしようとしている **サーバー グループの名前**。 `--name` または `-n` の後に名前を指定します。この名前は 11 文字以下にする必要があります。

- デプロイしようとしている **PostgreSQL エンジンのバージョン**: 既定ではバージョン 12 です。 バージョン 12 をデプロイするには、このパラメーターを省略するか、パラメーターとして `--engine-version 12` または `-ev 12` のいずれかを渡します。 バージョン 11 をデプロイするには、`--engine-version 11` または `-ev 11` を指定します。

- スケールアウトによるパフォーマンスの向上を目的としてデプロイしようとしている **ワーカー ノードの数**。 ここに進む前に、[Postgres Hyperscale の概念](concepts-distributed-postgres-hyperscale.md)に関するページを参照してください。 デプロイするワーカー ノードの数を指定するには、パラメーター `--workers` または `-w` を使用し、その後に整数を指定します。 次の表は、サポートされている値の範囲と、それを使用して取得できる Postgres デプロイの形式を示しています。 たとえば、2 つのワーカー ノードを含むサーバー グループをデプロイする場合は、`--workers 2` または `-w 2` を指定します。 これで 3 つのポッドが作成されます。1 つはコーディネーター ノード/インスタンス用、2 つはワーカー ノード/インスタンス用です (各ワーカーに 1 つ)。



|以下のものが必要です。   |デプロイするサーバー グループの形状   |使用する `-w` パラメーター   |Note   |
|---|---|---|---|
|アプリケーションのスケーラビリティ ニーズを満たすための、Postgres のスケールアウト形式。   |3 つ以上の Postgres インスタンス (コーディネーターが 1 つで、ワーカーが 2 つ以上)。   |`-w n` を使用 (n は 2 以上)。   |ハイパースケール機能を提供する Citus 拡張機能が読み込まれます。   |
|最小限のコストでアプリケーションの機能検証を行うための、Postgres Hyperscale の基本形式。 パフォーマンスとスケーラビリティの検証には無効です。 そのためには、上記のデプロイの種類を使用する必要があります。   |コーディネーターでもありワーカーでもある 1 つの Postgres インスタンス。   |`-w 0` を使用して、Citus 拡張機能を読み込む。 コマンド ラインからデプロイする場合は、パラメーター `-w 0` --extensions Citus を使用します。   |ハイパースケール機能を提供する Citus 拡張機能が読み込まれます。   |
|必要に応じてスケールアウトできる Postgres の単純なインスタンス。   |1 つの Postgres インスタンス。 コーディネーターとワーカーのセマンティックはまだ認識されていません。 デプロイ後にスケールアウトするには、構成を編集してワーカー ノードの数を増やし、データを分散させます。   |`-w 0` を使用するか、`-w` を指定しないでください。   |Hyperscale 機能を提供する Citus 拡張機能はデプロイに存在しますが、まだ読み込まれていません。   |
|   |   |   |   |

`-w 1` を使用しても機能しますが、使用はお勧めしません。 このデプロイでは、あまり大きな価値は提供されません。 これによって取得されるのは 2 つの Postgres インスタンスであり、1 つはコーディネーターで、もう 1 つはワーカーです。 この設定では、デプロイされるのは 1 つのワーカーのみであるため、実際にはデータはスケールアウトされません。 そのため、パフォーマンスとスケーラビリティは向上しません。 今後のリリースでは、このデプロイのサポートは削除される予定です。

- サーバー グループで使おうとしている **ストレージ クラス**。 この設定はデプロイ後に変更することはできないため、サーバー グループをデプロイする時点で設定することが重要です。 データ、ログ、バックアップ用に使用するストレージ クラスを指定することもできます。 既定では、ストレージ クラスを指定しない場合、データ コントローラーのストレージ クラスが使用されます。
    - データ用のストレージ クラスを設定するには、パラメーター `--storage-class-data` または `-scd` を指定し、その後にストレージ クラスの名前を指定します。
    - ログ用のストレージ クラスを設定するには、パラメーター `--storage-class-logs` または `-scl` を指定し、その後にストレージ クラスの名前を指定します。
    - 設計とエクスペリエンスの最終処理中にバックアップ/復元機能が一時的に削除されたため、バックアップ用のストレージ クラスの設定のサポートが一時的に削除されました。

   > [!IMPORTANT]
   > デプロイ後にストレージ クラスを変更する必要がある場合、データの抽出、サーバー グループの削除、新しいサーバー グループの作成、データのインポートを行ってください。 

作成コマンドを実行すると、既定の `postgres` 管理ユーザーのパスワードを入力するよう求められます。 そのユーザーの名前は、このプレビューでは変更できません。 作成コマンドを実行する前に `AZDATA_PASSWORD` セッション環境変数を設定すると、対話型プロンプトをスキップすることができます。

### <a name="examples"></a>例

**データ コントローラーと同じストレージ クラスを使用する 2 つのワーカー ノードが含まれる、postgres01 という名前の Postgres バージョン 12 のサーバー グループをデプロイするには、次のコマンドを実行します。**

```azurecli
az postgres arc-server create -n postgres01 --workers 2 --k8s-namespace <namespace> --use-k8s
```

> [!NOTE]  
> - 同じターミナル セッションで `AZDATA_USERNAME` および `AZDATA_PASSWORD` セッション環境変数を使用してデータ コントローラーをデプロイした場合は、PostgreSQL Hyperscale サーバー グループをデプロイするために `AZDATA_PASSWORD` の値も使用されます。 別のパスワードを使用する場合は、(1) `AZDATA_PASSWORD` の値を更新するか、(2) `AZDATA_PASSWORD` 環境変数を削除するか、(3) その値を削除すると、サーバー グループを作成するときに対話形式でパスワードの入力を求められます。
> - PostgreSQL Hyperscale サーバー グループを作成しても、リソースはすぐに Azure に登録されません。 [リソース インベントリ](upload-metrics-and-logs-to-azure-monitor.md)または[利用状況データ](view-billing-data-in-azure.md)を Azure にアップロードするプロセスの一環として、Azure でリソースが作成され、そのリソースを Azure portal で確認できるようになります。


## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Arc データ コントローラーにデプロイされている PostgreSQL Hyperscale サーバー グループを一覧表示する

Arc データ コントローラーにデプロイされている PostgreSQL Hyperscale サーバー グループを一覧表示するには、次のコマンドを実行します。

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```


```output
  {
    "name": "postgres01",
    "replicas": 1,
    "state": "Ready",
    "workers": 2
  }
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループに接続するためのエンドポイントを取得する

PostgreSQL サーバー グループのエンドポイントを表示するには、次のコマンドを実行します。

```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
次に例を示します。
```console
{
  "instances": [
    {
      "endpoints": [
        {
          "description": "PostgreSQL Instance",
          "endpoint": "postgresql://postgres:<replace with password>@123.456.78.912:5432"
        },
        {
          "description": "Log Search Dashboard",
        },
        {
          "description": "Metrics Dashboard",
          "endpoint": "https://98.765.432.11:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01"
        }
      ],
      "engine": "PostgreSql",
      "name": "postgres01"
    }
  ],
  "namespace": "arc"
}
```

PostgreSQL インスタンス エンドポイントを使用して、お気に入りのツール ([Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)、[pgcli](https://www.pgcli.com/) psql、pgAdmin など) から PostgreSQL Hyperscale サーバー グループに接続できます。これを行う場合は、コーディネーター ノード/インスタンスに接続します。これにより、分散テーブルを作成していた場合、適切なワーカー ノード/インスタンスにクエリがルーティングされます。 詳細については、[Azure Arc 対応 PostgreSQL Hyperscale の概念](concepts-distributed-postgres-hyperscale.md)に関するページを参照してください。

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Azure 仮想マシンのデプロイに関する特別な注意事項

Azure 仮想マシンを使用している場合は、エンドポイントの IP アドレスに _パブリック_ IP アドレスが表示されません。 パブリック IP アドレスを検索するには、次のコマンドを使用します。
```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```
その後、パブリック IP アドレスとポートを組み合わせて接続できます。

また、ネットワーク セキュリティ ゲートウェイ (NSG) を使用して PostgreSQL Hyperscale サーバー グループのポートを公開する必要がある場合もあります。 (NSG) を経由したトラフィックを許可するには、規則を設定します。 規則を設定するには、NSG の名前を把握しておく必要があります。 次のコマンドを使用して、NSG を特定します。

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

NSG の名前を取得したら、次のコマンドを使用してファイアウォール規則を追加できます。 この例の値は、ポート 30655 に対する NSG 規則を作成し、**任意の** ソース IP アドレスからの接続を許可します。 

> [!WARNING]
> 任意の発信元 IP アドレスからの接続を許可するようにルールを設定することはお勧めしません。 クライアントの IP アドレスや、チームまたは組織の IP アドレスが含まれる IP アドレス範囲に固有の `-source-address-prefixes` 値を指定すると、適切にロック ダウンできます。

次の `--destination-port-ranges` パラメーターの値を、上記の `az postgres arc-server list` コマンドから受け取ったポート番号に置き換えます。

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Azure Data Studio で接続する

Azure Data Studio を開き、上記の外部エンドポイントの IP アドレスとポート番号、およびインスタンスの作成時に指定したパスワードを使用してインスタンスに接続します。  PostgreSQL が *[接続の種類]* ドロップダウンに表示されない場合は、[拡張] タブで PostgreSQL を検索して、PostgreSQL 拡張機能をインストールできます。

> [!NOTE]
> 接続パネルの [詳細] ボタンをクリックして、ポート番号を入力する必要があります。

Azure VM を使用している場合は、次のコマンドを使用してアクセスできる _パブリック_ IP アドレスが必要であることを忘れないでください。

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>psql を使用して接続する

PostgreSQL Hyperscale サーバー グループにアクセスするには、前の手順で取得した PostgreSQL Hyperscale サーバー グループの外部エンドポイントを渡します。

この時点で、psql のいずれかに接続できます。

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

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
