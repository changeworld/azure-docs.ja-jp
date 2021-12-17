---
title: Azure 管理ツールを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure Germany からグローバル Azure への Azure 管理ツールの移行に関する情報を提供します。
ms.topic: article
ms.date: 06/22/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli
ms.openlocfilehash: 157dded56e3323ed6389f84bd173ba435e8bdc5b
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "117029065"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>管理ツール リソースをグローバル Azure に移行する

[!INCLUDE [closure info](../../includes/germany-closure-info.md)]

この記事には、Azure Germany からグローバル Azure への Azure ストレージ リソースの移行に役立つ情報が含まれています。

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager は、スムーズな移行を完了するために役立ちます。 ただし、Azure Germany で作成した Traffic Manager プロファイルをグローバル Azure に移行することはできません  (移行中に Traffic Manager エンドポイントをターゲット環境に移行するため、いずれにしても Traffic Manager プロファイルを更新する必要があります)。

ソース環境で引き続き実行されている Traffic Manager を使用して、ターゲット環境で追加のエンドポイントを定義できます。 Traffic Manager が新しい環境で実行されている場合も引き続き、ソース環境でまだ移行されていないエンドポイントを定義できます。 このシナリオは、[ブルーグリーン シナリオ](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)と呼ばれます。 このシナリオには、次の手順が必要です。

1. グローバル Azure で、新しい Traffic Manager プロファイルを作成します。
1. Azure Germany でエンドポイントを定義します。
1. ご利用の DNS CNAME レコードを新しい Traffic Manager プロファイルに変更します。
1. 古い Traffic Manager プロファイルを無効にします。
1. エンドポイントを移行して構成します。 Azure Germany 内のエンドポイントごとに、次の操作を行います。
   1. エンドポイントをグローバル Azure に移行します。
   1. 新しいエンドポイントを使用するように Traffic Manager プロファイルを変更します。

詳細情報:

- [Traffic Manager のチュートリアル](../traffic-manager/index.yml)を完了して、新しい知識を得ましょう。
- [Traffic Manager の概要](../traffic-manager/traffic-manager-overview.md)を確認してください。
- [Traffic Manager プロファイルの作成](../traffic-manager/quickstart-create-traffic-manager-profile.md)方法を確認します。
- [ブルーグリーン シナリオ](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)について確認してください。

## <a name="azure-backup"></a>Azure Backup

Azure Backup サービスは、データをバックアップし、それを Microsoft Azure クラウドから復旧するための、シンプルで安全かつコスト効率の高いソリューションとなっています。 PowerShell コマンドレットを使用した、ドイツ中部 (GEC) とドイツ北東部 (GNE) からドイツ中西部 (GWC) へのバックアップ データの移動が可能になっています。

### <a name="prerequisite-for-moving-hybrid-workloads"></a>ハイブリッド ワークロードを移動する場合の前提条件

移動操作が開始されると、既存のコンテナーではバックアップが停止されます。 そのため、リージョンからのバックアップ データの移動を開始する前に、ハイブリッド ワークロード (Data Protection Manager (DPM) サーバー/Azure Backup Server (MABS)/Microsoft Azure Recovery Services (MARS) については、GWC にある新しいコンテナー内のデータを保護することが重要です。
新しいコンテナーで保護を開始するには:

1. GWC で新しいコンテナー (VaultN) を作成します。
1. DPM サーバー/MABS/MARS エージェントを VaultN に再登録します。
1. ポリシーを割り当てて、バックアップの作成を開始します。

最初のバックアップは完全コピー、その後は増分バックアップとなります。

>[!Important]
>- バックアップ データの移動操作を開始する前に、VaultN での最初の完全バックアップが完了したことを確認します。
>- DPM/MABS については、ターゲット コンテナーからデータを復元するのに元のコンテナーのパスフレーズが必要なため、そのパスフレーズを安全な場所に保管します。 元のパスフレーズがないと、ソース コンテナーからの復元は不可能です。

### <a name="step-1-download-the-resources"></a>手順 1: リソースをダウンロードする

必要なリソースをダウンロードしてインストールします。

1. 最新バージョンの PowerShell (PowerShell 7) を[ダウンロード](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.3)します。
1. Azure Cloud Shell で Az.RecoveryServices モジュール バージョン 4.2.0 を使用します。
1. すべての MARS エージェントを最新のバージョンに[更新](https://aka.ms/azurebackup_agent)します。
1. パスフレーズを確認します。 再生成する必要がある場合は、[検証手順](https://support.microsoft.com/topic/mandatory-update-for-azure-backup-for-microsoft-azure-recovery-services-agent-411e371c-aace-e134-de6b-bf9fda48026e#section-3)に従ってください。

### <a name="step-2-create-a-target-vault-in-gwc"></a>手順 2: GWC 内にターゲット コンテナーを作成する

GWC 内にターゲット コンテナー (コンテナー 2) を作成します。 コンテナーを作成する方法については、「[Recovery Services コンテナーを作成して構成する](../backup/backup-create-rs-vault.md)」を参照してください。

>[!Note]
>- コンテナーに、保護された項目がないことを確認します。
>- ターゲット コンテナーに、必要な冗長性 (ローカル冗長ストレージ (LRS) または geo 冗長ストレージ (GRS)) が備わっていることを確認します。

### <a name="step-3---use-powershell-to-trigger-backup-data-move"></a>手順 3: - PowerShell を使用してバックアップ データの移動をトリガーする

#### <a name="get-the-source-vault-from-gne-or-gec"></a>GNE または GEC からソース コンテナーを取得する

以下のコマンドレットを実行します。

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$srcVault = Get-AzRecoveryServicesVault -name “srcVault” -ResourceGroupName “TestSourceRG”`

>[!Note]
>- `srcVault` = ソース コンテナー
>- `TestSourceRG` = ソース リソース グループ

#### <a name="get-the-target-vault-in-gwc"></a>GWC 内のターゲット コンテナーを取得する

以下のコマンドレットを実行します。

1. `Connect-AzAccount`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$trgVault = Get-AzRecoveryServicesVault -name “targetVault” -ResourceGroupName “TestTargetRG”`

>[!Note]
>- `targetVault` = ターゲット コンテナー
>- `TestTargetRG` = テスト リソース グループ

#### <a name="perform-validation"></a>検証を実行する
 
以下のコマンドレットを実行します。

1. `$validated = $false`
1. `$validated = Test-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault`

#### <a name="initializeprepare-ds-move"></a>DS 移動を初期化または準備する

以下のコマンドレットを実行します。

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -SubscriptionName $srcSub`
1. ```azurepowershell
   if($validated) {
           $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault
                       }
   ```
1. `$corr`

#### <a name="trigger-ds-move"></a>DS の移動をトリガーする

以下のコマンドレットを実行します。

1. `Connect-AzAccount`
1. `Set-AzContext -SubscriptionName $trgSub`
1. `Copy-AzRecoveryServicesVault - CorrelationIdForDataMove $corr -SourceVault $srcVault -TargetVault $trgVault -Force`

`Get-AzRecoveryServicesBackupJob` コマンドレットを使用して操作を監視できます。

>[!Note] 
>- バックアップ データの移動操作中には、すべてのバックアップ項目が一時的な状態に移されます。 この状態では、新しい復旧ポイント (RP) は作成されず、古い RP はクリーンアップされません。
>- この機能は GEC と GNE で有効になっているため、小さなコンテナーに対してこれらの手順を実行し、移動を検証することをお勧めします。 成功したら、すべてのコンテナーに対してこれらの手順を実行します。
>- コンテナー全体に対してバックアップ データの移動がトリガーされると共に、移動はコンテナーごと (VM、DPM および MABS サーバー、MARS エージェント) に行われます。 **[ジョブ]** セクションで、コンテナーごとの移動の進行状況を追跡します。 

 ![移動ジョブの進行状況を監視します。](./media/germany-migration-management-tools/track-move-jobs.png)

移動の操作中、ソース コンテナーでは以下のアクションがブロックされます。

- スケジュールされた新しいバックアップ
- データの削除を伴うバックアップの停止。
- データを削除する
- バックアップの再開
- ポリシーの変更

### <a name="step-4-check-the-status-of-the-move-job"></a>手順 4: 移動ジョブの状態を確認する

バックアップ データの移動操作は、コンテナーごとに行われます。 Azure VM バックアップの場合、VM バックアップはコンテナーであると見なされます。 バックアップ データ移動操作の進行状況を示すため、すべてのコンテナーに対してジョブが作成されます。

ジョブを監視するには、以下のコマンドレットを実行します。

1. `Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `$Jobs = Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `Get-AzRecoveryServicesBackupJobDetail -Job $Jobs[0] -VaultId $trgVault.ID`
1. `$JobDetails.ErrorDetails`

### <a name="step-5-post-move-operations"></a>手順 5: 移動後の操作

すべてのコンテナーについて、ターゲット コンテナーへのバックアップ データ移動操作が完了したら、VM バックアップに対して必要な操作はそれ以上ありません。



#### <a name="verify-the-movement-of-containers-is-complete"></a>コンテナーの移動が完了したことを確認する

ソース コンテナーからすべてのコンテナーがターゲット コンテナーに移動したかどうかを確認するには、ターゲット コンテナーに移動し、そのコンテナー内のすべてのコンテナーを確認します。

次のコマンドレットを実行して、ソース コンテナーからターゲット コンテナーに移動されたすべての VM の一覧を表示します。

```azurepowershell
Get-AzRecoveryServicesBackupContainer -BackupManagementType “AzureVM” -VaultId $trgVault.ID
```

#### <a name="verify-the-movement-of-policies-is-complete"></a>ポリシーの移動が完了したことを確認する

バックアップ データが新しいリージョンに正常に移動された後に、ソース コンテナーで Azure VM バックアップ項目に適用されていたすべてのポリシーが、ターゲット コンテナーに適用されます。

すべてのポリシーがソース コンテナーからターゲット コンテナーに移動されたかどうかを確認するには、ターゲット コンテナーに移動し、次のコマンドレットを実行して、移動されたすべてのポリシーの一覧を取得します。

```azurepowershell
Get-AzRecoveryServicesBackupProtectionPolicy -VaultId $trgVault.ID
```

これらのポリシーは、移動された復旧ポイントのライフサイクル管理が続行されるように、移動操作後も引き続きバックアップ データに適用されます。

(移動処理中に期限切れになったか、移動処理の直後に期限切れになる可能性がある) 複数の復旧ポイントが突然クリーンアップされないように、移動後 10 日間は古い復旧ポイント (RP) のクリーンアップが一時停止されます。 この期間中は、古い RP によって発生する追加データに対する請求は行われません。

>[!Important]
>これらの古い RP から復旧する必要がある場合は、バックアップ データの移動直後の、この 10 日間の期間内にそれらを復旧します。 この安全期間が完了すると、各バックアップ項目に適用されるポリシーが有効になって、古い RP のクリーンアップが適用されます。

#### <a name="restore-operations"></a>復元操作

**Azure Virtual Machines を復元する**

Azure Virtual machines の場合は、復旧ポイントから、ターゲット コンテナー内に復元できます。

#### <a name="configure-mars-agent"></a>MARS エージェントを構成する

1. ターゲット コンテナーに再登録します。
1. 復旧ポイントから復元します。
1. 回復後、新しいコンテナー (VaultN) に再登録し、バックアップを再開します。

>[!Note]
>MARS エージェントがターゲット コンテナーに登録されている間には、新しいバックアップは実行されません。

#### <a name="configure-dpmmabs"></a>DPM/MABS を構成する

**推奨**

外部 DPM メソッドを使用して復元を実行します。 詳細については、「[Azure Backup Server からデータを復旧する](../backup/backup-azure-alternate-dpm-server.md)」を参照してください。

>[!Note]
>- 元の場所への復旧 (OLR) はサポートされていません。
>- バックアップは、登録されているすべてのマシンについて、VaultN で続行されます。

**その他のオプション**

元の場所への復旧 (OLR) の場合:

1. DPM サーバー/MABS をターゲット コンテナーに再登録します。
1. 復元操作を実行します。
1. DPM サーバー/MABS を新しいコンテナーに再登録します。

>[!Note]
>DPM の使用に関する制限事項: <br><br> <ul><li>DPM サーバーをターゲット コンテナーに接続すると、DPM サーバーに登録されているすべてのマシンについて、バックアップ操作が停止されます。</li><li>復元後に DPM サーバーが新しいコンテナーに再登録された後には、バックアップの再開前に整合性チェックが実行されます (その完了にかかる時間はデータの量によって異なります)。</li></ul>

### <a name="error-codes"></a>エラー コード

#### <a name="usererrorconflictingdatamoveonvault"></a>UserErrorConflictingDataMoveOnVault 

**メッセージ:** 現在、コンテナーで別のデータ移動操作が実行されています 

**シナリオ:** ソース コンテナーでデータ移動操作を試みていますが、同じソース コンテナーで既に他のデータ移動操作が実行中です。

**推奨される操作:** 現在のデータ移動操作が完了するのを待ってから、もう一度試します。

#### <a name="usererroroperationnotallowedduringdatamove"></a>UserErrorOperationNotAllowedDuringDataMove

**メッセージ:** データ移動操作が進行中のため、この操作は許可されません。 

**シナリオ:** データ移動操作の進行中、以下の操作はソース コンテナーで許可されません。
 
- データの保持を伴うバックアップの停止 
- データの削除を伴うバックアップの停止。 
- バックアップ データの削除。 
- バックアップの再開 
- ポリシーの変更。

**推奨される操作:** データ移動操作が完了するのを待ってから、もう一度試します。 サポートされている操作に関する[詳細を確認](#azure-backup)してください。

#### <a name="usererrornocontainersfoundfordatamove"></a>UserErrorNoContainersFoundForDataMove 

**メッセージ:** このボールトには、データ移動操作がサポートされているコンテナーはありません 

**シナリオ:** このメッセージは、次の場合に表示されます。

- ソース コンテナーにまったくコンテナーがない。 
- ソース コンテナーにはサポートされていないコンテナーだけがある。 
- ソース コンテナーには、以前にいずれかのターゲット コンテナーに移動されたすべてのコンテナーがあり、API では IgnoreMoved = true を指定した。

**推奨される操作:** データ移動でサポートされるコンテナーを[確認](#azure-backup)します。

#### <a name="usererrordatamovenotsupportedatcontainerlevel"></a>UserErrorDataMoveNotSupportedAtContainerLevel 

**メッセージ:** データ移動操作はコンテナー レベルではサポートされていません。 

**シナリオ:** コンテナー レベルのデータ移動操作を選択しました。 

**推奨される操作:** コンテナー レベルのデータ移動操作を試みます。

### <a name="usererrordatamovenotallowedcontainer-registrationinprogress"></a>UserErrorDataMoveNotAllowedContainer RegistrationInProgress 

**メッセージ:** コンテナーの登録操作がソース ボールトで実行されているため、データ移動操作は許可されません。 

**シナリオ:** データの移動を試みたときに、ソース コンテナーでコンテナー登録操作が進行しています。 

**推奨される操作:** しばらくしてからデータ移動操作を試みます。

#### <a name="usererrordatamovenotallowedtargetvaultnotempty"></a>UserErrorDataMoveNotAllowedTargetVaultNotEmpty 

**メッセージ:** ターゲット ボールトに既にコンテナーが登録されているため、データ移動操作は許可されません。 

**シナリオ:** 選択したターゲット ボールトには、既に登録されているコンテナーがいくつかあります。 

**推奨される操作:** 空のターゲット ボールトに対してデータ移動操作を試みます。

#### <a name="usererrorunsupportedsourceregionfordatamove"></a>UserErrorUnsupportedSourceRegionForDataMove 

**メッセージ:** データ移動操作は、このリージョンではサポートされていません 

**シナリオ:** ソース リージョンが無効です。

**推奨される操作:** データ移動で[サポートされるリージョンの一覧](#azure-backup)を確認します。

#### <a name="usererrorunsupportedtargetregionfordatamove"></a>UserErrorUnsupportedTargetRegionForDataMove 

**メッセージ:** このリージョンへのデータ移動操作はサポートされていません

**シナリオ:** ターゲット リージョンの ID が無効です。 

**推奨される操作:** データ移動で[サポートされるリージョンの一覧](#azure-backup)を確認します。


#### <a name="usererrordatamovetargetvaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveTargetVaultWithPrivate EndpointNotSupported 

**メッセージ:** 選択したターゲット コンテナーにプライベート エンドポイントがあるためデータを移動できません。 

**シナリオ:** ターゲット コンテナーでプライベート エンド ポイントが有効になっています。 

**推奨される操作:** プライベート エンドポイントを削除し、移動操作を再試行します。 サポートされている操作に関する[詳細を確認](#azure-backup)してください。

### <a name="usererrordatamovesourcevaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveSourceVaultWithPrivate EndpointNotSupported 

**メッセージ:** 選択したソース コンテナーにプライベート エンドポイントがあるためデータを移動できません。

**シナリオ:** ソース コンテナーでプライベート エンド ポイントが有効になっています。

**推奨される操作:** プライベート エンドポイントを削除し、移動操作を再試行します。 サポートされている操作に関する[詳細を確認](../backup/private-endpoints.md#deleting-private-endpoints)してください。

#### <a name="usererrordatamovesourcevaultwithcmk-notsupported"></a>UserErrorDataMoveSourceVaultWithCMK NotSupported 

**メッセージ:** 選択したソース コンテナーで暗号化が有効になっているため、データを移動できません。 

**シナリオ:** ソース コンテナーでカスタマー マネージド キー (CMK) が有効になっています。

**推奨される操作:** サポートされる操作を[確認](#azure-backup)します。

#### <a name="usererrordatamovetargetvaultwithcmknotsupported"></a>UserErrorDataMoveTargetVaultWithCMKNotSupported 

**メッセージ:** 選択したターゲット コンテナーで暗号化が有効になっているため、データを移動できません。 

**シナリオ:** ターゲット コンテナーでカスタマー マネージド キー (CMK) が有効になっています。

**推奨される操作:** サポートされる操作を[確認](#azure-backup)します。

## <a name="scheduler"></a>Scheduler

Azure Scheduler は廃止される予定です。 ジョブのスケジュール設定を作成するため、代わりに、グローバル Azure で [Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用できます。

詳細情報:

- [Azure Logic Apps のチュートリアル](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)を完了することで詳細を確認してください。
- [Azure Logic Apps の概要](../logic-apps/logic-apps-overview.md)を確認します。

## <a name="network-watcher"></a>Network Watcher

現時点では、Azure Network Watcher インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、新しい Network Watcher インスタンスを作成および構成することをお勧めします。 次に、古い環境と新しい環境の間で結果を比較します。 

詳細情報:

- [Network Watcher のチュートリアル](../network-watcher/index.yml)を完了して、新しい知識を得ましょう。
- [Network Watcher の概要](../network-watcher/network-watcher-monitoring-overview.md)を確認してください。
- [ネットワーク セキュリティ グループのフロー ログ](../network-watcher/network-watcher-nsg-flow-logging-portal.md)について学習してください。
- [接続モニター](../network-watcher/connection-monitor.md)について確認してください。

## <a name="site-recovery"></a>Site Recovery

現在の Azure Site Recovery の設定をグローバル Azure に移行することはできません。 グローバル Azure で新しい Site Recovery ソリューションを設定する必要があります。

Site Recovery の詳細について、また、Azure Germany からグローバル Azure に VM を移行する方法については、[Site Recovery の使用方法](./germany-migration-compute.md#compute-iaas)に関するページを参照してください。

次のステップ バイ ステップ チュートリアルを完了して、新しい知識を得ましょう。

- [Azure から Azure へのディザスター リカバリー](../site-recovery/azure-to-azure-about-networking.md)
- [VMware から Azure へのディザスター リカバリー](../site-recovery/site-recovery-deployment-planner.md)
- [Hyper-V から Azure へのディザスター リカバリー](../site-recovery/hyper-v-deployment-planner-overview.md)

## <a name="azure-policies"></a>Azure のポリシー

Azure Germany からグローバル Azure にポリシーは直接移行することはできません。 移行中、割り当て済みのポリシーのスコープは、通常、変更されます。 特に、このシナリオのように、サブスクリプションがターゲット環境で異なる場合にそのようになります。 ただし、ポリシー定義を保持し、それをグローバル Azure で再利用することができます。

Azure CLI では、次のコマンドを実行して、現在ご利用の環境内のポリシーをすべて一覧表示することができます。

> [!NOTE]
> 次のコマンドを実行する前に、必ず、Azure CLI 内で AzureGermanCloud 環境に切り替えてください。


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

**PolicyType** の値が **Custom** となっているポリシーのみをエクスポートします。 **policyRule** をファイルにエクスポートします。 次の例では、カスタム ポリシー "Allow Germany Central Only" (短縮形: `allowgconly`) を現在のフォルダーにあるファイルにエクスポートしています。 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

エクスポート ファイルは、次の例のようになります。

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

次に、グローバル Azure 環境に切り替えます。 ファイルを編集してポリシー規則を変更します。 たとえば、`germanycentral` を `westeurope` に変更します。

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

新しいポリシーの作成: 

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

これで、`allowweonly` という名前の新しいポリシーが用意できました。 このポリシーでは、リージョンとして西ヨーロッパのみが許可されます。

必要に応じて、新しい環境でスコープにポリシーを割り当てます。 次のコマンドを実行することで、Azure Germany 内の古い割り当てを文書化することができます。

```azurecli
az policy assignment list
```

詳細情報:

- [Azure DNS ポリシーのチュートリアル](../governance/policy/tutorials/create-and-manage.md)を完了して、新しい知識を得ましょう。
- [Azure CLI を使用してポリシーを表示](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli)する方法または[PowerShell を使用してポリシーを表示](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell)する方法を確認してください。
- [Azure CLI を使用してポリシーの定義を作成](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli)する方法または [PowerShell を使用してポリシーの定義を作成](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell)する方法を確認してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="where-can-i-move-the-backup-data"></a>バックアップ データを移動できる場所はどこですか?

バックアップ データは、ドイツ中部 (GEC) およびドイツ北東部 (GNE) の Recovery Services コンテナー (RSV) から、ドイツ中西部 (GEC) に移動できます。

### <a name="what-backup-data-can-i-move"></a>どのようなバックアップ データを移動できますか?

2021 年 6 月 21 日から、以下のワークロードのバックアップ データをリージョン間で移動できます。

- Azure Virtual Machines
- ハイブリッド ワークロード
- Microsoft Azure Recovery Services (MARS) エージェントを使用するファイルやフォルダーのバックアップ
- Data Protection Manager (DPM) サーバー
- Azure Backup Server (MABS)

### <a name="how-can-i-move-backup-data-to-another-region"></a>どのようにすればバックアップ データを別のリージョンに移動できますか?

既存のリージョンにあるデータが失われないようにするため、Azure Backup では、GEC および GNE から GWC へのバックアップ データの移動が有効にされています。

移行が行われている間、GEC と GNE ではバックアップが停止されます。 そのため、移行操作を開始する前に、新しいリージョンでワークロードを保護することが不可欠です。

### <a name="what-to-do-if-the-backup-data-move-operation-fails"></a>バックアップ データ移動操作が失敗した場合はどうすればよいですか?

バックアップ データは、以下のエラー シナリオによって発生する可能性があります。

| エラー メッセージ    | 原因 |
| --- | --- |
| 空のターゲット コンテナーを指定してください。 ターゲット コンテナーに、バックアップ項目やバックアップ コンテナーが含まれていてはいけません。 | 保護された項目が既に存在するターゲット コンテナーを選択しました。 |
| Azure Backup データは、サポートされているターゲット リージョンにのみ移動できます。 | 移動がサポートされているリージョンの 1 つではないリージョンから、ターゲット コンテナーを選択しました。 |

新しい空のターゲット コンテナーで同じコマンド (以下を参照) を実行して、最初からバックアップを再試行する必要があります。または、フラグで指定することで、失敗した項目のソース コンテナーからの移動を再試行することもできます。

```azurepowershell
   if($validated) {
                              $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault -RetryOnlyFailed
                      }
```

### <a name="is-there-a-cost-involved-in-moving-this-backup-data"></a>このバックアップ データの移動に伴うコストはありますか?

いいえ。 リージョン間でバックアップ データを移動する場合の追加コストはありません。 リージョン間でデータを移動するコストは Azure Backup によって負担されます。 移動操作完了後の非請求期間は 10 日間のみです。 この期間後、ターゲット コンテナーでの課金が開始されます。

### <a name="if-i-face-issues-in-moving-backup-data-whom-should-i-contact"></a>バックアップ データの移動で問題が発生した場合は、どこに連絡する必要がありますか?

GEC または GNE から GWC へのバックアップ データの移行に関する問題についてはすべて、[GESupportAzBackup@microsoft.com](mailto:GESupportAzBackup@microsoft.com) までメールにてご連絡ください。

## <a name="next-steps"></a>次のステップ

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [メディア](./germany-migration-media.md)
