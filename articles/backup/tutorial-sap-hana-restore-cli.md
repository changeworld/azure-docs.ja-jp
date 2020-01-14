---
title: チュートリアル - CLI を使用した Azure 上の SAP HANA DB の復元
description: このチュートリアルでは、Azure CLI を使用して、Azure VM 上で実行されている SAP HANA データベースを Azure Backup Recovery Services コンテナーから復元する方法について説明します。
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470407"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>チュートリアル:Azure CLI を使用して Azure VM 内の SAP HANA データベースを復元する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このドキュメントでは、Azure CLI を使用して、バックアップされた SAP HANA データベースを Azure VM に復元する方法について詳しく説明します。 これらの手順は、[Azure portal](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) を使用して実行することもできます。

CLI コマンドを実行するには、[Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) を使用します。

このチュートリアルを終了すると、次のことができるようになります。

> [!div class="checklist"]
>
> * バックアップされたデータベースの復元ポイントを表示する
> * データベースを復元する

このチュートリアルでは、Azure Backup を使用してバックアップされている SAP HANA データベースが Azure VM 上で実行されていることを前提としています。 [CLI を使用して Azure の SAP HANA データベースをバックアップする方法](tutorial-sap-hana-backup-cli.md)を使用して SAP HANA データベースをバックアップした場合は、次のリソースを使用しています。

* *saphanaResourceGroup* という名前のリソース グループ
* *saphanaVault* という名前のコンテナー
* *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM* という名前の保護されたコンテナー
* *saphanadatabase;hxe;hxe* という名前のバックアップされたデータベースと項目
* *westus2* リージョン内のリソース

## <a name="view-restore-points-for-a-backed-up-database"></a>バックアップされたデータベースの復元ポイントを表示する

データベースのすべての復旧ポイントの一覧を表示するには、次のように [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) コマンドレットを使用します。

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

復旧ポイントの一覧は次のようになります。

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

ご覧のとおり、上記の一覧には、完全バックアップ、差分バックアップ、ログ バックアップという 3 つの復旧ポイントが含まれています。

>[!NOTE]
>[az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) コマンドレットを使用して、すべての連続したログ バックアップ チェーンの開始ポイントと終了ポイントを表示することもできます。

## <a name="prerequisites-to-restore-a-database"></a>データベースを復元するための前提条件

データベースを復元する前に、次の前提条件が満たされていることを確認してください。

* データベースの復元先にできるのは、同じリージョン内の SAP HANA インスタンスのみです。
* ターゲット インスタンスは、ソースと同じコンテナーに登録されている必要があります。
* Azure Backup は、同じ VM 上にある 2 つの異なる SAP HANA インスタンスを識別できません。 そのため、同じ VM 上の一方のインスタンスから他方のインスタンスにデータを復元することはできません。

## <a name="restore-a-database"></a>データベースを復元する

Azure Backup は、Azure VM 上で実行されている SAP HANA データベースを次のように復元できます。

* ログ バックアップを使用して、特定の日付または時刻 (秒) に復元する。 選択された時刻を基に、Azure Backup は完全、差分、または一連のログ バックアップから、必要になる適切なバックアップを自動的に判別します。
* 特定の完全バックアップまたは差分バックアップに復元することで、特定の復旧ポイントに復元する。

データベースを復元するには、[az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) コマンドレットを使用します。これには、入力の 1 つとして復旧構成オブジェクトが必要です。 このオブジェクトは、[az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) コマンドレットを使用して生成できます。 復旧構成オブジェクトには、復元を実行するためのすべての詳細が含まれています。 いずれかの復元モード (**OriginalWorkloadRestore** または **AlternateWorkloadRestore**)。

>[!NOTE]
> **OriginalWorkloadRestore** - 元のソースと同じ SAP HANA インスタンスにデータを復元します。 このオプションでは、元のデータベースが上書きされます。 <br>
> **AlternateWorkloadRestore** - データベースを別の場所に復元し、元のソース データベースを保持します。

## <a name="restore-to-alternate-location"></a>別の場所に復元する

別の場所にデータベースを復元するには、復元モードとして **AlternateWorkloadRestore** を使用します。 次に、復元ポイントを選択する必要があります。以前の特定の時点または以前の復元ポイントのいずれかを選択できます。

このチュートリアルでは、以前の復元ポイントに復元します。 データベースの[復元ポイントの一覧を表示](#view-restore-points-for-a-backed-up-database)して、復元先のポイントを選択します。 このチュートリアルでは、*7660777527047692711* という名前の復元ポイントを使用します。

上記の復元ポイント名と復元モードを使用し、[az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) コマンドレットを使用して復旧構成オブジェクトを作成します。 このコマンドレットの残りの各パラメーターの意味を見てみましょう。

* **--target-item-name** これは、復元されたデータベースが使用する名前です。 この例では、*restored_database* という名前を使用しました。
* **--target-server-name** これは、Recovery Services コンテナーに正常に登録され、復元対象のデータベースと同じリージョンにある SAP HANA サーバーの名前です。 このチュートリアルでは、*hxehost* という名前の、保護したものと同じ SAP HANA サーバーにデータベースを復元します。
* **--target-server-type** SAP HANA データベースを復元するには、**SapHanaDatabase** を使用する必要があります。

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

上記のクエリに対する応答は、次のような復旧構成オブジェクトになります。

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

ここで、データベースを復元するには、 [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) コマンドレットを実行します。 このコマンドを使用するには、*recoveryconfig.json*という名前のファイルに保存されている上記の json 出力を入力します。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

出力は次のようになります。

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

応答によってジョブ名がわかります。 このジョブ名を使用し、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用してジョブの状態を追跡できます。

## <a name="restore-and-overwrite"></a>復元および上書き

元の場所に復元するには、復元モードとして **OrignialWorkloadRestore** を使用します。 次に、復元ポイントを選択する必要があります。以前の特定の時点または以前の復元ポイントのいずれかを選択できます。

このチュートリアルでは、復元先として以前の特定の時点の "28-11-2019-09:53:00" を選択します。 この復元ポイントは、dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss の形式で指定できます。 復元先として有効な特定の時点を選択するには、[az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) コマンドレットを使用します。これにより、連続したログ チェーン バックアップの間隔が一覧表示されます。

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

上記のクエリに対する応答は、次のような復旧構成オブジェクトになります。

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

ここで、データベースを復元するには、 [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) コマンドレットを実行します。 このコマンドを使用するには、*recoveryconfig.json*という名前のファイルに保存されている上記の json 出力を入力します。

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

出力は次のようになります。

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

応答によってジョブ名がわかります。 このジョブ名を使用し、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用してジョブの状態を追跡できます。

## <a name="next-steps"></a>次のステップ

* Azure CLI を使用してバックアップされる SAP HANA データベースを管理する方法については、[CLI を使用して Azure VM で SAP HANA データベースを管理する方法](tutorial-sap-hana-backup-cli.md)のチュートリアルに進んでください。

* Azure portal を使用して Azure VM 上で実行されている SAP HANA データベースを復元する方法については、「[Azure VM 上の SAP HANA データベースの復元](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)」を参照してください。
