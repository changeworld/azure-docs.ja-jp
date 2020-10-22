---
title: Azure Database for PostgreSQL Hyperscale サーバー グループでのバックアップと復元
description: Azure Database for PostgreSQL Hyperscale サーバー グループでのバックアップと復元
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d27537f017707e937303dd0c08a589db28aac6ef
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071440"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループでのバックアップと復元

Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの完全バックアップと復元を行うことができます。 これを行うと、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのすべてのノードにあるデータベースのセット全体が、バックアップまたは復元されます。
バックアップを作成して復元するには、サーバー グループに対してバックアップ ストレージ クラスが構成されていることを確認する必要があります。 現在は、サーバー グループの作成時にバックアップ ストレージ クラスを指定する必要があります。 サーバー グループを作成した後で、バックアップ ストレージ クラスを使用するように構成することはまだできません。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> プレビューでは、Postgres エンジンのバージョン 11 のバックアップ/復元はサポートされていません。 Postgres バージョン 12 のバックアップ/復元のみがサポートされています。

## <a name="verify-configuration"></a>構成の確認

最初に、既存のサーバー グループがバックアップ ストレージ クラスを使用するように構成されているかどうかを確認します。

サーバー グループの名前を設定した後、次のコマンドを実行します。
```console
 azdata arc postgres server show -n postgres01
```
出力のストレージ セクションを確認します。
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
そのコマンドの出力の "backups" セクションにストレージ クラスの名前が示されている場合、バックアップ ストレージ クラスを使用するようにサーバー グループが構成されているということであり、自動的にバックアップを作成し、復元する準備ができています。 "backups" セクションが表示されない場合は、サーバー グループをいったん削除し、再作成してバックアップ ストレージ クラスを構成する必要があります。 現時点では、サーバー グループを作成した後で、バックアップ ストレージ クラスを構成することはまだできません。

>[!IMPORTANT]
>サーバー グループによってバックアップ ストレージ クラスが使用されるように既に構成されている場合は、次の手順をスキップし、「手動で完全バックアップを実行する」に直接進みます。

## <a name="create-a-server-group"></a>サーバー グループの作成 

次に、バックアップと復元用に構成されたサーバー グループを作成します。

それらのバックアップを取得して復元できるようにするには、ストレージ クラスを使用して構成されたサーバーを作成する必要があります。

Kubernetes クラスターで使用可能なストレージ クラスの一覧を取得するには、次のコマンドを実行します。

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

たとえば、kubeadm に基づいて単純な環境を作成した場合は、次のようになります。
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>手動で完全バックアップを実行する


次に、手動で完全バックアップを実行します。

> [!CAUTION]
> **Azure Kubernetes Service (AKS) のみをご利用の場合:** Azure Kubernetes Service (AKS) でホストされているサーバー グループのバックアップを作成できない問題については認識しております。 その修正には既に取り組んでおります。 今後のリリースまたは更新で更新プログラムが配布されるまでは、バックアップを作成する前にサーバー グループのポッドを削除する必要があります。 サーバー グループのポッドごとに (**kubectl get pods -n \<namespace name>** を実行することでポッドをリストアップします)、**kubectl delete pod \<server group pod name> -n \<namespace name>** を実行し、ポッドを削除します。 サーバー グループに含まれていないポッドは削除しないでください。 ポッドを削除しても、データが危険にさらされることはありません。 すべてのポッドがオンラインに戻り、STATUS=RUNNING になるまで待ち、それからバックアップを作成してください。 ポッドの状態は、上記の kubectl get pods コマンドの出力で確認できます。


サーバー グループのデータ フォルダーとログ フォルダー全体の完全バックアップを行うには、次のコマンドを実行します。

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
各値の説明:
- __name__ は、バックアップの名前を示します
- __server-name__ は、サーバー グループを示します
- __no wait__ は、バックアップの完了を待たずに、ユーザーがこのコマンド ライン ウィンドウを継続して使用できるようにすることを示します

このコマンドを使用すると、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを構成するすべてのノードで、分散完全バックアップが調整されます。 つまり、コーディネーター ノードとワーカー ノードのすべてのデータがバックアップされます。

次に例を示します。
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

バックアップが完了すると、バックアップの ID、名前、状態が返されます。 次に例を示します。
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> 次のことはまだできません。
> - 自動バックアップのスケジュールを作成する
> - 実行中にバックアップの進行状況を表示する

## <a name="list-backups"></a>バックアップの一覧を表示する

復元に使用可能なバックアップの一覧を表示する。

復元に使用できるバックアップの一覧を表示するには、次のコマンドを実行します。

```console
azdata arc postgres backup list --server-name <servergroup name>
```

次に例を示します。
```console
azdata arc postgres backup list --server-name postgres01
```

次のような出力が返されます。
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

タイムスタンプには、バックアップが作成された時点が UTC で示されます。

## <a name="restore-a-backup"></a>バックアップを復元する

サーバー グループ全体のバックアップを復元するには、次のコマンドを実行します。

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

各値の説明:
- __backup-id__ は、list backup コマンドで表示するバックアップの ID です (ステップ 3 を参照)。
これを使用すると、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを構成するすべてのノードで、分散完全復元が調整されます。 つまり、コーディネーター ノードとワーカー ノードのすべてのデータが復元されます。

次に例を示します。
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

復元操作が完了すると、次のような出力がコマンド ラインに返されます。
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> 次のことはまだできません。
> - 名前を指定してバックアップを復元する
> - サーバー グループを異なる名前または異なるサーバー グループで復元する
> - 復元操作の進行状況を表示する

## <a name="delete-backups"></a>バックアップを削除する
プレビューでは、バックアップの保持を設定することはできません。 ただし、不要なバックアップは手動で削除できます。
バックアップを削除する汎用のコマンドは次のとおりです。
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
ここで、
- `--server-name` はユーザーがバックアップを削除するサーバー グループの名前です。
- `--name` は削除するバックアップの名前です。
- `-id` は削除するバックアップの ID です。

> [!NOTE]
> `--name` と `-id` は同時に指定できません。

前の段落で説明したように、list backup コマンドを実行すると、バックアップの名前と ID を取得できます。

たとえば、次のバックアップの一覧が表示されているとします。
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
そして、その先頭にあるものを削除するには、次のコマンドを実行します。
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
その時点でバックアップを一覧表示すると、次の出力が表示されます。
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

削除コマンドの詳細については、次を実行してください。
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>次のステップ
- サーバー グループの[スケール アウト (ワーカー ノードの追加)](scale-out-postgresql-hyperscale-server-group.md) について確認する
- サーバー グループの[スケールアップまたはスケール ダウン (メモリ/仮想コアの増減)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) について確認する
