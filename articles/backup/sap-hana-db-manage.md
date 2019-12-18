---
title: Azure VM 上のバックアップされた SAP HANA データベースを管理する
description: この記事では、Azure 仮想マシン上で実行されている SAP HANA データベースを管理および監視するための一般的なタスクについて説明します。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: f76054c7c78c55a9754975267ee4fa3caab968a3
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287521"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>バックアップされた SAP HANA データベースを管理および監視する

この記事では、Azure 仮想マシン (VM) 上で実行され、[Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) サービスによって Azure Backup Recovery Services コンテナーにバックアップされた SAP HANA データベースを管理および監視するための一般的なタスクについて説明します。 ジョブやアラートを監視したり、オンデマンド バックアップをトリガーしたり、ポリシーを編集したり、データベースの保護を停止および再開したり、バックアップから VM を登録解除したりする方法について説明します。

SAP HANA データベースのバックアップをまだ構成していない場合は、「[Azure VM での SAP HANA データベースのバックアップ](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)」を参照してください。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>ポータルで手動バックアップ ジョブを監視する

Azure Backup では、手動でトリガーされたすべてのジョブが Azure portal の **[バックアップ ジョブ]** セクションに表示されます。

![[バックアップ ジョブ] セクション](./media/sap-hana-db-manage/backup-jobs.png)

このポータルに表示されるジョブには、データベースの検出と登録、バックアップ操作、復元操作などがあります。 スケジュールされたジョブ (ログ バックアップを含む) はこのセクションに表示されません。 また、SAP HANA ネイティブ クライアント (Studio/Cockpit/DBA Cockpit) から手動でトリガーされたバックアップもここには表示されません。

![[バックアップ ジョブ] 一覧](./media/sap-hana-db-manage/backup-jobs-list.png)

監視の詳細については、[Azure portal での監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)に関するページおよび [Azure Monitor を使用した監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)に関するページを参照してください。

## <a name="view-backup-alerts"></a>バックアップ アラートを表示する

アラートは、SAP HANA データベースのバックアップを監視するための簡単な手段です。 アラートは、バックアップによって生成される多数のイベントに惑わされることなく、最も関心があるイベントに焦点を絞るために役立ちます。 Azure Backup ではアラートを設定することができ、それを次のように監視できます。

* [Azure Portal](https://portal.azure.com/) にサインインします。
* コンテナー ダッシュボードで、 **[バックアップ アラート]** を選択します。

  ![コンテナー ダッシュボードの [バックアップ アラート]](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* アラートが表示されます。

  ![バックアップ アラートの一覧](./media/sap-hana-db-manage/backup-alerts-list.png)

* アラートをクリックすると、その詳細が表示されます。

  ![[アラートの詳細]](./media/sap-hana-db-manage/alert-details.png)

Azure Backup では現在、アラートを電子メール経由で送信できます。 これらのアラートは次のように処理されます。

* すべてのバックアップ エラーに対してトリガーされます。
* エラー コードごとにデータベース レベルで統合されます。
* データベースの最初のバックアップ エラーに対してのみ送信されます。

監視の詳細については、[Azure portal での監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)に関するページおよび [Azure Monitor を使用した監視](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)に関するページを参照してください。

## <a name="management-operations"></a>管理操作

Azure Backup では、サポートされる管理操作が豊富なため、バックアップされた SAP HANA データベースの管理が簡単になります。 以降のセクションでは、これらの操作について詳細に説明します。

### <a name="run-an-ad-hoc-backup"></a>アドホック バックアップの実行

バックアップは、ポリシー スケジュールに従って実行されます。 次のように、バックアップ オンデマンを実行できます。

1. コンテナー メニューで **[バックアップ アイテム]** をクリックします。
2. **[バックアップ項目]** で、SAP HANA データベースを実行している VM を選択し、 **[今すぐバックアップ]** をクリックします。
3. **[今すぐバックアップ]** で、カレンダー コントロールを使用して復旧ポイントを保持する最終日を選択します。 次に、 **[OK]** をクリックします
4. ポータルの通知を監視します。 コンテナー ダッシュボードの **[バックアップ ジョブ]**  >  **[進行中]** でジョブの進行状況を監視できます。 データベースのサイズによっては、最初のバックアップの作成に時間がかかる場合があります。

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Azure Backup が有効になっているデータベースで SAP HANA ネイティブ クライアント バックアップを実行する

Azure Backup でバックアップされているデータベースの (HANA Studio/Cockpit を使用した) ローカル バックアップを取得する場合は、次の操作を行います。

1. データベースの完全バックアップまたはログ バックアップがすべて完了するまで待ちます。 SAP HANA Studio/Cockpit で状態を確認します。
2. ログ バックアップを無効にし、関連するデータベースのファイル システムにバックアップ カタログを設定します。
3. これを行うには、**systemdb** >  **[構成]**  >  **[データベースの選択]**  >  **[Filter (Log)]\(フィルター (ログ)\)** の順にダブルクリックします。
4. **[enable_auto_log_backup]** を **[No]** に設定します。
5. **[log_backup_using_backint]** を **[False]** に設定します。
6. データベースのアドホック完全バックアップを作成します。
7. 完全バックアップとカタログ バックアップが完了するまで待ちます。
8. 前の設定を Azure のものに戻します。
   * **[enable_auto_log_backup]** を **[Yes]** に設定します。
   * **[log_backup_using_backint]** を **[True]** に設定します。

### <a name="edit-underlying-policy"></a>基になるポリシーを編集する

バックアップ頻度や保有期間の範囲を変更するにはポリシーを変更します。

* コンテナー ダッシュボードで、 **[管理]**  >  **[バックアップ ポリシー]** の順に移動します。

  ![コンテナー ダッシュボードの [バックアップ ポリシー]](./media/sap-hana-db-manage/backup-policies-dashboard.png)

* 編集するポリシーを選択します。

  ![バックアップ ポリシーの一覧](./media/sap-hana-db-manage/backup-policies-list.png)

  ![バックアップ ポリシーの詳細](./media/sap-hana-db-manage/backup-policy-details.png)

>[!NOTE]
> 保持期間の変更は、新しい復旧ポイントだけでなく古い復旧ポイントすべてにもさかのぼって適用されます。
>
> SAP HANA データベースには増分バックアップ ポリシーを使用できません。 増分バックアップは現在、これらのデータベースではサポートされていません。

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA データベースの保護を停止する

SAP HANA データベースの保護は、次のいくつかの方法で停止できます。

* 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する。
* 今後のすべてのバックアップ ジョブを停止し、復旧ポイントはそのままにする。

復旧ポイントをそのままにする場合、以下の項目を念頭に置いてください。

* 復旧ポイントはすべて永久に変更されず、削除はすべて保護の停止で停止し、データを保持します
* インスタンスの保護とストレージの使用に対して課金されます。 詳細については、「[Azure Backup の価格](https://azure.microsoft.com/pricing/details/backup/)」をご覧ください。
* バックアップを停止しないでデータ ソースを削除すると、新しいバックアップは失敗します。

データベースの保護を停止するには、次の手順を実行します。

* コンテナー ダッシュボードで **[バックアップ項目]** を選択します。
* **[バックアップの管理の種類]** で、 **[Azure VM の SAP HANA]** を選択します。

  ![[Azure VM の SAP HANA] を選択する](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 保護を停止するデータベースを選択します。

  ![保護を停止するデータベースを選択する](./media/sap-hana-db-manage/select-database.png)

* データベース メニューで、 **[バックアップの停止]** を選択します。

  ![[バックアップの停止] を選択する](./media/sap-hana-db-manage/stop-backup.png)

* **[バックアップの停止]** メニューで、データを保持するか削除するかを選択します。 必要に応じて、理由とコメントを入力します。

  ![データの保持または削除を選択する](./media/sap-hana-db-manage/retain-backup-data.png)

* **[バックアップの停止]** を選択する

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA データベースの保護を再開する

SAP HANA データベースの保護を停止するときに **[バックアップ データの保持]** オプションを選択した場合は、後で保護を再開できます。 バックアップされたデータを保持しない場合は、保護を再開できなくなります。

SAP HANA データベースの保護を再開するには:

* バックアップ項目を開き、 **[バックアップの再開]** を選択します。

   ![[バックアップの再開] を選択する](./media/sap-hana-db-manage/resume-backup.png)

* **[バックアップ ポリシー]** メニューで、ポリシーを選択して、 **[保存]** を選択します。

### <a name="upgrading-from-sap-hana-10-to-20"></a>SAP HANA 1.0 から 2.0 へのアップグレード

[SAP HANA 1.0 から 2.0 へのアップグレードの後に](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20) SAP HANA データベースのバックアップを続行する方法について学習してください。

### <a name="upgrading-without-a-sid-change"></a>SID の変更なしでのアップグレード

[アップグレードの後に SID がまだ変更されていない](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change) SAP HANA データベースのバックアップを続行する方法について学習してください。

### <a name="unregister-an-sap-hana-database"></a>SAP HANA データベースを登録解除する

SAP HANA インスタンスの登録解除は、保護を無効にした後、コンテナーを削除する前に行います。

* コンテナー ダッシュボードの **[管理]** で、 **[バックアップ インフラストラクチャ]** を選択します。

   ![[バックアップ インフラストラクチャ] を選択する](./media/sap-hana-db-manage/backup-infrastructure.png)

* **[バックアップの管理の種類]** を **[Workload in Azure VM] (Azure VM のワークロード)** として選択します。

   ![[バックアップの管理の種類] を [Workload in Azure VM] (Azure VM のワークロード) として選択する](./media/sap-hana-db-manage/backup-management-type.png)

* **[保護されたサーバー]** で、登録解除するインスタンスを選択します。 コンテナーを削除するには、すべてのサーバー/インスタンスを登録解除する必要があります。

* 保護されたインスタンスを右クリックし、 **[登録解除]** を選択します。

   ![[登録解除] を選択する](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>次の手順

* [SAP HANA データベースをバックアップする場合の一般的な問題をトラブルシューティングする](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)方法について学習します。

