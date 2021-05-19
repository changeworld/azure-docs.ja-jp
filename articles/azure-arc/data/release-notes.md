---
title: Azure Arc 対応データ サービス - リリース ノート
description: 最新のリリース ノート
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 05/04/2021
ms.topic: conceptual
ms.openlocfilehash: 58dd7baa612e2dcf302ce87b2d77cd0e71f90187
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763849"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>リリースノート - Azure Arc 対応データ サービス (プレビュー)

この記事では、Azure Arc 対応データ サービスで最近リリースされた、または強化された機能と機能強化について説明します。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="april-2021"></a>2021 年 4 月

このプレビュー リリースは、2021 年 4 月 29 日に公開されています。

### <a name="whats-new"></a>新機能

このセクションでは、このリリースで導入または有効化された新機能について説明します。 

#### <a name="platform"></a>プラットフォーム

- 直接接続クラスターによって、テレメトリ情報が自動的に Azure にアップロードされます。 

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

- Azure Arc 対応 PostgreSQL Hyperscale が直接接続モードでサポートされるようになりました。 これで、Azure portal の Azure マーケット プレースから、Azure Arc 対応 PostgreSQL Hyperscale をデプロイできるようになりました。 
- Azure Arc 対応 PostgreSQL Hyperscale には、カラム型テーブル ストレージを特長とする Citus 10.0 拡張機能が付属しています
- Azure Arc 対応 PostgreSQL Hyperscale で、完全なユーザーおよびロール管理がサポートされるようになりました。
- Azure Arc 対応 PostgreSQL Hyperscale で、`Tdigest` および `pg_partman` を含む追加の拡張機能がサポートされるようになりました。
- Azure Arc 対応 PostgreSQL Hyperscale では、サーバー グループ内の PostgreSQL インスタンスのロールごとに、仮想コアおよびメモリ設定の構成をサポートするようになりました。
- Azure Arc 対応 PostgreSQL Hyperscale では、サーバー グループ内の PostgreSQL インスタンスのロールごとに、データベース エンジンおよびサーバー設定の構成をサポートするようになりました。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

- データベースを 3 つのレプリカを持つ SQL Managed Instance に復元すると、それが可用性グループに自動的に追加されます。 
- 3 つのレプリカと共にデプロイされた SQL Managed Instance で、読み取り専用のセカンダリ エンドポイントに接続します。 読み取り専用のセカンダリ接続エンドポイントを表示するには、`azdata arc sql endpoint list` を使用します。

### <a name="known-issues"></a>既知の問題

- Azure portal を使用して、直接接続モードでデータ コントローラーを作成できます。 その他の Azure Arc 対応データ サービス ツールを使用したデプロイはサポートされていません。 具体的には、このリリースでは、次のツールのいずれかを使用して直接接続モードでデータ コントローラーをデプロイすることはできません。
   - Azure Data Studio
   - Azure Data CLI (`azdata`)
   - Kubernetes ネイティブ ツール (`kubectl`)

   「[Azure Arc データ コントローラーをデプロイする | 直接接続モード](deploy-data-controller-direct-mode.md)」では、ポータルでデータ コントローラーを作成する方法について説明しています。 

- 直接接続モードで、`azdata arc dc upload` を使用して使用状況、メトリック、およびログをアップロードすることは、現在ブロックされています。 使用状況は自動的にアップロードされます。 間接接続モードで作成されたデータ コントローラーのアップロードは引き続き機能します。
- `–proxy-cert <path-t-cert-file>` 経由でプロキシを使用する場合、直接接続モードでの使用状況データの自動アップロードは成功しません。
- Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale は GB18030 認定ではありません。
- 現時点では、kubernetes クラスターごとに直接接続モードで 1 つの Azure Arc データ コントローラーのみがサポートされています。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

- 直接モードでの Azure Arc 対応 SQL Managed Instance のデプロイは、Azure portal からのみ行うことができ、azdata、Azure Data Studio、kubectl などのツールからは使用できません。

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

- 現時点では、ポイントインタイム リストアは NFS ストレージではサポートされていません。
- `pg_cron` 拡張機能を同時に有効にし、構成することはできません。 これには、2 つのコマンドを使用する必要があります。 1 つは有効化のコマンドで、もう 1 つは構成するコマンドです。 次に例を示します。

   1. 拡張機能の有効化:
   
      ```console
      azdata arc postgres server edit -n myservergroup --extensions pg_cron 
      ```

   1. サーバー グループを再起動します。

   1. 拡張機能の構成:
   
      ```console
      azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   再起動が完了する前に 2 つ目のコマンドを実行すると、失敗します。 そのような場合は、しばらく待ってから、2 つ目のコマンドをもう一度実行してください。

- サーバー グループの構成を編集して追加の拡張機能を有効にするときに `--extensions` パラメーターに無効な値を渡すと、有効な拡張機能の一覧がサーバー グループの作成時の設定に誤ってリセットされ、ユーザーは追加の拡張機能を作成できなくなります。 そのような場合に使用できる唯一の回避策は、サーバー グループを削除して再デプロイすることです。

## <a name="march-2021"></a>2021 年 3 月

2021 年 3 月のリリースは、2021 年 4 月 5 日に初めて導入され、リリースの最終ステージは 2021 年 4 月 9 日に完了しました。

Azure Data CLI (`azdata`) バージョン番号: 20.3.2。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

### <a name="data-controller"></a>データ コントローラー

- Azure Arc 対応データ サービスのデータ コントローラーをポータルから直接接続モードでデプロイします。 「[データ コントローラーのデプロイ - 直接接続モード - 前提条件](deploy-data-controller-direct-mode-prerequisites.md)」から始めます。

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

PostgreSQL 用のカスタム リソース定義 (CRD) は、どちらも 1 つの CRD に統合されています。 次の表を参照してください。

|Release |CRD |
|-----|-----|
|2021 年 2 月以前| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|2021 年 3 月以降 | postgresqls.arcdata.microsoft.com

前のインストールをクリーンアップするときに、以前の CRD を削除します。 「[以前のインストールからのクリーンアップ](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations)」を参照してください。

### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

- Azure portal から直接接続モードで SQL マネージド インスタンスを作成できるようになりました。

- データベースを 3 つのレプリカを持つ SQL Managed Instance に復元できるようになりました。これは、可用性グループに自動的に追加されます。 

- 3 つのレプリカと共にデプロイされた SQL Managed Instance で、読み取り専用のセカンダリ エンドポイントに接続できるようになりました。 読み取り専用のセカンダリ接続エンドポイントを表示するには、`azdata arc sql endpoint list` を使用します。

## <a name="february-2021"></a>2021 年 2 月

### <a name="new-capabilities-and-features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号: 20.3.1。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

追加の更新プログラムには以下が含まれます。

- Azure Arc 対応 SQL Managed Instance
   - Always On 可用性グループによる高可用性

- Azure Arc 対応 PostgreSQL Hyperscale Azure Data Studio: 
   - 概要ページに、ノードごとに項目別にされたサーバー グループの状態が表示されます
   - 新しいプロパティ ページにサーバー グループに関する詳細が表示されます
   - **[ノード パラメーター]** ページから Postgres エンジン パラメーターを構成します

## <a name="january-2021"></a>2021 年 1 月

### <a name="new-capabilities-and-features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号:20.3.0。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

追加の更新プログラムには以下が含まれます。
- 17 の新しい言語で使用できるローカライズされたポータル
- Kube ネイティブの .yaml ファイルに対する軽微な変更
- 新しいバージョンの Grafana と Kibana
- Azure Data Studio のノートブックで azdata を使用する場合の Python 環境に関する問題が解決されました
- pg_audit 拡張機能を PostgreSQL Hyperscale で使用できるようになりました
- PostgreSQL Hyperscale データベースの完全復元を実行する場合、バックアップ ID は不要になりました
- サーバー グループを構成する PostgreSQL インスタンスごとに状態 (正常性状態) が報告されます

   以前のリリースでは、状態はサーバー グループ レベルで集計され、PostgreSQL ノード レベルでは項目別にまとめられませんでした。

- PostgreSQL のデプロイでは、create コマンドに指定されたボリューム サイズのパラメーターが受け入れられます
- サーバー グループを編集するときに、エンジンのバージョン パラメーターが受け入れられるようになりました
- Azure Arc 対応 PostgreSQL Hyperscale のポッドの名前付け規則が変更されました

    現在は `ServergroupName{c, w}-n` の形式になっています。 たとえば、3 つのノード (1 つのコーディネーター ノードと 2 つのワーカー ノード) を持つサーバー グループは次のように表されます。
   - `Postgres01c-0` (コーディネーター ノード)
   - `Postgres01w-0` (ワーカー ノード)
   - `Postgres01w-1` (ワーカー ノード)

## <a name="december-2020"></a>2020 年 12 月

### <a name="new-capabilities--features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号:20.2.5。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

Azure Data CLI (`azdata`) で `azdata arc sql endpoint list` および `azdata arc postgres endpoint list` コマンドを使用すると、SQL Managed Instance および PostgreSQL Hyperscale の各エンドポイントが表示されます。

Azure Data Studio を使用して、SQL Managed Instance リソース (CPU コアとメモリ) の要求および制限を編集できます。

Azure Arc 対応 PostgreSQL Hyperscale では、PostgreSQL のバージョン 11 と 12 の両方に対して、完全バックアップの復元に加えて、ポイントインタイム リストアがサポートされるようになりました。 ポイントインタイム リストア機能を使用すると、復元する特定の日付と時刻を指定できます。

Azure Arc 対応 PostgreSQL Hyperscale のポッドの名前付け規則が変更されました。 現在は、ServergroupName{r, s}-_n_ の形式になっています。 たとえば、3 つのノード (1 つのコーディネーター ノードと 2 つのワーカー ノード) を持つサーバー グループは次のように表されます。
- `postgres02r-0` (コーディネーター ノード)
- `postgres02s-0` (ワーカー ノード)
- `postgres02s-1` (ワーカー ノード)

### <a name="breaking-change"></a>互換性に影響する変更点

#### <a name="new-resource-provider"></a>新しいリソース プロバイダー

このリリースでは、`Microsoft.AzureArcData` という名称の更新された[リソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)が導入されました。 この機能を使用するには、事前にこのリソース プロバイダーを登録する必要があります。 

リソース プロバイダーを登録するには、次のようにします。 

1. Azure portal で、 **[サブスクリプション]** を選択します 
2. サブスクリプションの選択
3. **[設定]** で、 **[リソース プロバイダー]** を選択します 
4. `Microsoft.AzureArcData` を検索し、 **[登録]** を選択します 

詳細な手順については [「Azure リソースプロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。 また、この変更により、Azure portal にアップロードした既存のすべての Azure リソースが削除されます。 このリソース プロバイダーを使用するには、データ コントローラーを更新し、最新の `azdata` CLI を使用する必要があります。  

### <a name="platform-release-notes"></a>プラットフォームのリリース ノート

#### <a name="direct-connectivity-mode"></a>直接接続モード

このリリースでは、直接接続モードが導入されます。 直接接続モードでは、データ コントローラーによって使用状況の情報を Azure に自動的にアップロードできます。 使用状況のアップロードの一環として、Arc データ コントローラー リソースがポータルに自動的に作成されます (`azdata` アップロードによって既に作成されていない場合)。  

データ コントローラーを作成するときに、直接接続を指定できます。 次の例では、直接接続モード (`connectivity-mode direct`) を使用して `arc` という名前のデータ コントローラーを `azdata arc dc create` によって作成します。 例を実行する前に、`<subscription id>` をお使いのサブスクリプション ID に置き換えてください。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>2020 年 10 月 

Azure データ CLI (`azdata`) バージョン番号:20.2.3。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

### <a name="breaking-changes"></a>重大な変更

このリリースでは、次のような破壊的変更が導入されました。 

* PostgreSQL カスタム リソース定義 (CRD) で、用語 `shards` が `workers` に名称変更されています。 この用語 (`workers`) は、コマンド ライン パラメーター名と一致します。

* `azdata arc postgres server delete` では、postgres インスタンスを削除する前に確認を求めるメッセージが表示されます。  プロンプトをスキップするには `--force` を使用します。

### <a name="additional-changes"></a>追加の変更

* `--volume-claim mounts` という新しい省略可能パラメーターが `azdata arc postgres server create` に追加されました。 値は、ボリューム要求マウントのコンマ区切りの一覧です。 ボリューム要求マウントは、ボリュームの種類と PVC 名のペアです。 現在サポートされているボリュームの種類は `backup` のみです。  PostgreSQL では、ボリュームの種類が `backup` の場合、PVC は `/mnt/db-backups` にマウントされます。  これにより、PostgresSQL インスタンス間でバックアップを共有でき、1 つの PostgresSQL インスタンスのバックアップを別のインスタンスで復元できるようになります。

* PostgresSQL カスタム リソース定義の新しい短い名前: 
  * `pg11` 
  * `pg12`
* テレメトリのアップロードでは、次のいずれかがユーザーに提供されます。
   * Azure にアップロードされたポイントの数、または 
   * Azure にデータが読み込まれていない場合は、再試行するためのプロンプト。
* `azdata arc dc debug copy-logs` では、`/var/opt/controller/log` フォルダーからの読み取りと、Linux での PostgreSQL エンジン ログの収集も行われるようになりました。
*   PostgreSQL Hyperscale を使用してバックアップを作成および復元するときに、作業インジケーターを表示します。
* `azdata arc postrgres backup list` にバックアップ サイズの情報が含まれるようになりました。
* SQL Managed Instance 管理者名プロパティが Azure portal の概要ブレードの右側の列に追加されました。
* Azure Data Studio では、PostgreSQL Hyperscale のワーカー ノードの数、仮想コア、メモリ設定の構成がサポートされます。 
* プレビューで、Postgres バージョン 11 と 12 のバックアップ/復元がサポートされます。

## <a name="september-2020"></a>2020 年 9 月

Azure Arc 対応データ サービスは、パブリック プレビュー用にリリースされています。 Arc 対応データ サービスを使用すると、どこからでもデータ サービスを管理できます。

- SQL Managed Instance
- PostgreSQL Hyperscale

手順については「[Azure Arc 対応データ サービスとは](overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> AKS、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。

- [クライアント ツールをインストールする](install-client-tools.md)
- [Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールする必要があります)
- [Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure サービスのリソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)
