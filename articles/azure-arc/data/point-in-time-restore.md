---
title: Azure Arc 対応 SQL Managed Instance のデータベースを以前の時点に復元します
description: Azure Arc 対応 SQL Managed Instance でデータベースを特定の時点に復元する方法について説明します。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d091e9c90217ff9df6fba45d308296a80970053
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401102"
---
#  <a name="perform-a-point-in-time-restore"></a>ポイントインタイム リストアを実行する

ポイントインタイム リストア (PITR) を使用して、データベースを、過去のある時点における別のデータベースのコピーとして作成します。 この記事では、Azure Arc 対応 SQL マネージド インスタンス内でデータベースのポイントインタイム リストアを実行する方法について説明します。

ポイントインタイム リストアでは、次のようにデータベースを復元できます。

- 既存のデータベースから
- 同じ Azure Arc 対応 SQL マネージド インスタンス上の新しいデータベースへ

データベースを事前に構成された保有期間の設定内の特定の時点に復元できます。

ポイントインタイム リストアは、回復ポイントの目標 (RPO) と保有期間の 2 つのプロパティを持つインスタンス レベルの設定です。 RPO では、トランザクション ログのバックアップを実行する頻度を決定します。 RPO は、データ損失が予想される時間の長さです。 RPO は分単位で指定します。 保有期間によって、Azure Arc 対応データ サービスによってデータベースのバックアップを保存する期間 (日数) が決定されます。 保有期間は、完全、差分、およびトランザクション ログのバックアップ ファイルに適用されます。  

現在、ポイントインタイム リストアでは、次のようにデータベースを復元できます。

- インスタンス上の既存のデータベースから
- 同じインスタンス上の新しいデータベースへ

## <a name="limitations"></a>制限事項

Azure Arc 対応 SQL Managed Instance に対するポイントインタイム リストアには、次の制限があります。

- Azure Arc 対応 SQL Managed Instance 全体のポイントインタイム リストアは実行できません。 
- 高可用性 (プレビュー) でデプロイされた Azure Arc 対応 SQL マネージド インスタンスでは、現時点ではポイントインタイム リストアはサポートされていません。
- 同じ Azure Arc 対応 SQL マネージド インスタンスにのみ復元できます。
- 特定の時点への復元は、yaml ファイルを使用した場合にのみ実行できます。
- 事前に構成された保有期間を超えた古いバックアップ ファイルを手動でクリーンアップする必要があります。
- データベースの名前を変更すると、新しいフォルダーで新しいバックアップ チェーンが開始されます。
- 同じ名前の異なるデータベースを削除して作成しても、現時点では適切に処理されません。

## <a name="description"></a>説明

ポイントインタイム リストアでは、データベースが特定の時点に復元されます。 データベースを特定の時点に復元するには、Azure Arc 対応データ サービスによって、特定の順序でバックアップ ファイルが適用されます。 例:

1. 完全バックアップ
2. 差分バックアップ 
3. 1 つ以上のトランザクション ログ バックアップ

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="ポイントインタイム リストア":::

Azure Arc 対応 SQL Managed Instance には、ポイントインタイム リストアを行うための組み込みの機能が付属しています。 Azure Arc 対応 SQL Managed Instance に新しいデータベースが作成または復元されるたびに、バックアップが自動的に実行されます。 

ポイントインタイム リストア機能に影響を与える 2 つのパラメーターがあります。

- 目標復旧時点 
- 保持期間

## <a name="create-a-database-from-a-point-in-time"></a>特定の時点からデータベースを作成する

次に、`kubectl` を使用して、データベースを同じ Azure Arc 対応 SQL Managed Instance に復元する手順を示します。

1. 復元操作のタスクを作成します。 復元パラメーターを使用して、yaml ファイルを作成します。

   例:

   ```json
   apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
      kind: SqlManagedInstanceRestoreTask
   metadata:
     name: sql01-restore-20210909
   namespace: arc
   spec:
     source:
       name: sql01
       database: db01
     restorePoint: "2021-09-09T02:00:00Z"
     destination:
       name: sql01
       database: db02
   ```

   上記の yaml ファイルを編集します。

   - `metadata` > `name` -タスクのカスタム リソース (CR) の名前
   - `metadata` > `namespace` - Azure Arc 対応 SQL Managed Instance の名前空間
   - `source` > `name` - Azure Arc 対応 SQL Managed Instance の名前
   - `source` > `database` - 復元元の Azure Arc 対応 SQL Managed Instance 上の "**復元元**" データベースの名前
   - `restorePoint` - 復元する特定の時点 ("UTC" の日付と時刻)。
   - `destination` > `name` - Azure Arc 対応 SQL Managed Instance の名前
   - `destination` > `database` - Azure Arc 対応 SQL Managed Instance 上の "**復元先**"データベースの名前


   > [!NOTE] 
   > 復元元と復元先の Azure Arc 対応 SQL マネージド インスタンスの名前は同じである必要があります。

2. ポイントインタイム リストア操作を開始するタスクを作成する

   ```console
   kubectl apply -f sql-restore-task.yaml
   ```

3. 復元の状態を確認します

   復元操作の状態を確認するには、次のコマンドを実行します。

   ```console
   kubectl get sqlmirestoretask -n <namespace>
   ```

   `<namespace>` を SQL インスタンスをホストする名前空間と置き換えます。

復元タスクの状態が **[完了]** になると、新しいデータベースを使用できるようになります。 

## <a name="troubleshoot-failed-restore-operations"></a>失敗した復元操作のトラブルシューティング

復元タスクの状態が **[失敗]** になっている場合は、次のコマンドを実行して、イベントの根本原因を探します。

```console
kubectl describe sqlmirestoretask <taskname> -n <namespace>
```

例:
```console
kubectl describe sqlmirestoretask sql01-restore-20210909 -n arc
```

## <a name="enabledisable-automated-backups"></a>自動バックアップを有効または無効にする

ポイントインタイム リストア (PITR) サービスは、次の設定で既定で有効になっています。

- 回復ポイントの目標 (RPO) = 300 秒。 許容される値は 0、または 300 から 600 (秒単位) です。

RPO は、Azure Arc 対応 SQL マネージド インスタンス上のすべてのデータベースのログ バックアップが、既定で 300 秒つまり 5 分ごとに取得されるようにサービスを設定します。 この値を 0 に変更してバックアップの取得を無効にするか、SQL インスタンス上のデータベースに必要な RPO 要件に応じて秒単位でより高い値を指定することができます。 

Azure Arc 対応 SQL マネージド インスタンスの特定のインスタンスの自動バックアップを無効にすることも、既定の設定を変更することもできます。 PITR サービス自体を無効にすることはできません。

RPO は、`recoveryPointObjectiveInSeconds` プロパティの値を次のように変更することで編集できます。

```console
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

このコマンドにより、既定のエディターで、Azure Arc 対応 SQL マネージド インスタンスのカスタム リソース仕様が開きます。 `spec` の下の `backup` 設定を探します。

```json
backup:
  recoveryPointObjectiveInSeconds: 300
```

エディターで `recoveryPointObjectiveInSeconds` の値を編集し、新しい設定の変更内容を有効にするために保存します。 

> [!NOTE]
> RPO 設定を編集すると、Azure Arc 対応の SQL マネージド インスタンスを含むポッドが再起動されます。 

## <a name="monitor-backups"></a>バックアップを監視する

バックアップは `/var/opt/mssql/backups/archived/<dbname>/<datetime>` フォルダーに格納されます。ここで、`<dbname>` はデータベースの名前です。`<datetime>` は各完全バックアップの開始時の UTC 形式のタイムスタンプになります。 完全バックアップが開始されるたびに、新しいフォルダーが作成され、そのフォルダー内に完全バックアップとそれ以降のすべての差分バックアップおよびトランザクション ログ バックアップが含められます。 最新の完全バックアップとそれ以降の差分バックアップおよびトランザクション ログ バックアップは、`/var/opt/mssql/backups/current/<dbname><datetime>` フォルダーの下に格納されます。

### <a name="clean-up"></a>クリーンアップ 

古いバックアップを削除して領域を作成する必要がある場合、または古いバックアップが不要になった場合は、`/var/opt/mssql/backups/archived/` フォルダーの下にあるすべてのフォルダーを削除できます。 タイムラインの途中でフォルダーを削除すると、そのウィンドウ内の特定の時点に復元する機能に影響を与える可能性があります。 連続的なタイムラインの復元可能性を考慮して、最初に最も古いフォルダーを削除してください。 

## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 SQL Managed Instance の機能の詳細を確認する](managed-instance-features.md)

[データ コントローラーの作成から開始する](create-data-controller.md)

[データ コントローラーが既に作成されている場合: Azure Arc 対応 SQL Managed Instance を作成する](create-sql-managed-instance.md)
