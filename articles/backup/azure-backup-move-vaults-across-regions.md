---
title: Azure Recovery Services コンテナーを別のリージョンに移動する
description: この記事では、リージョン間でリソースを移動した後もバックアップを確実に継続する方法について説明します。
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: faf6ad49234d9753a0479151bf931714a1eba226
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114145"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>リージョン間での移動後に Recovery Services コンテナー内のリソースをバックアップする

Azure Resource Mover は、リージョン間でさまざまなリソースの移動をサポートします。 リージョン間でリソースを移動している間も、リソースが保護された状態を維持することができます。 Azure Backup は、さまざまなワークロードの保護に対応しているため、新しいリージョンでも同じ保護レベルを維持するためには、いくつかの手順が必要となる場合があります。

そのための詳しい手順を理解するために、以降のセクションを参照してください。

>[!Note]
>Recovery Services コンテナー間でのバックアップ データの移動は、現在、Azure Backup ではサポートされていません。 新しいリージョンのリソースを保護するためには、新しいリージョン内の新しいコンテナーまたは既存のコンテナーにそのリソースを登録してバックアップする必要があります。 リージョン間でリソースを移動しているとき、以前のリージョンにある既存の Recovery Services コンテナー内のバックアップ データは、必要に応じて保持または削除することができます。 以前のコンテナー内のデータを保持する場合、それに応じたバックアップ料金が発生します。

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>リージョン間での移動後に Azure 仮想マシンをバックアップする

Recovery Services コンテナーによって保護されている Azure 仮想マシン (VM) は、別のリージョンに移動すると、以前のコンテナーにはバックアップされなくなります。 以前のコンテナーへのバックアップは、**BCMV2VMNotFound** または [**ResourceNotFound**](/azure/backup/backup-azure-vms-troubleshoot#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found) というエラーで失敗するようになります。

新しいリージョン内の VM を保護するには、これらの手順に従う必要があります。

1. VM を移動する前に、既存のコンテナーのダッシュボードの [ **[バックアップ項目]** タブでその VM を選択](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud)し、 **[保護の停止]** を選択します。その後、データを保持するか削除するかを必要に応じて選択します。 データを保持する設定で VM のバックアップ データが停止された場合、復旧ポイントは無期限に維持され、どのようなポリシーも適用されません。 これによって、確実にいつでもバックアップ データを復元することができます。

   >[!Note]
   >以前のコンテナー内のデータを保持すると、バックアップ料金が発生します。 データを残すことによる課金を避けたい場合は、[データの削除オプション](/azure/backup/backup-azure-manage-vms#delete-backup-data)を使用して、保持されているバックアップ データを削除する必要があります。

1. [Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines) を使用して VM を新しいリージョンに移動します。

1. 新しいリージョンにある新しい Recovery Services コンテナーまたは既存の Recovery Services コンテナーで、VM の保護を開始します。
   以前のバックアップから復元する必要が生じた場合は、バックアップ データを保持するように選択してあれば、引き続き以前の Recovery Services コンテナーから復元することができます。 

以上の手順により、新しいリージョンでもリソースが確実にバックアップされるようになります。

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>リージョン間での移動後に Azure ファイル共有をバックアップする

ストレージ アカウントとそこに含まれるファイル共有をリージョン間で移動する方法については、「[Azure ストレージ アカウントを別のリージョンに移動する](/azure/storage/common/storage-account-move)」を参照してください。

>[!Note]
>Azure ファイル共有をリージョン間でコピーしても、そこに関連付けられているスナップショットは一緒には移動されません。 スナップショット データを新しいリージョンに移動するには、スナップショットの個々のファイルとディレクトリを、[AzCopy](/azure/storage/common/storage-use-azcopy-files#copy-all-file-shares-directories-and-files-to-another-storage-account) を使用して新しいリージョンのストレージ アカウントに移動する必要があります。

現在、Azure Backup には、Azure Files 用の[スナップショット管理ソリューション](/azure/backup/backup-afs#discover-file-shares-and-configure-backup)が用意されています。 つまり、ファイル共有データを手動で Recovery Services コンテナーに移動することはしません。 また、スナップショットはストレージ アカウントと一緒には移動しないため、実質的に、すべてのバックアップ (スナップショット) は既存のリージョンにのみ存在し、また、既存のコンテナーによって保護されることになります。 ただし、これらの手順に従うことによって、新しいリージョンに作成した新しいファイル共有を Azure Backup で確実に保護することができます。

1. 新しいリージョン内にある新しい Recovery Services コンテナーまたは既存の Recovery Services コンテナーで、新しいストレージ アカウントにコピーされた Azure ファイル共有の保護を開始します。  

1. Azure ファイル共有が新しいリージョンにコピーされたら、保護を停止し、元の Azure ファイル共有のスナップショット (対応する復旧ポイントを含む) を必要に応じて保持または削除することができます。 これは元のコンテナーのダッシュボードの [[バックアップ項目]](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) タブで、該当するファイル共有を選択することによって行えます。 データを保持する設定で Azure ファイル共有のバックアップ データが停止された場合、復旧ポイントは無期限に維持され、どのようなポリシーも適用されません。
   
   これにより、以前のコンテナーからいつでもスナップショットを復元できるよう備えることができます。 
 
## <a name="back-up-sql-server-in-azure-vmsap-hana-in-azure-vm"></a>Azure VM 内の SQL Server または Azure VM 内の SAP HANA をバックアップする

SQL Server または SAP HANA サーバーが実行されている VM を別のリージョンに移動した場合、それらの VM 内の SQL データベースと SAP HANA データベースは以後、以前のリージョンのコンテナーにはバックアップできなくなります。 Azure VM で実行されている SQL Server または SAP HANA サーバーを新しいリージョンで保護するには、これらの手順に従う必要があります。
 
1. SQL Server または SAP HANA が実行されている VM を新しいリージョンに移動する前に、既存のコンテナーのダッシュボードの [[バックアップ項目] タブ](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud)でその VM を選択し、バックアップを停止すべき "_データベース_" を選択します。 **[保護の停止]** を選択した後、データを保持するか削除するかを必要に応じて選択します。 データを保持する設定でバックアップ データが停止された場合、復旧ポイントは無期限に維持され、どのようなポリシーも適用されません。 これによって、確実にいつでもバックアップ データを復元することができます。

   >[!Note]
   >以前のコンテナー内のデータを保持すると、バックアップ料金が発生します。 データを残すことによる課金を避けたい場合は、[データの削除オプション](/azure/backup/backup-azure-manage-vms#delete-backup-data)を使用して、保持されているバックアップ データを削除する必要があります。

1. SQL Server または SAP HANA が実行されている VM を、[Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines) を使用して新しいリージョンに移動します。

1. 新しいリージョンにある新しい Recovery Services コンテナーまたは既存の Recovery Services コンテナーで VM の保護を開始します。 以前のバックアップから復元する必要が生じた場合でも、引き続き以前の Recovery Services コンテナーから復元することができます。
 
以上の手順により、新しいリージョンでもリソースが確実にバックアップされるようになります。