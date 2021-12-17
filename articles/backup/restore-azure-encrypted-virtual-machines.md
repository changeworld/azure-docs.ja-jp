---
title: 暗号化された Azure VM を復元する
description: Azure Backup サービスを使用して、暗号化された Azure VM を復元する方法について説明します。
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: 95d665fb2ae2e1dc0427090842cddbecfb3c3706
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187813"
---
# <a name="restore-encrypted-azure-virtual-machines"></a>暗号化された Azure 仮想マシンを復元する

この記事では、[Azure Backup](backup-overview.md) サービスを使用して、暗号化されたディスクを含む Windows または Linux の Azure 仮想マシン (VM) を復元する方法について説明します。 詳細については、「[Azure VM バックアップの暗号化](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups)」を参照してください。


## <a name="before-you-start"></a>開始する前に

暗号化された VM の復元を開始する前に、既知の制限事項を確認する

- 同じサブスクリプション内で ADE により暗号化された VM をバックアップして復元できます。
- Azure Backup では、スタンドアロン キーを使用して暗号化された VM がサポートされます。 VM を暗号化するために使用された証明書の一部であるキーは、現在サポートされていません。
- ADE で暗号化された VM は、ファイルまたはフォルダー レベルで復旧することはできません。 ファイルとフォルダーを復元するには、VM 全体を復旧する必要があります。
- VM を復元する場合、ADE で暗号化された VM に[既存の VM を置き換える](backup-azure-arm-restore-vms.md#restore-options)オプションを使用することはできません。 このオプションは、暗号化されていないマネージド ディスクに対してのみサポートされています。


## <a name="restore-an-encrypted-vm"></a>暗号化された VM を復元する

暗号化された VM は、以下の説明のとおり、VM ディスクを復元し、仮想マシン インスタンスを作成することでのみ復元できます。 現在、**既存の VM 上の既存のディスクを置き換える** こと、**復元ポイントから VM を作成する** こと、**ファイルまたはフォルダー レベルの復元** はサポートされていません。
 
以下の手順に従って、暗号化された VM を復元します。

### <a name="step-1-restore-the-vm-disk"></a>**手順 1**: VM ディスクを復元する

1. **[復元の構成]**  >  **[新規作成]**  >  **[復元の種類]** で、 **[ディスクの復元]** を選択します。
1. **[リソース グループ]** で、復元されたディスクの既存のリソース グループを選択するか、グローバルに一意の名前を持つ新しいリソース グループを作成します。
1. **[ステージングの場所]** で、VHD のコピー先とする必要があるストレージ アカウントを指定します。 [詳細については、こちらを参照してください](backup-azure-arm-restore-vms.md#storage-accounts)。

    ![[リソース グループ] と [ステージングの場所] を選択します](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. **[復元]** を選択して復元操作をトリガーします。

仮想マシンでマネージド ディスクを使用しているときに **[仮想マシンの作成]** オプションを選択した場合、Azure Backup では、指定されたストレージ アカウントは使用されません。 ただし、 **[ディスクの復元]** と **[インスタント リストア]** の場合、ストレージ アカウントはテンプレートを格納するためにのみ使用されます。 指定したリソース グループ内にマネージド ディスクが作成されます。
仮想マシンでアンマネージド ディスクを使用した場合、それらはストレージ アカウントに対して BLOB として復元されます。

   > [!NOTE]
   > VM ディスクを復元したら、元の VM の OS ディスクを再作成せずに、復元された VM ディスクと手動でスワップできます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/blog/os-disk-swap-managed-disks/)。

### <a name="step-2-recreate-the-virtual-machine-instance"></a>**手順 2**: 仮想マシン インスタンスを再作成する 

次のいずれかのアクションを実行します。

- 復元操作の間に生成されるテンプレートを使用して VM の設定をカスタマイズし、VM のデプロイをトリガーします。 [詳細については、こちらを参照してください](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
  >[!NOTE]
   >テンプレートのデプロイ時に、ストレージ アカウント コンテナーとパブリックおよびプライベート設定を確認してください。
- PowerShell を使用して、復元されたディスクから新しい VM を作成します。 [詳細については、こちらを参照してください](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

### <a name="step-3-restore-an-encrypted-linux-vm"></a>**手順 3**: 暗号化された Linux VM を復元する

データ ディスクが開かれてマウントされるように、ADE 拡張機能を再インストールします。

## <a name="cross-region-restore-for-an-encrypted-azure-vm"></a>暗号化された Azure VM 用のリージョンをまたがる復元

Azure Backup では、暗号化された Azure VM の [Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)への、リージョンをまたがる復元がサポートされています。 暗号化された VM 用の[リージョンをまたがる復元を有効にする](backup-create-rs-vault.md#configure-cross-region-restore)方法を確認してください。

## <a name="move-an-encrypted-azure-vm"></a>暗号化された Azure VM を移動する

暗号化された VM をコンテナーまたはリソース グループ間で移動することは、バックアップされた Azure 仮想マシンを移動することと同じです。 参照:

- [Azure 仮想マシンを別の Recovery Services コンテナーに移動する手順](backup-azure-move-recovery-services-vault.md#move-an-azure-virtual-machine-to-a-different-recovery-service-vault)
- [Azure 仮想マシンを別のリソース グループまたはサブスクリプションに移動する手順](../azure-resource-manager/management/move-resource-group-and-subscription.md)


## <a name="next-steps"></a>次のステップ

問題が発生した場合は、以下の記事を確認します。

- 暗号化された Azure VM をバックアップして復元するときの[一般的なエラー](backup-azure-vms-troubleshoot.md)
- [Azure VM エージェント/バックアップの拡張機能](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)に関する問題



