---
title: Azure Files のバックアップに関する FAQ
description: この記事では、Azure Backup サービスを使用して Azure ファイル共有を保護する方法に関してよく寄せられる質問への回答を示します。
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105643"
---
# <a name="questions-about-backing-up-azure-files"></a>Azure Files のバックアップに関する質問

この記事では、Azure Files のバックアップについてよくある質問への回答を示します。 一部の回答は、より詳しい情報を扱った記事にリンクされています。 また、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

この記事の各セクションの内容をひととおり確認するには、右側の「**この記事の内容**」にあるリンクをご利用ください。

## <a name="configuring-the-backup-job-for-azure-files"></a>Azure Files のバックアップ ジョブの構成

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>有効な Azure ファイル共有を含む、保護したいストレージ アカウントの一部が表示されないのはなぜですか?

ストレージ アカウントがサポートされているストレージ アカウントの種類のいずれかに属していることを確実にするために、「[Azure ファイル共有のバックアップのサポート マトリックス](azure-file-share-support-matrix.md)」を参照してください。 探しているストレージ アカウントが既に保護されている、または別のコンテナーに登録されている可能性もあります。 コンテナーから[ストレージ アカウントの登録を解除](manage-afs-backup.md#unregister-a-storage-account)して、保護のために他のコンテナー内のストレージ アカウントを検出します。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>バックアップを構成しようとしているときに、ストレージ アカウントに Azure ファイル共有の一部が表示されないのはなぜですか。

その Azure ファイル共有が既に同じ Recovery Services コンテナー内で保護されているかどうかと、その Azure ファイル共有を最近削除したかどうかを確認してください。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Azure Files Sync で同期グループに接続されているファイル共有を保護することはできますか?

はい。 同期グループに接続されている Azure ファイル共有の保護は有効になっています。

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>ファイル共有をバックアップしようと、ストレージ アカウント内のファイル共有を検出するために、そのストレージ アカウントをクリックしました。 しかし、それらを保護することはしませんでした。 これらのファイル共有を他のコンテナーで保護するにはどうすればよいのですか?

バックアップを試みる際、ストレージ アカウントを選択して、その中からファイル共有を検出すると、これを行ったストレージ アカウントがコンテナーに登録されます。 別のコンテナーでファイル共有を保護する場合は、このコンテナーから選択したストレージ アカウントを[登録解除](manage-afs-backup.md#unregister-a-storage-account)してください。

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>自分のファイル共有のバックアップ先コンテナーを変更することはできますか?

はい。 しかし、接続されているコンテナーからの[ファイル共有の保護を停止](manage-afs-backup.md#stop-protection-on-a-file-share)し、このストレージ アカウントを[登録解除](manage-afs-backup.md#unregister-a-storage-account)したうえで、別のコンテナーから保護する必要があります。

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>コンテナーで保護できる Azure ファイル共有はいくつですか。

コンテナーにつき最大 50 個のストレージ アカウントから Azure ファイル共有を保護できます。 また、1 つのコンテナーで保護できる Azure ファイル共有は最大 200 個です。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>同じストレージ アカウントにある 2 つの異なるファイル共有を別々のコンテナーで保護することはできますか?

いいえ。 1 つのストレージ アカウントに存在するすべてのファイル共有は、必ず同じコンテナーで保護する必要があります。

## <a name="backup"></a>バックアップ

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>上限に達したエラーのためにバックアップが失敗し始めた場合はどうすればよいですか?

どの時点でも、ファイル共有のスナップショットを 200 個まで作成することができます。 この制限には、ポリシーの定義に従って Azure Backup により作成されたスナップショットの数も含まれます。 この制限に達した後でバックアップが失敗するようになったら、将来のバックアップを正常に実行できるよう、オンデマンド スナップショットを削除してください。

## <a name="restore"></a>復元

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>削除した Azure ファイル共有から復旧できますか。

Azure ファイル共有を削除するときは、削除されるバックアップの一覧が表示され、確認が要求されます。 現在、削除した Azure ファイル共有を復元することはできません。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Azure ファイル共有の保護を停止した場合、バックアップから復元することはできますか。

はい。 保護を停止するときに **[バックアップ データの保持]** を選択した場合は、既存のすべての復元ポイントから復元できます。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>進行中の復元ジョブをキャンセルした場合、どうなりますか。

進行中の復元ジョブがキャンセルされると、復元プロセスが停止され、すべてのファイルがキャンセル前に復元されます。ロールバックされずに、構成された変換先 (元の場所または別の場所) は保持されます。

## <a name="manage-backup"></a>バックアップの管理

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>PowerShell を使用して、Azure ファイル共有のバックアップを構成/管理/復元できますか。

はい。 詳細なドキュメントを[こちら](backup-azure-afs-automation.md)で参照してください

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Azure Backup によって作成されたスナップショットにアクセスしてマウントできますか?

Azure Backup によって作成されたスナップショットはすべて、ポータル、PowerShell、または CLI でスナップショットを表示することでアクセスできます。 Azure Files 共有スナップショットの詳細については、「[Azure Files の共有スナップショットの概要](../storage/files/storage-snapshots-files.md)」を参照してください。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>バックアップについて構成できる保持期間の上限はどの位ですか?

最大保有期間の詳細については、[サポート マトリックス](azure-file-share-support-matrix.md)を参照してください。 バックアップ ポリシーの構成中に保有期間の値を入力すると、Azure Backup によってスナップショットの数がリアルタイムで計算されます。 定義された保有期間の値に対応するスナップショットの数が 200 を超えるとすぐに、ポータルには保有期間の値を調整するよう要求する警告が表示されます。 これは、任意の時点で任意のファイル共有に対して Azure Files でサポートされるスナップショットの最大数の上限を超えないようにするためです。

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Azure ファイル共有のバックアップ ポリシーを変更した場合はどうなりますか。

新しいポリシーをファイル共有に適用すると、新しいポリシーのスケジュールと保持期間が適用されます。 リテンション期間が延長された場合、既にある復旧ポイントは、新しいポリシーに従って保存するようにマーキングされます。 リテンション期間が短縮された場合、次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。

## <a name="next-steps"></a>次のステップ

Azure Backup のその他の領域の詳細については、これらの Backup に関する他の FAQ を参照してください。

- [Recovery Services コンテナーの FAQ](backup-azure-backup-faq.md)
- [Azure VM バックアップの FAQ](backup-azure-vm-backup-faq.md)
- [Azure Backup エージェントの FAQ](backup-azure-file-folder-backup-faq.md)
