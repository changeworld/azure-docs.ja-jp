---
title: Azure Backup Recovery Services コンテナーを移動する方法
description: Recovery Services コンテナーを Azure サブスクリプションおよびリソース グループをまたいで移動する方法の手順。
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: references_regions
ms.openlocfilehash: edd15e6b32911c76a54bbe852af4236ba12250f5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264771"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Recovery Services コンテナーを Azure サブスクリプションおよびリソース グループをまたいで移動する

この記事では、Azure Backup 用に構成された Recovery Services コンテナーを、Azure のサブスクリプションをまたいで移動するか、同じサブスクリプション内の別のリソース グループに移動する方法について説明します。 Azure portal または PowerShell を使用して、Recovery Services コンテナーを移動することができます。

## <a name="supported-regions"></a>サポートされているリージョン

フランス中部、フランス南部、ドイツ北東部、ドイツ中部、中国北部、中国北部 2、中国東部、中国東部 2 以外のすべてのパブリック リージョンと主権リージョンのすべてです。

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Recovery Services コンテナーを移動するための前提条件

- リソース グループをまたいでコンテナーを移動している間、ソースとターゲットの両方のリソース グループがロックされ、書き込み操作と削除操作が禁止されます。 詳細については、[こちらの記事](../azure-resource-manager/management/move-resource-group-and-subscription.md)を参照してください。
- 管理者のサブスクリプションのみが、コンテナーを移動するアクセス許可を持ちます。
- サブスクリプション間でコンテナーを移動する場合、ターゲット サブスクリプションがソース サブスクリプションと同じテナントに存在すること、またその状態が有効になっている必要があります。 コンテナーを別の Azure AD に移動するには、[サブスクリプションを別のディレクトリに移動する方法](../role-based-access-control/transfer-subscription.md)および [Recovery Service コンテナーの FAQ](./backup-azure-backup-faq.yml) に関する記事をご覧ください。
- ターゲット リソース グループへの書き込み操作を実行するアクセス許可が必要です。
- コンテナーを移動すると、リソース グループのみが変更されます。 Recovery Services コンテナーは同じ場所に存在し、変更することはできません。
- 一度に移動できる Recovery Services コンテナーはリージョンごとに 1 つだけです。
- VM が Recovery Services コンテナーと一緒にサブスクリプションをまたいで移動しないか、または新しいリソース グループに移動しない場合、現在の VM の復旧ポイントは、有効期限が切れるまでコンテナーにそのまま残ります。
- VM をコンテナーと一緒に移動するかどうかに関係なく、VM はコンテナー内に保持されているバックアップ履歴から復元できます。
- Azure Disk Encryption では、キー コンテナーと VM が同じ Azure リージョンおよびサブスクリプションに属している必要があります。
- 仮想マシンをマネージド ディスクと一緒に移動するには、この[記事](../azure-resource-manager/management/move-resource-group-and-subscription.md)を参照してください。
- クラシック モデルを使用してデプロイされるリソースを移動するためのオプションは、リソースをサブスクリプション内で移動するか、新しいサブスクリプションに移動するかによって異なります。 詳細については、[こちらの記事](../azure-resource-manager/management/move-resource-group-and-subscription.md)を参照してください。
- コンテナーに対して定義されるバックアップ ポリシーは、コンテナーがサブスクリプションをまたいで移動しても、新しいリソース グループに移動しても保持されます。
- 移動できるのは、次のいずれかの種類のバックアップ項目を含むコンテナーだけです。 以下に記載されていない種類のバックアップ項目をすべて停止し、データを完全に削除してから、コンテナーを移動する必要があります。
  - Azure Virtual Machines
  - Microsoft Azure Recovery Services (MARS) エージェント
  - Microsoft Azure Backup Server (MABS)
  - Data Protection Manager (DPM)
- VM のバックアップ データがあるコンテナーをサブスクリプションをまたいで移動する場合、バックアップを継続するためには、お客様の VM を同じサブスクリプションに移動し、同じターゲット VM リソース グループ名 (以前のサブスクリプションと同じもの) を使用する必要があります。

> [!NOTE]
> Azure リージョン間で Azure Backup 用の Recovery Services コンテナーを移動することはできません。<br><br>
> **Azure Site Recovery** を使用して、任意の VM (Azure IaaS、HYPER-V、VMware) または物理マシンをディザスター リカバリー用に構成している場合、移動操作はブロックされます。 Azure Site Recovery 用のコンテナーを移動する場合は、[この記事](../site-recovery/move-vaults-across-regions.md)を参照して、コンテナーを手動で移動する方法を確認してください。

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Azure portal を使用して Recovery Services コンテナーを別のリソース グループに移動する

Recovery Services コンテナーとその関連リソースを別のリソース グループに移動するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **Recovery Services コンテナー** の一覧を開き、移動するコンテナーの名前を選択します。 コンテナーのダッシュボードが開き、次の図のように表示されます。

   ![Recovery Services コンテナーを開く](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   コンテナーの **[Essentials]** 情報が表示されない場合、ドロップダウン アイコンを選択します。 コンテナーの Essentials 情報が表示されるはずです。

   ![[Essentials] 情報タブ](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. コンテナーの概要メニューで、 **[リソース グループ]** の横にある **[変更]** を選択して **[リソースの移動]** ペインを開きます。

   ![リソース グループを変更する](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. **[リソースの移動]** ペインでは、選択したコンテナーについては次の図に示すようにチェック ボックスをオンにして、オプションの関連リソースを移動することをお勧めします。

   ![サブスクリプションを移動する](./media/backup-azure-move-recovery-services/move-resource.png)

5. ターゲット リソース グループを追加するには、 **[リソース グループ]** ドロップダウン リストで、既存のリソース グループを選択するか、 **[新しいグループを作成する]** オプションを選択します。

   ![リソースを作成する](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. リソース グループを追加した後、 **[移動されたリソースに関連付けられているツールとスクリプトは、新しいリソース ID を使用するように更新するまで動作しないことを理解しました。]** オプションを確認し、 **[OK]** を選択してコンテナーの移動を完了します。

   ![確認メッセージ](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Azure portal を使用して Recovery Services コンテナーを別のサブスクリプションに移動する

Recovery Services コンテナーとその関連リソースを別のサブスクリプションに移動できます

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Recovery Services コンテナーの一覧を開き、移動するコンテナーを選択します。 コンテナーのダッシュボードが開き、次の図のように表示されます。

    ![Recovery Services コンテナーを開く](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    コンテナーの **[Essentials]** 情報が表示されない場合、ドロップダウン アイコンを選択します。 コンテナーの Essentials 情報が表示されるはずです。

    ![[Essentials] 情報タブ](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. コンテナーの概要メニューで、 **[サブスクリプション]** の横にある **[変更]** を選択して **[リソースの移動]** ペインを開きます。

   ![サブスクリプションを変更する](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 移動するリソースを選択します。 ここで、 **[すべて選択]** オプションを使用して、表示されているすべてのオプション リソースを選択することをお勧めします。

   ![リソースの移動](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. コンテナーを移動する先のターゲットのサブスクリプションを、 **[サブスクリプション]** ドロップダウン リストから選択します。
6. ターゲット リソース グループを追加するには、 **[リソース グループ]** ドロップダウン リストで、既存のリソース グループを選択するか、 **[新しいグループを作成する]** オプションを選択します。

   ![サブスクリプションを追加する](./media/backup-azure-move-recovery-services/add-subscription.png)

7. **[移動されたリソースに関連付けられているツールとスクリプトは、新しいリソース ID を使用するように更新するまで動作しないことを理解しました。]** オプションを選択して確認し、 **[OK]** を選択します。

> [!NOTE]
> クロス サブスクリプション バックアップ (RS コンテナーと保護対象の VM が別のサブスクリプションにある) シナリオは、サポートされていません。 また、ローカル冗長ストレージ (LRS) からグローバル冗長ストレージ (GRS) への (およびその逆の) ストレージ冗長オプションは、コンテナー移動操作中に変更できません。

## <a name="use-azure-portal-to-back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>Azure portal を使用して、リージョン間での移動後に Recovery Services コンテナー内のリソースをバックアップする

Azure Resource Mover では、リージョン間での複数のリソースの移動がサポートされています。 リージョン間でリソースを移動している間も、リソースが保護された状態を確実に維持することができます。 Azure Backup では複数のワークロードの保護がサポートされているため、新しいリージョンでも同じ保護レベルを維持するためには、いくつかの手順を行う必要がある場合があります。

そのための詳しい手順を理解するために、以降のセクションを参照してください。

>[!Note]
>Recovery Services コンテナー間でのバックアップ データの移動は、現在、Azure Backup ではサポートされていません。 新しいリージョンのリソースを保護するためには、新しいリージョン内の新しいまたは既存のコンテナーにそのリソースを登録してバックアップする必要があります。 リージョン間でリソースを移動するときには、必要に応じて、以前のリージョンにある既存の Recovery Services コンテナー内のバックアップ データを保持または削除することができます。 以前のコンテナー内のデータを保持することにした場合は、それに応じたバックアップ料金が発生します。

### <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>リージョン間での移動後に Azure 仮想マシンをバックアップする

Recovery Services コンテナーによって保護された Azure 仮想マシン (VM) は、別のリージョンに移動すると、以前のコンテナーにバックアップできなくなります。 以前のコンテナーへのバックアップは、**BCMV2VMNotFound** または [**ResourceNotFound**](./backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found) というエラーで失敗するようになります。 新しいリージョンで VM を保護する方法については、以降のセクションを参照してください。

#### <a name="prepare-to-move-azure-vms"></a>Azure VM の移動を準備する

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

#### <a name="move-azure-vms"></a>Azure VM の移動

[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) を使用して、VM を新しいリージョンに移動します。

#### <a name="protect-azure-vms-using-azure-backup"></a>Azure Backup を使用して Azure VM を保護する

新しいリージョンの新しいまたは既存の Recovery Services コンテナーで VM の保護を開始します。 以前のバックアップから復元する必要がある場合は、バックアップ データを保持するように選択してあれば、引き続き以前の Recovery Services コンテナーから復元することができます。 

以上の手順により、新しいリージョンでもリソースが確実にバックアップされるようになります。

### <a name="back-up-azure-file-share-after-moving-across-regions"></a>リージョン間での移動後に Azure ファイル共有をバックアップする

現在、Azure Backup には、Azure Files 用の[スナップショット管理ソリューション](./backup-afs.md)が用意されています。 つまり、ファイル共有データを手動で Recovery Services コンテナーに移動することはありません。 また、スナップショットはストレージ アカウントと一緒に移動されないため、実質上、すべてのバックアップ (スナップショット) は既存のリージョンにのみ存在し、既存のコンテナーによって保護されることになります。 ただし、ストレージ アカウントをファイル共有と共にリージョン間で移動する場合や、新しいリージョンに新しいファイル共有を作成する場合は、以降のセクションを参照して、Azure Backup によってそれらを確実に保護します。

#### <a name="prepare-to-move-azure-file-share"></a>Azure ファイル共有の移動を準備する

ストレージ アカウントを移動する前に、以下の前提条件が満たされていることを確認してください。

1.  [ストレージ アカウントを移動するための前提条件](../storage/common/storage-account-move.md?tabs=azure-portal#prerequisites)を確認します。 
1. リソースの移動テンプレートをエクスポートして変更します。 詳細については、[リージョン移動のためにストレージ アカウントを準備する](../storage/common/storage-account-move.md?tabs=azure-portal#prepare)ことに関するページを参照してください。

#### <a name="move-azure-file-share"></a>Azure ファイル共有を移動する

ストレージ アカウントと、その中の Azure ファイル共有をリージョン間で移動するには、「[Azure Storage アカウントを別のリージョンに移動する](../storage/common/storage-account-move.md)」を参照してください。

>[!Note]
>Azure ファイル共有をリージョン間でコピーしても、そこに関連付けられているスナップショットは一緒に移動されません。 スナップショット データを新しいリージョンに移動するには、スナップショットの個々のファイルとディレクトリを、[AzCopy](../storage/common/storage-use-azcopy-files.md#copy-all-file-shares-directories-and-files-to-another-storage-account) を使用して新しいリージョンのストレージ アカウントに移動する必要があります。

#### <a name="protect-azure-file-share-using-azure-backup"></a>Azure Backup を使用して Azure ファイル共有を保護する

新しいリージョン内の新しいまたは既存の Recovery Services コンテナーで、新しいストレージ アカウントにコピーされた Azure ファイル共有の保護を開始します。  

Azure ファイル共有が新しいリージョンにコピーされたら、保護を停止し、元の Azure ファイル共有のスナップショット (および対応する復旧ポイント) を必要に応じて保持または削除するように選択できます。 これは元のコンテナーのダッシュボードの [[バックアップ項目] タブ](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)で、該当するファイル共有を選択することで行うことができます。 データを保持する設定で Azure ファイル共有のバックアップ データが停止された場合、復旧ポイントは無期限に維持され、いずれのポリシーにも従いません。
   
これにより、確実にいつでも以前のコンテナーからスナップショットを復元できるようになります。 
 
### <a name="back-up-sql-serversap-hana-in-azure-vm-after-moving-across-regions"></a>リージョン間の移動後に Azure VM 内の SQL Server または SAP HANA をバックアップする

SQL または SAP HANA サーバーが実行されている VM を別のリージョンに移動した場合、それらの VM 内の SQL および SAP HANA データベースは、以前のリージョンのコンテナーにバックアップできなくなります。 新しいリージョンで、Azure VM で実行中の SQL サーバーや SAP HANA サーバーを保護するには、以降のセクションを参照してください。

#### <a name="prepare-to-move-sql-serversap-hana-in-azure-vm"></a>Azure VM 内の SQL Server または SAP HANA の移動を準備する

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

#### <a name="move-sql-serversap-hana-in-azure-vm"></a>Azure VM 内の SQL Server または SAP HANA を移動する

[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) を使用して、VM を新しいリージョンに移動します。

#### <a name="protect-sql-serversap-hana-in-azure-vm-using-azure-backup"></a>Azure Backup を使用して Azure VM 内の SQL Server または SAP HANA を保護する

新しいリージョンの新しいまたは既存の Recovery Services コンテナーで VM の保護を開始します。 以前のバックアップから復元する必要がある場合でも、引き続き以前の Recovery Services コンテナーから復元することができます。
 
上記の手順は、新しいリージョンでもリソースが確実にバックアップされるようにするのに役立つはずです。

## <a name="use-powershell-to-move-recovery-services-vault"></a>PowerShell を使用して Recovery Services コンテナーを移動する

Recovery Services コンテナーを別のリソース グループに移動するには、`Move-AzureRMResource` コマンドレットを使用します。 `Move-AzureRMResource` ではリソース名とリソースの種類が必要です。 どちらも `Get-AzureRmRecoveryServicesVault` コマンドレットから取得できます。

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

リソースを他のサブスクリプションに移動するには、`-DestinationSubscriptionId` パラメーターを含めます。

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

上記のコマンドレットを実行した後、指定したリソースを移動することの確認を求められます。 「**Y**」と入力して確認します。 検証が成功した後、リソースが移動します。

## <a name="use-cli-to-move-recovery-services-vault"></a>CLI を使用して Recovery Services コンテナーを移動する

Recovery Services コンテナーを別のリソース グループに移動するには、次のコマンドレットを使用します。

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

新しいサブスクリプションに移動するには、`--destination-subscription-id` パラメーターを指定します。

## <a name="post-migration"></a>移行後の処理

1. リソース グループのアクセス制御を設定および検証します。  
2. 移動の完了後のコンテナーに対して、バックアップのレポートと監視機能をもう一度構成する必要があります。 以前の構成は、移動操作中に失われます。

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Azure 仮想マシンを別の Recovery Services コンテナーに移動する 

Azure Backup が有効になっている Azure 仮想マシンを移動する場合は、2 つの選択肢があります。 どちらを選ぶかは、ビジネス要件によって異なります。

- [以前にバックアップしたデータを保持する必要がない](#dont-need-to-preserve-previous-backed-up-data)
- [以前にバックアップしたデータを保持する必要がある](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>以前にバックアップしたデータを保持する必要がない

新しいコンテナーでワークロードを保護するには、現在の保護とデータを古いコンテナーから削除し、バックアップを再び構成する必要があります。

>[!WARNING]
>次の操作は破壊的であり、元に戻すことはできません。 保護されたサーバーに関連付けられているすべてのバックアップ データとバックアップ項目が、完全に削除されます。 慎重に進めてください。

**古いコンテナーで現在の保護を停止して削除する:**

1. コンテナーのプロパティで、論理的な削除を無効にします。 論理的な削除を無効にするには、[こちらの手順](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal)に従ってください。

2. 保護を停止し、現在のコンテナーからバックアップを削除します。 コンテナー ダッシュボード メニューで **[バックアップ項目]** を選択します。 ここに一覧表示されている、新しいコンテナーに移動する必要がある項目は、バックアップ データと共に削除する必要があります。 [クラウド内の保護されたアイテムを削除する](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)および[オンプレミスの保護されたアイテムを削除する](backup-azure-delete-vault.md#delete-protected-items-on-premises)方法を参照します。

3. AFS (Azure ファイル共有)、SQL Server、または SAP HANA サーバーの移動を計画している場合は、それらの登録を解除する必要もあります。 コンテナーのダッシュボード メニューで **[バックアップ インフラストラクチャ]** を選択します。 [SQL Server を登録解除する](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)、[Azure ファイル共有に関連付けられたストレージ アカウントを登録解除する](manage-afs-backup.md#unregister-a-storage-account)、および [SAP HANA インスタンスを登録解除する](sap-hana-db-manage.md#unregister-an-sap-hana-instance)方法を参照してください。

4. 古いコンテナーから削除されたら、引き続き、新しいコンテナーでワークロードのバックアップを構成します。

### <a name="must-preserve-previous-backed-up-data"></a>以前にバックアップしたデータを保持する必要がある

古いコンテナー内にある現在保護されているデータを保持し、新しいコンテナーで保護を継続する必要がある場合は、一部のワークロードでオプションが制限されます。

- MARS の場合は、[保護を停止してデータを保持](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup)し、新しいコンテナーでエージェントを再び登録できます。

  - 古いコンテナーの既存の復旧ポイントはすべて、引き続き Azure Backup サービスによって保持されます。
  - 古いコンテナー内の復旧ポイントを保持するには、料金を支払う必要があります。
  - バックアップされたデータの復元は、古いコンテナー内の有効期限が切れていない復旧ポイントに対してのみ可能です。
  - データの新しい初期レプリカを新しいコンテナーに作成する必要があります。

- Azure VM の場合は、古いコンテナー内の VM に対して[保護を停止してデータを保持](backup-azure-manage-vms.md#stop-protecting-a-vm)し、VM を別のリソース グループに移動してから、新しいコンテナー内の VM を保護することができます。 VM を別のリソース グループに移動するための[ガイダンスと制限事項](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)に関するページを参照してください。

  VM を保護できるのは、一度に 1 つのコンテナーのみになります。 ただし、新しいリソース グループ内の VM は別の VM と見なされるため、新しいコンテナーで保護できます。

  - Azure Backup サービスにより、古いコンテナーにバックアップされている回復ポイントが保持されます。
  - 古いコンテナーの復旧ポイントを保持するには、料金を支払う必要があります (詳細については、[Azure Backup の料金](azure-backup-pricing.md)に関するページを参照してください)。
  - 必要な場合は、古いコンテナーから VM を復元することができます。
  - 新しいリソース内の VM に対する新しいコンテナー上の最初のバックアップは、初期レプリカになります。

## <a name="next-steps"></a>次のステップ

リソース グループとサブスクリプションの間でさまざまな種類のリソースを移動できます。

詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。
