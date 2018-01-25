---
title: "Azure VM バックアップの FAQ | Microsoft Docs"
description: "一般的な質問への回答: Azure VM バックアップの動作、制限、ポリシーの変更があったときに起こること"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "azure vm バックアップ, azure vm 復元, バックアップ ポリシー"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: f69cbbab19acbc4e71445012d262896275a7d768
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Azure VM バックアップ サービスについての質問
この記事では、Azure VM バックアップの構成要素が理解しやすいよう、よく寄せられる質問とその回答を記載しています。 一部の回答は、より詳しい情報を扱った記事にリンクされています。 また、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

## <a name="configure-backup"></a>バックアップの構成
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Recovery Services コンテナーでサポートされるのはクラシック VM と Resource Manager ベースの VM のどちらですか。 <br/>
Recovery Services コンテナーでは両方のモデルがサポートされています。  (クラシック ポータルで作成された) クラシック VM または (Azure Portal で作成された) Resource Manager VM を、Recovery Services コンテナーにバックアップできます。

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Azure VM バックアップでサポートされない構成は、どのようなものですか。
[サポートされるオペレーティング システム](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup)と [VM のバックアップの制限](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)に関するページをご覧ください。

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>バックアップ構成ウィザードに VM が表示されません。なぜでしょうか。
バックアップの構成ウィザードで Azure Backup の対象として表示されるのは、次に該当する VM だけです。
  * まだ保護されていない (VM のバックアップ状態は、VM ブレードに移動し、設定メニューの [バックアップ状態] を見て確認できます。) 詳細については、[VM のバックアップ状態の確認](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)方法に関するページを参照してください。
  * VM と同じリージョンに属している

## <a name="backup"></a>Backup
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>オンデマンド バックアップ ジョブのリテンション期間スケジュールは、スケジュールされたバックアップと同じでしょうか。

いいえ。 オンデマンド バックアップ ジョブのリテンション期間はご自身で指定する必要があります。 ポータルからトリガーした場合、既定で 30 日間保存されます。 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>最近いくつかの VM で Azure Disk Encryption を有効にしました。 既存のバックアップは今後も正常に機能しますか。
Azure Backup サービスに Key Vault へのアクセス許可を与える必要があります。 PowerShell でこうしたアクセス許可を付与するには、[PowerShell](backup-azure-vms-automation.md) ドキュメントの "*バックアップの有効化*" に関するセクションに記載されている手順を使用します。

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>VM のディスクを Managed Disks に移行しました。 既存のバックアップは今後も正常に機能しますか。
はい。バックアップはシームレスに機能します。バックアップを再構成する必要はありません。 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>VM がシャットダウンされています。 オンデマンドまたはスケジュールされたバックアップは機能しますか。
はい。 コンピューターがシャットダウンされている場合でも、バックアップは機能します。復旧ポイントはクラッシュ コンシステントとしてマークされます。 詳細については、[リンク先の記事](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)の「データの一貫性」セクションをご覧ください

### <a name="can-i-cancel-an-in-progress-backup-job"></a>進行中のバックアップ ジョブを取り消すことはできますか。
はい。 "スナップショットの作成" フェーズの場合は、バックアップ ジョブを取り消すことができます。 **スナップショットからのデータ転送が進行中である場合は、ジョブを取り消すことができません**。 

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>バックアップされた管理ディスク VM でリソース グループのロックを有効にしました。 既存のバックアップは今後も正常に機能しますか。
ユーザーがリソース グループをロックすると、バックアップ サービスは古い復元ポイントを削除できません。 バックエンドから最大 18 個の復元ポイントの制限が課されているため、これにより新しいバックアップの開始が失敗します。 RG のロック後に内部エラーでバックアップが失敗した場合は、[手順に従って復元ポイントのコレクションを削除します](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)。

## <a name="restore"></a>Restore
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>ディスクの復元と完全 VM 復元とは、どのように使い分ければよいでしょうか。
Azure の完全 VM 復元を簡易的な作成方法の 1 つと考えてください。 [VM の復元] オプションでは、ディスク名、それらのディスクに使用されているコンテナー、パブリック IP アドレス、およびネットワーク インターフェイス名を変更します。 この変更は、VM の作成時に生成されたリソースの一意性を確保するために必要です。 ただし、VM は可用性セットには追加されません。 

ディスクの復元は、次のような場合に使用してください。
  * サイズを変更するなど、特定の時点の構成から作成された VM をカスタマイズする。
  * バックアップの時点では存在しなかった構成を追加する。 
  * 作成されるリソースの名前付け規則を制御する。
  * 可用性セットに VM を追加する。
  * PowerShell または宣言型のテンプレート定義でしか実現できない他の構成に対して使用する。
  
### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>ディスクを管理対象ディスクにアップグレードした後で、非管理対象ディスク VM のバックアップを使用して復元することはできますか。
はい、ディスクを非管理対象から管理対象に移行する前に作成したバックアップを使用することができます。 既定では、VM の復元ジョブは、非管理対象ディスクで VM を作成します。 ディスクの復元機能を使用して、ディスクを復元し、管理対象ディスク上の VM の作成に使用することができます。 

## <a name="manage-vm-backups"></a>VM バックアップの管理
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>VM のバックアップ ポリシーを変更した場合どうなりますか。
新しいポリシーを VMに適用すると、新しいポリシーのスケジュールとリテンション期間が適用されます。 リテンション期間が延長された場合、既にある復旧ポイントは、新しいポリシーに従って保存するようにマーキングされます。 リテンション期間が短縮された場合、次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。 

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Azure バックアップに登録された VM をリソース グループ間で移動するにはどうすればよいですか。
バックアップされた VM をターゲット リソース グループに正常に移動するには、次の手順に従います。 
1. バックアップを一時的に停止し、バックアップ データを保持する
2. VM をターゲット リソース グループに移動する
3. それを同じコンテナーまたは新しいコンテナーで再保護する

ユーザーは、移動操作の前に作成された使用可能な復元ポイントから復元できます。


