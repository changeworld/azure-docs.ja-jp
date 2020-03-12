---
title: FAQ - Azure VM 上の SAP HANA データベースのバックアップ
description: この記事では、Azure Backup サービスを使用した SAP HANA データベースのバックアップに関する一般的な質問への回答を示します。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: d9d10e38885ba814045d8476b83671153feb7b8c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919687"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>よく寄せられる質問 - Azure VM 上の SAP HANA データベースをバックアップする

この記事では、Azure Backup サービスを使用した SAP HANA データベースのバックアップに関する一般的な質問への回答を示します。

## <a name="backup"></a>バックアップ

### <a name="how-many-full-backups-are-supported-per-day"></a>1 日あたり何回の完全バックアップがサポートされていますか?

サポートされている完全バックアップは 1 日 1 回のみです。 同じ日に差分バックアップと完全バックアップをトリガーすることはできません。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功したバックアップ ジョブでアラートが作成されますか?

いいえ。 成功したバックアップ ジョブではアラートは生成されません。 アラートは、失敗したバックアップ ジョブに対してのみ送信されます。 ポータル アラートの詳細な動作は[ここ](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)に記載されています。 ただし、成功したジョブに対してもアラートを生成することに関心がある場合は、[Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor) を使用できます。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>スケジュールされたバックアップ ジョブを [バックアップ ジョブ] メニューで確認できますか?

[バックアップ ジョブ] メニューには、アドホック バックアップ ジョブしか表示されません。 スケジュールされたジョブの場合は、[Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor) を使用します。

### <a name="are-future-databases-automatically-added-for-backup"></a>今後作成されるデータベースはバックアップに自動的に追加されますか?

いいえ、これは現在サポートされていません。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>インスタンスからデータベースを削除した場合、バックアップはどうなりますか?

SAP HANA インスタンスからデータベースが削除された場合でも、データベース バックアップは引き続き試行されます。 これは、削除されたデータベースが **[バックアップ項目]** に異常として表示され始め、引き続き保護されていることを示しています。
このデータベースの保護を停止するための正しい方法は、このデータベースに対して "**バックアップを停止してデータを削除する**" を実行することです。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>データベースが保護された後にその名前を変更した場合、その動作はどうなりますか?

名前が変更されたデータベースは、新しいデータベースとして処理されます。 このため、サービスはこの状況を、データベースが見つからず、バックアップが失敗したかのように処理します。 名前が変更されたデータベースは新しいデータベースとして表示されるため、保護するように構成する必要があります。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Azure VM 上の SAP HANA データベースをバックアップするための前提条件は何ですか?

「[前提条件](tutorial-backup-sap-hana-db.md#prerequisites)」セクションと[事前登録スクリプトの内容](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)に関するセクションを参照してください。

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Azure で SAP HANA データベースをバックアップできるようにするには、どのようなアクセス許可を設定する必要がありますか?

事前登録スクリプトを実行すると、Azure で SAP HANA データベースをバックアップできるようにするために必要なアクセス許可が設定されます。 事前登録スクリプトで実行される処理の詳細については、[こちら](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)をご覧ください。

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>SAP HANA を 1.0 から 2.0 に移行した後、バックアップは機能しますか?

トラブルシューティング ガイドの[こちらのセクション](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)をご覧ください。

## <a name="restore"></a>復元

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>データベースの復元先の HANA システムが表示されないのはなぜですか?

ターゲットの SAP HANA インスタンスに復元するためのすべての前提条件が満たされているかどうかを確認してください。 詳しくは、[Azure VM の SAP HANA データベースの復元の前提条件](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)に関する記事をご覧ください。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>データベースで DB の上書き復元が失敗するのはなぜですか?

復元の間に **[強制的に上書きします]** オプションが選択されていることを確認します。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>"復元のソース システムとターゲット システムには互換性がありません" エラーが表示されるのはなぜですか?

SAP HANA ノート [1642148](https://launchpad.support.sap.com/#/notes/1642148) を参照し、現在サポートされている復元の種類を確認してください。

## <a name="next-steps"></a>次のステップ

Azure VM で実行されている [SAP HANA データベースをバックアップする](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)方法を学習します。
