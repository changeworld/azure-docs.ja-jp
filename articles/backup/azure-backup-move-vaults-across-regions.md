---
title: Azure Recovery Services コンテナーを別のリージョンに移動する
description: この記事では、リージョン間でリソースを移動した後もバックアップを確実に継続する方法について説明します。
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: how-to
ms.openlocfilehash: 2b3804a0a73329d7b2e4e2449fe773918791dcea
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264943"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>リージョン間での移動後に Recovery Services コンテナー内のリソースをバックアップする

Azure Resource Mover は、リージョン間でさまざまなリソースの移動をサポートします。 リージョン間でリソースを移動している間も、リソースが保護された状態を確実に維持することができます。 Azure Backup では複数のワークロードの保護がサポートされているため、新しいリージョンでも同じ保護レベルを維持するためには、いくつかの手順を行う必要がある場合があります。

そのための詳しい手順を理解するために、以降のセクションを参照してください。

>[!Note]
>Recovery Services コンテナー間でのバックアップ データの移動は、現在、Azure Backup ではサポートされていません。 新しいリージョンのリソースを保護するためには、新しいリージョン内の新しいまたは既存のコンテナーにそのリソースを登録してバックアップする必要があります。 リージョン間でリソースを移動するときには、必要に応じて、以前のリージョンにある既存の Recovery Services コンテナー内のバックアップ データを保持または削除することができます。 以前のコンテナー内のデータを保持することにした場合は、それに応じたバックアップ料金が発生します。

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>リージョン間での移動後に Azure 仮想マシンをバックアップする

Recovery Services コンテナーによって保護された Azure 仮想マシン (VM) は、別のリージョンに移動すると、以前のコンテナーにバックアップできなくなります。 以前のコンテナーへのバックアップは、**BCMV2VMNotFound** または [**ResourceNotFound**](./backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found) というエラーで失敗するようになります。 新しいリージョンで VM を保護する方法については、以降のセクションを参照してください。

### <a name="prepare-to-move-azure-vms"></a>Azure VM の移動を準備する

VM を移動する前に、以下の前提条件が満たされていることを確認してください。

1. [VM の移動に伴う前提条件](../resource-mover/tutorial-move-region-virtual-machines.md#prerequisites)を参照して、VM が移動の条件を満たしていることを確認します。
1. 既存のコンテナーのダッシュボードの [ **[バックアップ項目]** タブで VM を選択](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)し、 **[保護の停止]** を選択します。その後、データを保持するか削除するかを必要に応じて選択します。 データを保持する設定で VM のバックアップ データが停止された場合、復旧ポイントは無期限に維持され、いずれのポリシーにも従いません。 これにより、確実にいつでもバックアップ データを復元できるようになります。
   >[!Note]
   >以前のコンテナー内のデータを保持すると、バックアップ料金が発生します。 データを保持することによる課金を避けたい場合は、[データの削除オプション](./backup-azure-manage-vms.md#delete-backup-data)を使用して、保持されているバックアップ データを削除する必要があります。
1. VM がオンになっていることを確認します。 移動先のリージョンで使用できる必要がある VM のディスクはすべて、VM 内でアタッチと初期化が行われます。
1. VM に、最新の信頼されたルート証明書と、更新された証明書失効リスト (CRL) があることを確認します。 そのためには次を行います。
   - Windows VM で、最新の Windows 更新プログラムをインストールします。
   - Linux VM では、ディストリビューター ガイダンスを参照して、マシンに最新の証明書と CRL が存在するようにします。
1. VM からの送信接続を許可します。
   - URL ベースのファイアウォール プロキシを使用して送信接続を制御しようとしている場合は、[これらの URL](../resource-mover/support-matrix-move-region-azure-vm.md#url-access) へのアクセスを許可します。
   - ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御しようとしている場合は、[これらのサービス タグ ルール](../resource-mover/support-matrix-move-region-azure-vm.md#nsg-rules)を作成します。

### <a name="move-azure-vms"></a>Azure VM の移動

[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) を使用して、VM を新しいリージョンに移動します。

### <a name="protect-azure-vms-using-azure-backup"></a>Azure Backup を使用して Azure VM を保護する

新しいリージョンの新しいまたは既存の Recovery Services コンテナーで VM の保護を開始します。 以前のバックアップから復元する必要がある場合は、バックアップ データを保持するように選択してあれば、引き続き以前の Recovery Services コンテナーから復元することができます。 

以上の手順により、新しいリージョンでもリソースが確実にバックアップされるようになります。

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>リージョン間での移動後に Azure ファイル共有をバックアップする

現在、Azure Backup には、Azure Files 用の[スナップショット管理ソリューション](./backup-afs.md)が用意されています。 つまり、ファイル共有データを手動で Recovery Services コンテナーに移動することはありません。 また、スナップショットはストレージ アカウントと一緒に移動されないため、実質上、すべてのバックアップ (スナップショット) は既存のリージョンにのみ存在し、既存のコンテナーによって保護されることになります。 ただし、ストレージ アカウントをファイル共有と共にリージョン間で移動する場合や、新しいリージョンに新しいファイル共有を作成する場合は、以降のセクションを参照して、Azure Backup によってそれらを確実に保護します。

### <a name="prepare-to-move-azure-file-share"></a>Azure ファイル共有の移動を準備する

ストレージ アカウントを移動する前に、以下の前提条件が満たされていることを確認してください。

1.  [ストレージ アカウントを移動するための前提条件](../storage/common/storage-account-move.md?tabs=azure-portal#prerequisites)を確認します。 
1. リソースの移動テンプレートをエクスポートして変更します。 詳細については、[リージョン移動のためにストレージ アカウントを準備する](../storage/common/storage-account-move.md?tabs=azure-portal#prepare)ことに関するページを参照してください。

### <a name="move-azure-file-share"></a>Azure ファイル共有を移動する

ストレージ アカウントと、その中の Azure ファイル共有をリージョン間で移動するには、「[Azure Storage アカウントを別のリージョンに移動する](../storage/common/storage-account-move.md)」を参照してください。

>[!Note]
>Azure ファイル共有をリージョン間でコピーしても、そこに関連付けられているスナップショットは一緒に移動されません。 スナップショット データを新しいリージョンに移動するには、スナップショットの個々のファイルとディレクトリを、[AzCopy](../storage/common/storage-use-azcopy-files.md#copy-all-file-shares-directories-and-files-to-another-storage-account) を使用して新しいリージョンのストレージ アカウントに移動する必要があります。

### <a name="protect-azure-file-share-using-azure-backup"></a>Azure Backup を使用して Azure ファイル共有を保護する

新しいリージョン内の新しいまたは既存の Recovery Services コンテナーで、新しいストレージ アカウントにコピーされた Azure ファイル共有の保護を開始します。  

Azure ファイル共有が新しいリージョンにコピーされたら、保護を停止し、元の Azure ファイル共有のスナップショット (および対応する復旧ポイント) を必要に応じて保持または削除するように選択できます。 これは元のコンテナーのダッシュボードの [[バックアップ項目] タブ](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)で、該当するファイル共有を選択することで行うことができます。 データを保持する設定で Azure ファイル共有のバックアップ データが停止された場合、復旧ポイントは無期限に維持され、いずれのポリシーにも従いません。
   
これにより、確実にいつでも以前のコンテナーからスナップショットを復元できるようになります。 
 
## <a name="back-up-sql-serversap-hana-in-azure-vm-after-moving-across-regions"></a>リージョン間の移動後に Azure VM 内の SQL Server または SAP HANA をバックアップする

SQL または SAP HANA サーバーが実行されている VM を別のリージョンに移動した場合、それらの VM 内の SQL および SAP HANA データベースは、以前のリージョンのコンテナーにバックアップできなくなります。 新しいリージョンで、Azure VM で実行中の SQL サーバーや SAP HANA サーバーを保護するには、以降のセクションを参照してください。

### <a name="prepare-to-move-sql-serversap-hana-in-azure-vm"></a>Azure VM 内の SQL Server または SAP HANA の移動を準備する

VM で実行中の SQL Server や SAP HANA を新しいリージョンに移動する前に、以下の前提条件が満たされていることを確認してください。

1. [VM の移動に伴う前提条件](../resource-mover/tutorial-move-region-virtual-machines.md#prerequisites)を参照して、VM が移動の条件を満たしていることを確認します。 
1. 既存のコンテナーのダッシュボードの [[バックアップ項目] タブ](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)で VM を選択し、バックアップを停止する必要がある "_データベース_" を選択します。 **[保護の停止]** を選択した後、必要に応じてデータを保持または削除します。 データを保持する設定でバックアップ データが停止された場合、復旧ポイントは無期限に維持され、いずれのポリシーにも従いません。 これにより、確実にいつでもバックアップ データを復元できるようになります。
   >[!Note]
   >以前のコンテナー内のデータを保持すると、バックアップ料金が発生します。 データを保持することによる課金を避けたい場合は、[データの削除オプション](./backup-azure-manage-vms.md#delete-backup-data)を使用して、保持されているバックアップ データを削除する必要があります。
1. 移動する VM がオンになっていることを確認します。 移動先のリージョンで使用できる必要がある VM のディスクはすべて、VM 内でアタッチと初期化が行われます。
1. VM に、最新の信頼されたルート証明書と、更新された証明書失効リスト (CRL) があることを確認します。 そのためには次を行います。
   - Windows VM で、最新の Windows 更新プログラムをインストールします。
   - Linux VM では、ディストリビューター ガイダンスを参照し、マシンに最新の証明書と CRL が存在するようにします。
1. VM からの送信接続を許可します。
   - URL ベースのファイアウォール プロキシを使用して送信接続を制御しようとしている場合は、[これらの URL](../resource-mover/support-matrix-move-region-azure-vm.md#url-access) へのアクセスを許可します。
   - ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御しようとしている場合は、[これらのサービス タグ ルール](../resource-mover/support-matrix-move-region-azure-vm.md#nsg-rules)を作成します。

### <a name="move-sql-serversap-hana-in-azure-vm"></a>Azure VM 内の SQL Server または SAP HANA を移動する

[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) を使用して、VM を新しいリージョンに移動します。

### <a name="protect-sql-serversap-hana-in-azure-vm-using-azure-backup"></a>Azure Backup を使用して Azure VM 内の SQL Server または SAP HANA を保護する

新しいリージョンの新しいまたは既存の Recovery Services コンテナーで VM の保護を開始します。 以前のバックアップから復元する必要がある場合でも、引き続き以前の Recovery Services コンテナーから復元することができます。
 
以上の手順により、新しいリージョンでもリソースが確実にバックアップされるようになります。