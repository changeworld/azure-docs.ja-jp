---
title: Azure VM バックアップの FAQ
description: '一般的な質問への回答: Azure VM バックアップの動作、制限、ポリシーの変更があったときに起こること'
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: 6ec178a8cb457973f39ea2dd929a3486a7696c55
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972194"
---
# <a name="frequently-asked-questions-azure-backup"></a>よく寄せられる質問 - Azure Backup

この記事では、[Azure Backup](backup-introduction-to-azure-backup.md) サービスについてよく寄せられる質問への回答を示します。

## <a name="general-questions"></a>一般的な質問


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Azure Backup ではどのような Azure VM をバックアップできますか。
サポートされているオペレーティング システムと制限を[ご確認ください](backup-azure-arm-vms-prepare.md#before-you-start)。



## <a name="backup"></a>バックアップ

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>オンデマンド バックアップ ジョブのリテンション期間のスケジュールは、スケジュールされたバックアップと同じですか。
いいえ。 オンデマンド バックアップ ジョブのリテンション期間はご自身で指定する必要があります。 ポータルからトリガーした場合、既定では 30 日間保存されます。

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>最近いくつかの VM で Azure Disk Encryption を有効にしました。 既存のバックアップは今後も正常に機能しますか。
Azure Backup に Key Vault へのアクセス許可を付与する必要があります。 [Azure Backup PowerShell](backup-azure-vms-automation.md) ドキュメントの**バックアップの有効化**に関するセクションの説明に従って、PowerShell でアクセス許可を指定します。

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>VM ディスクをマネージド ディスクに移行しました。 既存のバックアップは今後も正常に機能しますか。
はい。バックアップはシームレスに機能します。 何も再構成する必要はありません。

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>バックアップ構成ウィザードに VM が表示されていません。なぜでしょうか。
ウィザードにはコンテナーと同じリージョンで、まだバックアップされていない VM のみが表示されます。


### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>VM がシャットダウンされています。 オンデマンドまたはスケジュールされたバックアップは機能しますか。
はい。 マシンがシャットダウンされていても、バックアップは動作します。 復旧ポイントはクラッシュ整合としてマークされます。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>進行中のバックアップ ジョブを取り消すことはできますか。
はい。 **スナップショット作成**状態のバックアップ ジョブは取り消すことができます。 スナップショットからのデータ転送が進行中である場合は、ジョブを取り消せません。

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>バックアップされたマネージド ディスク VM でリソース グループのロックを有効にしました。 既存のバックアップは今後も正常に機能しますか。
リソース グループをロックすると、Azure Backup サービスで古い復元ポイントを削除できなくなります。
- 復元ポイントの上限は 18 個であるため、新しいバックアップは失敗し始めます。
- ロック後に内部エラーでバックアップが失敗した場合は、[こちらの手順に従って](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal)、復元ポイントのコレクションを削除します。

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>バックアップ ポリシーでは夏時間 (DST) が考慮されますか。
いいえ。 ローカル コンピューターの日時は、現在の夏時間が適用されたローカル時刻です。 スケジュールされているバックアップの時間は、DST のためにローカル タイムと異なる場合があります。

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Azure Backup によってバックアップされた VM には何台のデータ ディスクを接続できますか。
Azure Backup では最大 16 台のディスクを搭載した VM をバックアップできます。 16 台のディスクのサポートは、[最新バージョン](backup-upgrade-to-vm-backup-stack-v2.md)の Azure VM バックアップ スタック V2 で提供されます。

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Azure Backup は Standard SSD マネージド ディスクをサポートしていますか。
Azure Backup では、[Standard SSD マネージド ディスク](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)がサポートされています。 SSD マネージド ディスクは、Azure VM に対して新しい種類の永続ストレージを提供します。 SSD マネージド ディスクのサポートは、[最新バージョン](backup-upgrade-to-vm-backup-stack-v2.md)の Azure VM バックアップ スタック V2 で提供されます。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>書き込みアクセラレータ (WA) 対応ディスクを使用して VM をバックアップできますか。
WA 対応ディスクでスナップショットを作成することはできません。 ただし、Azure Backup サービスでは、WA 対応ディスクをバックアップから除外できます。 WA 対応ディスクでの VM ディスク除外は、Azure VM バックアップ スタック V2 にアップグレードされたサブスクリプションでのみサポートされます。 Azure VM バックアップ スタック V2 にアップグレードするには、こちらの[記事](backup-upgrade-to-vm-backup-stack-v2.md)をご覧ください。 この機能は、現在、東日本、北ヨーロッパ、東南アジア、米国東部、米国西部 2、西ヨーロッパ、および米国東部 2 でご利用いただけます。


### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>書き込みアクセラレータ (WA) ディスク と SAP HANA がインストールされている VM があります。 バックアップするには、どうすればよいですか。
Azure Backup では WA 対応ディスクをバックアップできませんが、バックアップから除外することはできます。 ただし、バックアップによってデータベース整合性が維持されなくなります。WA 対応ディスクの情報がバックアップされないためです。 オペレーティング システム ディスクのバックアップ、および WA 対応ではないディスクのバックアップが必要な場合は、この構成でディスクをバックアップできます。

RPO が 15 分の SAP HANA バックアップに対するプライベート プレビューがあります。 このプレビューは同様の方法で SQL DB バックアップに組み込まれていて、backInt インターフェイスが、SAP HANA 認定のサード パーティ ソリューションに対して使用されています。 プライベート プレビューについて関心をお持ちの場合は、"**Azure VM での SAP HANA バックアップのプライベート プレビューへのサインアップ**" という件名で、` AskAzureBackupTeam@microsoft.com ` 宛てにメールでご連絡ください。


## <a name="restore"></a>復元

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>ディスクのみを復元するか、VM 全体を復元するかは、どのように判断すればよいでしょうか。
VM の復元は、Azure VM 用の簡易的な作成オプションと考えてください。 このオプションでは、ディスク名、そのディスクが使用するコンテナー、パブリック IP アドレス、およびネットワーク インターフェイス名が変更されます。 この変更では、VM 作成時の一意のリソースが維持されます。 VM は可用性セットには追加されません。

ディスクの復元オプションは、次のような場合に使用します。
  * 作成された VM をカスタマイズする。 たとえば、サイズを変更します。
  * バックアップ時に存在しなかった構成設定を追加する
  * 作成されたリソースの名前付け規則を制御する。
  * VM を可用性セットに追加する。
  * PowerShell またはテンプレートを使用して構成する必要があるその他の設定を追加する  。

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>マネージド ディスクへのアップグレード後に、アンマネージド VM ディスクのバックアップを復元できますか。
はい。ディスクをアンマネージドからマネージドに移行する前に作成したバックアップを使用できます。
- 既定では、VM 復元ジョブによって、アンマネージド VM が作成されます。
- ただし、ディスクを復元して、それをマネージド VM の作成に使用することもできます。

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>VM がマネージド ディスクに移行されたときよりも前の復元ポイントにその VM を復元するには、どうすればよいですか。
既定では、VM の復元ジョブでは、アンマネージド ディスクで VM が作成されます。 マネージド ディスクで VM を作成するには、次の手順に従います。
1. [アンマネージド ディスクに復元します](tutorial-restore-disk.md#restore-a-vm-disk)。
2. [復元したディスクをマネージド ディスクに変換します](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)。
3. [マネージド ディスクで VM を作成します](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)。

PowerShell でこれを行う方法の詳細については、[こちら](backup-azure-vms-automation.md#restore-an-azure-vm)をご覧ください。

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>削除された VM を復元できますか。
はい。 VM を削除しても、コンテナー内の対応するバックアップ項目に移動して、復旧ポイントから復元できます。

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>同じ可用性セットに VM を復元するには、どうすればよいですか。
マネージド ディスクの Azure VM については、マネージド ディスクとして復元しているときに、可用性セットへの復元オプションがテンプレートに提供されます。 このテンプレートには、**可用性セット**と呼ばれる入力パラメーターがあります。

### <a name="how-do-we-get-faster-restore-performances"></a>復元のパフォーマンスを高めるには、どうすればよいですか。
復元のパフォーマンスを高めるには、VM バックアップ スタック V2 に移行して、[インスタント RP 機能](backup-upgrade-to-vm-backup-stack-v2.md)を使用することをお勧めします。

## <a name="manage-vm-backups"></a>VM バックアップの管理

### <a name="what-happens-if-i-modify-a-backup-policy"></a>バックアップ ポリシーを変更した場合は、どのようになりますか。
VM のバックアップは、変更されたポリシーまたは新しいポリシーのスケジュールおよびリテンション期間の設定を使用して実行されます。

- リテンション期間が延長された場合、既存の復旧ポイントは、新しいポリシーに従ってマーキングおよび保持されます。
- リテンション期間が短縮された場合、復旧ポイントは、次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Azure Backup でバックアップされた VM を別のリソース グループに移動するには、どうすればよいですか。

1. バックアップを一時的に停止し、バックアップ データを保持します。
2. VM をターゲット リソース グループに移動します。
3. バックアップを同じコンテナーまたは新しいコンテナーで再度有効にします。

VM は、移動操作の前に作成された使用可能な復元ポイントから復元できます。
