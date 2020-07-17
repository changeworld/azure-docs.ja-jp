---
title: Azure VM 上の SAP HANA データベースの復元
description: この記事では、Azure Virtual Machines 上で実行されている SAP HANA データベースを復元する方法について説明します。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287389"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Azure VM 上の SAP HANA データベースの復元

この記事では、Azure 仮想マシン (VM) 上で実行されていて、Azure Backup サービスによって Azure Backup Recovery Services コンテナーにバックアップされた SAP HANA データベースを復元する方法について説明します。 復元を使用すると、開発/テスト シナリオ用にデータのコピーを作成することや、以前の状態に戻すことができます。

SAP HANA データベースのバックアップ方法について詳しくは、「[Azure VM 上の SAP HANA データベースのバックアップ](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)」を参照してください。

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>特定の時点 (ポイントインタイム) または復旧ポイントに復元する

Azure Backup は、Azure VM 上で実行されている SAP HANA データベースを次のように復元できます。

* ログ バックアップを使用して、特定の日付または時刻 (秒) に復元する。 選択された時刻を基に、Azure Backup は完全、差分、または一連のログ バックアップから、必要になる適切なバックアップを自動的に判別します。

* 特定の完全バックアップまたは差分バックアップに復元することで、特定の復旧ポイントに復元する。

## <a name="prerequisites"></a>前提条件

データベースを復元する前に、次のことに注意してください。

* データベースの復元先にできるのは、同じリージョン内の SAP HANA インスタンスのみです。

* ターゲット インスタンスは、ソースと同じコンテナーに登録されている必要があります。

* Azure Backup は、同じ VM 上にある 2 つの異なる SAP HANA インスタンスを識別できません。 そのため、同じ VM 上の一方のインスタンスから他方のインスタンスにデータを復元することはできません。

* ターゲットの SAP HANA インスタンスが復元の準備ができているか確認するには、そのインスタンスの**バックアップの準備**状態を確認します。

  * ターゲット SAP HANA インスタンスが登録されているコンテナーを開きます。

  * コンテナー ダッシュボードで、 **[作業の開始]** の下にある **[バックアップ]** を選択します。

![コンテナー ダッシュボードでのバックアップ](media/sap-hana-db-restore/getting-started-backup.png)

* **[バックアップ]** で、 **[何をバックアップしますか?]** の下の **[Azure VM の SAP HANA]** を選択します。

![[Azure VM の SAP HANA] を選択する](media/sap-hana-db-restore/sap-hana-backup.png)

* **[VM 内のデータベースを検出]** で **[詳細の表示]** をクリックします。

![詳細を表示する](media/sap-hana-db-restore/view-details.png)

* ターゲット VM の **[バックアップの準備]** を確認します。

![保護されたサーバー](media/sap-hana-db-restore/protected-servers.png)

* SAP HANA がサポートする復元の種類について詳しくは、SAP HANA ノート [1642148](https://launchpad.support.sap.com/#/notes/1642148) を参照してください。

## <a name="restore-a-database"></a>データベースを復元する

* 復元する SAP HANA データベースが登録されているコンテナーを開きます。

* コンテナー ダッシュボードで、 **[保護された項目]** の下の **[バックアップ項目]** を選択します。

![[バックアップ項目]](media/sap-hana-db-restore/backup-items.png)

* **[バックアップ項目]** で、 **[バックアップの管理の種類]** の下の **[Azure VM の SAP HANA]** を選択します。

![バックアップの管理の種類](media/sap-hana-db-restore/backup-management-type.png)

* 復元するデータベースを選択します。

 ![復元するデータベース](media/sap-hana-db-restore/database-to-restore.png)

* データベース メニューを確認します。 ここには、次のような、データベース バックアップに関する情報が表示されます。

  * 最古および最新の復元ポイント

  * データベースの過去 24 時間と過去 72 時間のログ バックアップの状態

![データベース メニュー](media/sap-hana-db-restore/database-menu.png)

* **[DB の復元]** を選択します。

* **[復元の構成]** で、データの復元先 (または復元方法) を指定します。

  * **別の場所**:別の場所にデータベースを復元し、元のソース データベースを保持します。

  * **DB の上書き**:元のソースと同じ SAP HANA インスタンスにデータを復元します。 このオプションでは、元のデータベースが上書きされます。

![復元の構成](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>別の場所に復元する

* **[復元の構成]** メニューの **[復元先]** で、 **[別の場所]** を選択します。

![別の場所に復元する](media/sap-hana-db-restore/restore-alternate-location.png)

* データベースを復元する先の SAP HANA のホスト名とインスタンス名を選択します。
* **[バックアップの準備]** を確認することで、ターゲット SAP HANA インスタンスが復元の準備ができているかどうか確認します。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。
* **[復元される DB 名]** ボックスに、ターゲット データベースの名前を入力します。

> [!NOTE]
> SDC (Single Database Container) の復元では、[確認事項](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)に従う必要があります。

* 該当する場合は、 **[Overwrite if the DB with the same name already exists on selected HANA instance]\(選択した HANA インスタンスに既に同じ名前の DB が存在する場合に上書きする\)** を選択します。
* **[OK]** を選択します。

![復元の構成 - 最後の画面](media/sap-hana-db-restore/restore-configuration-last.png)

* **[復元ポイントの選択]** で、[特定の時点に復元する](#restore-to-a-specific-point-in-time) **[ログ (特定の時点)]** を選択します。 または、 **[完全および差分]** を選択して、[特定の復旧ポイントに復元](#restore-to-a-specific-recovery-point)します。

### <a name="restore-and-overwrite"></a>復元および上書き

* **[復元の構成]** メニューの **[復元先]** で、 **[DB の上書き]**  > 、 **[OK]** の順に選択します。

![DB の上書き](media/sap-hana-db-restore/overwrite-db.png)

* **[復元ポイントの選択]** で、[特定の時点に復元する](#restore-to-a-specific-point-in-time) **[ログ (特定の時点)]** を選択します。 または、 **[完全および差分]** を選択して、[特定の復旧ポイントに復元](#restore-to-a-specific-recovery-point)します。

### <a name="restore-to-a-specific-point-in-time"></a>特定の時点に復元する

復元の種類として **[ログ (特定の時点)]** を選択した場合は、次の操作を行います。

* ログ グラフから復旧ポイントを選択し、 **[OK]** を選択して復元ポイントを選択します。

![復元ポイント](media/sap-hana-db-restore/restore-point.png)

* **[復元]** メニューで、 **[復元]** を選択して復元ジョブを開始します。

![[復元] を選択する](media/sap-hana-db-restore/restore-restore.png)

* **[通知]** 領域で復元の進行状況を追跡します。またはデータベース メニューの **[復元ジョブ]** を選択して、復元の進行状況を追跡します。

![正常にトリガーされた復元](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>特定の復旧ポイントに復元する

復元の種類として **[完全および差分]** を選択した場合は、次の操作を行います。

* 一覧から復旧ポイントを選択し、 **[OK]** を選択して復元ポイントを選択します。

![特定の復旧ポイントの復元](media/sap-hana-db-restore/specific-recovery-point.png)

* **[復元]** メニューで、 **[復元]** を選択して復元ジョブを開始します。

![復元ジョブの開始](media/sap-hana-db-restore/restore-specific.png)

* **[通知]** 領域で復元の進行状況を追跡します。またはデータベース メニューの **[復元ジョブ]** を選択して、復元の進行状況を追跡します。

![復元の進行状況](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> Multiple Database Container (MDC) の復元では、システム DB がターゲット インスタンスに復元された後で、事前登録スクリプトを再実行する必要があります。 その後でのみ、後続のテナント DB の復元が成功します。 詳細については、[トラブルシューティング – MDC 復元](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](sap-hana-db-manage.md)方法を学習する
