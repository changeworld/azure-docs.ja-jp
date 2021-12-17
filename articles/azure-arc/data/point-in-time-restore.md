---
title: Azure Arc 対応 SQL Managed Instance のデータベースを以前の時点に復元します
description: Azure Arc 対応 SQL Managed Instance でデータベースを特定の時点に復元する方法について説明します。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 51235a67d6f00410ee2b3db753e892cf05992baa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563266"
---
#  <a name="perform-a-point-in-time-restore"></a>ポイントインタイム リストアを実行する

ポイントインタイム リストア (PITR) を使用して、データベースを、保持期間の範囲内の、過去のある時点における別のデータベースのコピーとして作成します。 この記事では、Azure Arc 対応の SQL マネージド インスタンス内でデータベースのポイントインタイム リストアを実行する方法について説明します。

ポイントインタイム リストアでは、次のようにデータベースを復元できます。

- 既存のデータベースから
- 同じ Azure Arc 対応 SQL マネージド インスタンス上の新しいデータベースへ

データベースを事前に構成された保有期間の設定内の特定の時点に復元できます。
Azure Arc 対応 SQL マネージド インスタンスの保有期間の設定は、次のようにして確認できます。

**直接** 接続モードの場合:
```
az sql mi-arc show --name <SQL instance name> --resource-group <resource-group>
#Example
az sql mi-arc show --name sqlmi --resource-group myresourcegroup
```
**間接** 接続モードの場合:
```
az sql mi-arc show --name <SQL instance name> --k8s-namespace <SQL MI namespace> --use-k8s
#Example
az sql mi-arc show --name sqlmi --k8s-namespace arc --use-k8s
```

現在、ポイントインタイム リストアでは、次のようにデータベースを復元できます。

- インスタンス上の既存のデータベースから
- 同じインスタンス上の新しいデータベースへ

## <a name="automatic-backups"></a>自動バックアップ

Azure Arc 対応 SQL マネージド インスタンスには、自動バックアップ機能が組み込まれています。 新しいデータベースを作成または復元するたびに、Azure Arc 対応 SQL マネージド インスタンスによって完全バックアップが直ちに開始され、差分バックアップとトランザクション ログ バックアップが自動的にスケジュールされます。 SQL マネージド インスタンスは、これらのバックアップをデプロイ時に指定したストレージ クラスに保存します。 

ポイントインタイム リストアを使用すると、保持期間内の特定の時点にデータベースを復旧することができます。 データベースを特定の時点に復元するには、Azure Arc 対応データ サービスによって、特定の順序でバックアップ ファイルが適用されます。 例:

1. 完全バックアップ
2. 差分バックアップ 
3. 1 つ以上のトランザクション ログ バックアップ

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="ポイントインタイム リストア":::

現在、完全バックアップは 1 週間に 1 回、差分バックアップは 12 時間に 1 回、トランザクション ログ バックアップは 5 分に 1 回行われています。

## <a name="retention-period"></a>リテンション期間

新しい Azure Arc 対応 SQL マネージド インスタンスの既定の保有期間は 7 日で、これは 0 または 1 ～ 35 日の値で調整できます。 保持期間は、SQL マネージド インスタンスのデプロイ時に `--retention-days` プロパティを指定することで設定できます。 構成された保有期間より古いバックアップ ファイルが自動的に削除されます。


## <a name="create-a-database-from-a-point-in-time-using-az"></a>az を使用して特定の時点からデータベースを作成する

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s
```

また、`--dry-run` オプションを使用すると、実際にデータベースを復元することなく、復元操作を検証することができます。 

```azurecli
az sql midb-arc restore --managed-instance <SQL managed instance> --name <source DB name> --dest-name <Name for new db> --k8s-namespace <namespace of managed instance> --time "YYYY-MM-DDTHH:MM:SSZ" --use-k8s --dry-run
#Example
az sql midb-arc restore --managed-instance sqlmi1 --name Testdb1 --dest-name mynewdb --k8s-namespace arc --time "2021-10-29T01:42:14.00Z" --use-k8s --dry-run
```


## <a name="create-a-database-from-a-point-in-time-using-azure-data-studio"></a>Azure Data Studio を使用して特定の時点からデータベースを作成する

また、次のように Azure Data Studio から特定の時点にデータベースを復元することもできます。
1. Azure Data Studio を起動する
2. [ツール](install-client-tools.md)に関するページで説明されている、必要な Arc 拡張機能があることを確認します。
3. Azure Arc データ コントローラーに接続する
4. データ コントローラー ノードを展開し、Azure Arc 対応 SQL マネージド インスタンスを右クリックして "管理" を選択します。 Azure Data Studio で SQL マネージド インスタンスのダッシュボードが起動します。
5. ダッシュボードの **[バックアップ]** タブをクリックします
6. SQL マネージド インスタンス上のデータベースの一覧と、最も早い復元から最も遅い復元の時間範囲、および **復元** を開始するためのアイコンが表示されます
7. 復元元のデータベースのアイコンをクリックします。 Azure Data Studio の右側にブレードが起動します
8. ブレードに必要な入力を指定し、 **[復元]** をクリックします

### <a name="monitor-progress"></a>進行状況の監視

復元が開始されると、完全バックアップ、差分バックアップ、およびログ バックアップの実際の復元操作を実行するタスクが kubernetes クラスター内に作成されます。 このアクティビティの進行状況は、次のように kubernetes クラスターから監視できます。

```console
kubectl get sqlmirestoretask -n <namespace>
#Example
kubectl get sqlmirestoretask -n arc
```

タスクの詳細を取得するには、タスクで `kubectl describe` を実行します。 次に例を示します。

```console
kubectl describe sqlmirestoretask <nameoftask> -n <namespace>
```

## <a name="configure-retention-period"></a>保持期間を構成する

Azure Arc 対応 SQL マネージド インスタンスの保有期間は、次のように元の設定から再構成できます。

> [!WARNING] 
> 現在の保有期間を短くすると、新しい保有期間より古い特定の時点に復元することができなくなります。 新しい保有期間内で PITR を提供するために必要がなくなったバックアップは削除されます。 現在の保有期間を長くした場合、新しい保有期間内の古い特定の時点に復元する機能はすぐには利用できません。 システムにより長いバックアップの保有が開始されたら、やがて使用できるようになります。

### <a name="change-retention-period-for-direct-connected-sql-managed-instance"></a>**直接** 接続の SQL マネージド インスタンスの保有期間を変更する

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 10
```

### <a name="change-retention-period-for-indirect-connected-sql-managed-instance"></a>**間接** 接続の SQL マネージド インスタンスの保有期間を変更する

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days <retentiondays>
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 10
```

## <a name="disable-automatic-backups"></a>自動バックアップを無効にする

Azure Arc 対応 SQL マネージド インスタンスの特定のインスタンスの自動バックアップは、以下のように `--retention-days` プロパティを 0 に設定することで無効にできます。

> [!WARNING]
> Azure Arc 対応 SQL マネージド インスタンスの自動バックアップを無効にすると、構成されている自動バックアップが削除され、特定の時点への復元を実行できなくなります。 必要に応じて、`retention-days` プロパティを変更して自動バックアップを再開始することができます。

### <a name="disable-automatic-backups-for-direct-connected-sql-managed-instance"></a>**直接** 接続の SQL マネージド インスタンスの自動バックアップを無効にする

```azurecli
az sql mi-arc edit  --name <SQLMI name> --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --custom-location dn-octbb-cl --resource-group dn-testdc --location eastus --retention-days 0
```

### <a name="disable-automatic-backups-for-indirect-connected-sql-managed-instance"></a>**間接** 接続の SQL マネージド インスタンスの自動バックアップを無効にする

```azurecli
az sql mi-arc edit  --name <SQLMI name> --k8s-namespace <namespace>  --use-k8s --retention-days 0
#Example
az sql mi-arc edit  --name sqlmi --k8s-namespace arc  --use-k8s --retention-days 0
```

## <a name="monitor-backups"></a>バックアップを監視する

バックアップは `/var/opt/mssql/backups/archived/<dbname>/<datetime>` フォルダーに格納されます。ここで、`<dbname>` はデータベースの名前です。`<datetime>` は各完全バックアップの開始時の UTC 形式のタイムスタンプになります。 完全バックアップが開始されるたびに、新しいフォルダーが作成され、そのフォルダー内に完全バックアップとそれ以降のすべての差分バックアップおよびトランザクション ログ バックアップが含められます。 最新の完全バックアップとそれ以降の差分バックアップおよびトランザクション ログ バックアップは、`/var/opt/mssql/backups/current/<dbname><datetime>` フォルダーの下に格納されます。

## <a name="limitations"></a>制限事項

Azure Arc 対応の SQL Managed Instance に対するポイントインタイム リストアには、次の制限があります。

- Azure Arc 対応 SQL Managed Instance 全体のポイントインタイム リストアは実行できません。 
- 高可用性 (プレビュー) でデプロイされた Azure Arc 対応 SQL マネージド インスタンスでは、現時点ではポイントインタイム リストアはサポートされていません。
- 同じ Azure Arc 対応 SQL マネージド インスタンスにのみ復元できます。
- 同じ名前の異なるデータベースを削除して作成しても、現時点では適切に処理されません。
- 復元操作を実行するときに ```--dry-run``` を使用して将来の日付を指定すると、エラーが発生します




## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 SQL Managed Instance の機能の詳細を確認する](managed-instance-features.md)

[データ コントローラーの作成から開始する](create-data-controller.md)

[データ コントローラーが既に作成されている場合 Azure Arc 対応 SQL Managed Instance を作成する](create-sql-managed-instance.md)
