---
title: Azure VM での SAP HANA データベースバックアップについて
description: この記事では、Azure 仮想マシン上で実行されている SAP HANA データベースをバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: a1be572d6462ed8f8a86db0f5cbfeaaa37c219ab
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586565"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Azure VM での SAP HANA データベースバックアップについて

SAP HANA データベースは、復旧ポイント目標 (RPO) と目標復旧時間を (RTO) の必要性が低い極めて重要なワークロードです。 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) を使用して、[Azure VM 上で実行されている SAP HANA データベースをバックアップ](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db)できるようになりました。

Azure Backup は、SAP による [Backint 認定](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) がされています。これにより、SAP HANA のネイティブ API を活用したネイティブバックアップサポートを提供します。 Azure Backup は、Azure Backup の **ゼロインフラストラクチャ** バックアップの理念に沿っており、バックアップ インフラストラクチャのデプロイおよび管理を軽減します。 Azure VＭ 上で実行されている SAP HANA データベースをシームレスにバックアップおよび復元できるようになりました ([M シリーズの VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory) もサポートされるようになりました！)。また、Azure Backup が提供するエンタープライズ管理機能を活用できます。

## <a name="added-value"></a>付加価値

Azure Backup を使用して SAP HANA データベースをバックアップおよび復元することには、次の利点があります：

* **15 分の復旧ポイント目標 (RPO)** :最大15分間までの重要なデータの回復が可能になりました。
* **ワンクリックで、特定の時点への復元をする**：運用データを、別の HANA サーバーに復元するのが簡単です。 バックアップとカタログのチェーン化による復元の実行は、すべて Azure によってバックグラウンドで管理されます。
* **長期保存**：厳格な準拠と監査のニーズに対応しています。 保有期間に基づいてバックアップを何年も保持し、復旧期間を超えると、組み込みのライフサイクル管理機能によって、自動的に削除されます。
* **Azure からのバックアップ管理サーバー**：Azure Backup の管理および監視機能を使用して、管理エクスペリエンスが向上します。 Azure CLI もサポートされています。

現在サポートされているバックアップと復元のシナリオを確認するには、[SAP HANA シナリオのサポートマトリックス](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) を参照してください。

## <a name="backup-architecture"></a>Backup のアーキテクチャ

![Backup アーキテクチャの図](./media/sap-hana-db-about/backup-architecture.png)

* バックアッププロセスは、Azure で [Recovery services コンテナー](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) を作成することによって開始されます。 このコンテナーは、時間の経過と共に作成されたバックアップと復元ポイントを格納するために使用されます。
* SAP HANA サーバーを実行している Azure VM がコンテナーに登録されており、バックアップされるデータベースが [検出](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases) されます。 Azure Backup サービスでデータベースを検出できるようにするには、[事前登録スクリプト](https://aka.ms/scriptforpermsonhana) を HANA サーバーで、root ユーザーとして実行する必要があります。
* このスクリプトは **AZUREWLBACKUPHANAUSER** DB ユーザー　を作成し、**hdbuserstore**に同じ名前の対応するキーを作成します。 スクリプトの詳細を解釈するには、「[権限の設定](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions)」のセクションを参照してください。
* Azure Backup サービスは、登録されている SAP HANA サーバー上に、 **HANA 用の Azure Backup Plugin** をインストールするようになりました。
* 事前登録スクリプトによって作成された **AZUREWLBACKUPHANAUSER** DB ユーザーは、**HANA の Microsoft Azure Backup プラグイン**によって使用され、すべてのバックアップおよび復元操作を実行するために使用されます。 このスクリプトを実行せずに SAP HANA Db のバックアップを構成しようとすると、次のエラーが表示される場合があります：**UserErrorHanaScriptNotRun**.
* 検出されたデータベースで [バックアップを構成](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) するには、必要なバックアップポリシーを選択し、バックアップを有効にする必要があります。

* バックアップが構成されると、Azure Backup サービスは、保護された SAP HANA サーバーの DATABASE レベルで、次の Backint パラメーターを設定します：
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>これらのパラメーターが、 HOST レベルに存在 *しない* ことを確認してください。 ホスト レベルのパラメーターによってこれらのパラメーターがオーバーライドされるため、予期しない動作が発生することがあります。
>

* **HANA 用の Azure Backup Plugin** は、すべてのバックアップスケジュールとポリシーの詳細を保持します。 それは、スケジュールされたバックアップをトリガーし、Backint API を介して **HANA Backup Engine** と通信します。
* **HANA Backup Engine** は、バックアップするデータを含む Backint ストリームを返します。
* 完全または差分のすべてのスケジュールされたバックアップとオンデマンドバックアップ (Microsoft Azure portal からトリガーされた) は、 **HANA 用の Azure Backup Plugin** によって開始されます。 ただし、ログバックアップは **HANA Backup Engine** によって管理およびトリガーされます。

## <a name="next-steps"></a>次のステップ

* [Azure VM で稼働している SAP HANA データベースを復元する](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)方法について学習する
* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)方法を学習する
