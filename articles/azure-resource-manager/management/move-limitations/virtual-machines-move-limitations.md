---
title: 新しいサブスクリプションまたはリソース グループへ Azure VM を移動する
description: Azure Resource Manager を使用して、新しいリソース グループまたはサブスクリプションに仮想マシンを移動します。
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: df34268b7741f76621c290e9979cf24d828ddc09
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478670"
---
# <a name="move-guidance-for-virtual-machines"></a>仮想マシンの移動に関するガイダンス

この記事では、現在サポートされていないシナリオと、バックアップを利用した仮想マシンを移行する手順について説明します。

## <a name="scenarios-not-supported"></a>サポートされていないシナリオ

次のシナリオはまだサポートされていません。

* 可用性ゾーン内のマネージド ディスクを別のサブスクリプションに移動することはできません。
* Standard SKU Load Balancer または Standard SKU パブリック IP を使用した仮想マシン スケール セットを移動することはできません。
* プランが添付された Marketplace リソースから作成された仮想マシンは、リソース グループまたはサブスクリプションの間で移動できません。 現在のサブスクリプションでの仮想マシンをプロビジョニング解除し、新しいサブスクリプションに再デプロイしてください。
* 仮想ネットワーク内のすべてのリソースを移動しない場合、既存の仮想ネットワーク内の仮想マシンを新しいサブスクリプションに移動することはできません。
* 低優先度の仮想マシンと低優先度の仮想マシン スケール セットは、リソース グループまたはサブスクリプション間で移動することはできません。
* 可用性セット内の仮想マシンを個別に移動することはできません。

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup を利用した仮想マシン

Azure Backup で構成された仮想マシンを移動するには、その復元ポイントをコンテナーから削除する必要があります。

仮想マシンに対して[論理的な削除](../../../backup/backup-azure-security-feature-cloud.md)が有効になっている場合は、その復元ポイントが保持されている間は仮想マシンを移動することはできません。 [論理的な削除を無効にする](../../../backup/backup-azure-security-feature-cloud.md#disabling-soft-delete)か、復元ポイントの削除後、14 日間経過するまで待ってください。

### <a name="portal"></a>ポータル

1. バックアップ用に構成されている仮想マシンを選択します。

1. 左側のウィンドウで、 **[バックアップ]** を選択します。

1. **[バックアップの停止]** を選択する

1. **[バックアップ データの削除]** を選択します。

1. 削除が完了した後に、コンテナーと仮想マシンをターゲット サブスクリプションに移動できます。 移動した後、バックアップを続行できます。

### <a name="powershell"></a>PowerShell

* 仮想マシンの場所を探します。
* 名前付けパターン `AzureBackupRG_<location of your VM>_1` (たとえば、AzureBackupRG_westus2_1) を持つリソース グループを探します
* PowerShell の場合、`Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`コマンドレットを使用します
* 名前付けパターン `AzureBackup_<name of your VM that you're trying to move>_###########` を持つタイプ `Microsoft.Compute/restorePointCollections` のリソースを探します
* このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。

### <a name="azure-cli"></a>Azure CLI

* 仮想マシンの場所を探します。
* 名前付けパターン `AzureBackupRG_<location of your VM>_1` (たとえば、AzureBackupRG_westus2_1) を持つリソース グループを探します
* CLI の場合、`az resource list -g AzureBackupRG_<location of your VM>_1`を使用します
* 名前付けパターン `AzureBackup_<name of your VM that you're trying to move>_###########` を持つタイプ `Microsoft.Compute/restorePointCollections` のリソースを探します
* このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。

## <a name="next-steps"></a>次のステップ

* リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../move-resource-group-and-subscription.md)」を参照してください。

* バックアップのための Recovery Service コンテナーの移動については、「[Recovery Services の制限事項](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)」を参照してください。
