---
title: 特定の時点にデータベースを復元する
description: ポイントインタイム リストア操作を実行する方法について説明します
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: fe4d250035c58bde2ba52e71046ea2f88854ff78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743831"
---
#  <a name="perform-a-point-in-time-restore"></a>ポイントインタイム リストアを実行する


Azure Arc 対応の SQL Managed Instance には、PaaS に似た多数の機能が組み込まれています。 このような機能の 1 つとして、事前に構成された保有期間の設定内で、データベースを特定の時点に復元する機能があります。 この記事では、Azure Arc 対応の SQL マネージド インスタンス内でデータベースのポイントインタイム リストアを実行する方法について説明します。

ポイントインタイム リストアは、回復ポイントの目標 (RPO) と保有期間 (RT) の 2 つのプロパティを持つインスタンス レベルの設定です。 回復ポイントの目標設定では、トランザクション ログのバックアップを実行する頻度を決定します。 これは、データ損失が予想される時間の長さでもあります。 保有期間とは、バックアップ (完全、差分、およびトランザクション ログ) を保持する期間のことです。  

現在、ポイントインタイム リストアでは、次のようにデータベースを復元できます。

- SQL インスタンス上の既存のデータベースから
- 同じ SQL インスタンス上の新しいデータベースへ

### <a name="limitations"></a>制限事項

Azure Arc 対応の SQL Managed Instance に対するポイントインタイム リストアには、次の制限があります。

- 同じ Azure Arc 対応の SQL マネージド インスタンスにのみ復元できます
- 特定の時点への復元は、yaml ファイルを使用した場合にのみ実行できます 
- 事前に構成された保有期間を超えた古いバックアップ ファイルを手動でクリーンアップする必要があります
- データベースの名前を変更すると、新しいフォルダーで新しいバックアップ チェーンが開始されます
- 同じ名前の異なるデータベースを削除して作成しても、現時点では適切に処理されません

### <a name="edit-pitr-settings"></a>PITR 設定を編集する

##### <a name="enabledisable-automated-backups"></a>自動バックアップを有効または無効にする

ポイントインタイム リストア (PITR) サービスは、次の設定で既定で有効になっています。

- 回復ポイントの目標 (RPO) = 300 秒。 許容される値は 0、または 300 から 600 (秒単位) です。

これは、Azure Arc 対応の SQL マネージド インスタンス上のすべてのデータベースのログ バックアップが、既定では 300 秒つまり 5 分ごとに取得されることを意味します。 この値を 0 に変更してバックアップの取得を無効にするか、SQL インスタンス上のデータベースに必要な RPO 要件に応じて秒単位でより高い値を指定することができます。 

PITR サービス自体を無効にすることはできませんが、Azure Arc 対応の SQL マネージド インスタンスの特定のインスタンスの自動バックアップを無効にするか、既定の設定を変更することができます。

RPO を編集するには、プロパティ ```recoveryPointObjectiveInSeconds``` の値を次のように変更します。

```
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

これにより、既定のエディターで、Azure Arc 対応の SQL マネージド インスタンスのカスタム リソース仕様が開きます。 ```spec``` の下の ```backup``` 設定を探します。

```
backup:
  recoveryPointObjectiveInSeconds: 300
```

エディターで ```recoveryPointObjectiveInSeconds``` の値を編集し、新しい設定の変更内容を有効にするために保存します。 

> [!NOTE]
> RPO 設定を編集すると、Azure Arc 対応の SQL マネージド インスタンスを含むポッドが再起動されます。 

### <a name="restore-a-database-to-a-point-in-time"></a>特定の時点にデータベースを復元する

復元操作は、Azure Arc 対応の SQL マネージド インスタンスで実行できます。これにより、ソース データベースから保有期間内の特定の時点に復元できます。 
**(1) 次のように、エディターで yaml ファイルを作成します。**

```
apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
kind: SqlManagedInstanceRestoreTask
metadata:
  name: sql01-restore-20210707
  namespace: arc
spec:
  source:
    name: sql01
    database: db01
  restorePoint: "2021-07-01T02:00:00Z"
  destination:
    name: sql01
    database: db02
```

- name - Kubernetes の要件であるカスタム リソースごとの一意の文字列
- namespace - Azure Arc 対応の SQL マネージド インスタンスが実行されている名前空間
- source > name - Azure Arc 対応の SQL マネージド インスタンスの名前
- source > database - Azure Arc 対応の SQL マネージド インスタンス上のソース データベースの名前
- restorePoint - 復元操作の特定の時点 ("UTC" 日時)。
- destination > name - 復元先の Azure Arc 対応の SQL マネージド インスタンスの名前。 現在、同じインスタンスへの復元のみがサポートされています。
- destination > database - 復元が適用される新しいデータベースの名前

**(2) yaml ファイルを適用して、復元操作を開始するタスクを作成する**

次のようにコマンドを実行して、復元操作を開始します。

```
kubectl apply -f sql01-restore-task.yaml
```

> [!NOTE]
> カスタム リソース内のタスクの名前とファイル名を同じにする必要はありません。


**復元の状態を確認する**

- 復元タスクの状態は約 10 秒ごとに更新され、状態は [待機中] --> [復元中] --> [完了]/[失敗] の順に変化します。 
- データベースの復元中は、状態に [復元中] と反映されます。

タスクの状態は次のように取得できます。

```
kubectl get sqlmirestoretask -n arc
``` 

### <a name="monitor-your-backups"></a>バックアップを監視する

バックアップは ```/var/opt/mssql/backups/archived/<dbname>/<datetime>``` フォルダーに格納されます。ここで、```<dbname>``` はデータベースの名前です。```<datetime>``` は各完全バックアップの開始時の UTC 形式のタイムスタンプになります。 完全バックアップが開始されるたびに、新しいフォルダーが作成され、そのフォルダー内に完全バックアップとそれ以降のすべての差分バックアップおよびトランザクション ログ バックアップが含められます。 最新の完全バックアップとそれ以降の差分バックアップおよびトランザクション ログ バックアップは、```/var/opt/mssql/backups/current/<dbname><datetime>``` フォルダーの下に格納されます。


### <a name="clean-up"></a>クリーンアップ 

古いバックアップを削除して領域を作成する必要がある場合、または古いバックアップが不要になった場合は、```/var/opt/mssql/backups/archived/``` フォルダーの下にあるすべてのフォルダーを削除できます。 タイムラインの途中でフォルダーを削除すると、そのウィンドウ内の特定の時点に復元する機能に影響を与える可能性があります。 連続的なタイムラインの復元可能性を考慮して、初めに最も古いフォルダーを削除することをお勧めします。 


