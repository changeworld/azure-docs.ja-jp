---
title: Azure Backup で保護されたワークロードの監視
description: この記事では、Azure portal を使用した Azure Backup ワークロードの監視機能と通知機能について説明します。
ms.topic: conceptual
ms.date: 11/02/2021
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: afc23090ce15621542ec6035c67d36b85a3414c1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439790"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup ワークロードの監視

Azure Backup では、バックアップ要件とインフラストラクチャ トポロジ (オンプレミスと Azure) に基づく複数のバックアップ ソリューションを提供します。 すべてのバックアップ ユーザーまたは管理者は、すべてのソリューション全体で何が起こっているかを確認し、重要なシナリオで通知を受け取ることができます。 この記事では、Azure Backup サービスによって提供される監視と通知の機能について説明します。

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Recovery Services コンテナーでのバックアップ項目

Recovery Services コンテナーを使用して、すべてのバックアップ項目を監視できます。 **バックアップ センター** の **[バックアップ インスタンス]** セクションに移動すると、指定されたワークロードの種類のすべてのバックアップ項目の詳細な一覧が表示され、各項目の最後のバックアップの状態、利用可能な最新の復元ポイントなどの情報が表示されます。

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-instances-inline.png" alt-text="Recovery Services コンテナーのバックアップ項目を表示するスクリーンショット。" lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-instances-expanded.png":::

>[!NOTE]
>DPM を使用して Azure にバックアップされた項目については、DPM サーバーを使用して保護されているすべてのデータソース (ディスクとオンラインの両方) が一覧に表示されます。 バックアップ データが保持されているデータソースの保護が停止した場合、データソースはポータルに表示されたままになります。 データ ソースの詳細にアクセスして、回復ポイントがディスク、オンライン、またはその両方に存在するかどうかを確認できます。 また、オンラインのデータ保護が停止されていてデータが保持されたデータソースの場合、データが完全に削除されるまで、オンライン回復ポイントの課金が継続されます。
>
> バックアップ項目を Recovery Services コンテナー ポータルに表示するには、DPM のバージョンが DPM 1807 (5.1.378.0) または DPM 2019 (バージョン 10.19.58.0 以降) である必要があります。

## <a name="backup-jobs-in-backup-center"></a>バックアップ センターのバックアップ ジョブ

Azure Backup では、Azure Backup で保護されているワークロード用の組み込みの監視とアラートの機能を提供します。 **バックアップ センター** の **[バックアップ ジョブ]** ウィンドウに移動すると、コンテナー全体の最新のバックアップ ジョブと復元ジョブを表示できます。

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-jobs-inline.png" alt-text="Recovery Services コンテナーの組み込み監視を示すスクリーンショット。" lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-jobs-expanded.png":::

ジョブは、バックアップの構成、バックアップ、復元、バックアップの削除などの操作が実行されるときに生成されます。

ここには、次の Azure Backup ソリューションからのジョブが表示されます。

- Azure VM バックアップ
- Azure ファイルのバックアップ
- SQL や SAP HANA などの Azure ワークロードのバックアップ
- Microsoft Azure Recovery Services (MARS) エージェント

System Center Data Protection Manager (SC-DPM)、Microsoft Azure Backup Server (MABS) からのジョブは表示されません。

> [!NOTE]
> Azure VM 内の SQL や SAP HANA のバックアップなどの Azure ワークロードには、膨大な数のバックアップ ジョブがあります。 たとえば、ログ バックアップは 15 分ごとに実行できます。 そのため、このようなデータベース ワークロードについては、ユーザーがトリガーした操作のみが表示されます。 スケジュールされたバックアップ操作は表示されません。

## <a name="backup-alerts-in-recovery-services-vault"></a>Recovery Services コンテナーでのバックアップ アラート

アラートは、主に、ユーザーが通知を受け取るシナリオであり、これによりユーザーは関連アクションを行うことができます。 **[バックアップ アラート]** セクションに、Azure Backup サービスによって生成されたアラートが表示されます。 これらのアラートはサービスによって定義され、ユーザーがカスタム アラートを作成することはできません。

### <a name="alert-scenarios"></a>アラート シナリオ

次のシナリオは、サービスによって警告可能シナリオとして定義されています。

- バックアップ/復元エラー
- バックアップの成功 (Microsoft Azure Recovery Services (MARS) エージェントに対する警告を含む)
- 保護の停止 (データの保持を含む)/保護の停止 (データの削除を含む)
- コンテナーの論理削除機能が無効
- データベース ワークロードに対してサポートされていないバックアップの種類

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>ここには、次の Azure Backup ソリューションからのアラートが表示されます。

- Azure VM バックアップ
- Azure ファイルのバックアップ
- SQL、SAP HANA などの Azure ワークロードのバックアップ
- Microsoft Azure Recovery Services (MARS) エージェント

> [!NOTE]
> System Center Data Protection Manager (SC-DPM)、Microsoft Azure Backup Server (MABS) からのアラートは表示されません。

### <a name="consolidated-alerts"></a>統合されたアラート

SQL や SAP HANA などの Azure ワークロード バックアップ ソリューションでは、ログ バックアップを非常に頻繁に (ポリシーに従って 15 分ごとに) 生成できます。 そのため、ログ バックアップ エラーも頻繁に発生する可能性があります (最大 15 分間隔)。 このシナリオで、エラーが発生するたびにアラートが発生した場合、エンド ユーザーの気が滅入ってしまいます。 そのため、最初の発生時にはアラートが送信され、それ以降のエラーが同じ根本原因である場合、それ以降のアラートは生成されません。 最初のアラートは、エラー数で更新されます。 しかし、アラートがユーザーによって非アクティブにされている場合は、次の発生時に別の電子メールがトリガーされ、これがその発生に対する最初のアラートとして扱われます。 このようにして Azure Backup は、SQL と SAP HANA のバックアップに対してアラートの統合を実行します。 オンデマンド バックアップ ジョブは統合されません。

### <a name="exceptions-when-an-alert-is-not-raised"></a>アラートが生成されない例外

失敗時にアラートが生成されない次のいくつかの例外があります。 これらは次のとおりです。

- ユーザーが実行中のジョブを明示的に取り消しました
- 別のバックアップ ジョブが処理中であるため、ジョブが失敗しました (前のジョブが完了するまで待つしかないため、行うことができる操作はありません)
- バックアップの Azure VM が存在しなくなっているため、VM バックアップ ジョブが失敗します
- [統合されたアラート](#consolidated-alerts)

上記の例外は、これらの操作 (主にユーザーがトリガーするもの) の結果がポータル/PS/CLI クライアントで即時に表示されるという理解に基づいて設計されています。 そのため、ユーザーはすぐにこれに気付くことから、通知は必要ありません。

### <a name="alert-types"></a>アラートの種類

アラートは、その重大度に基づいて次の 3 つの種類に定義できます。

- **[高]** :原則として、バックアップまたは回復が失敗すると (スケジュールされたかユーザーがトリガーしたかを問わず)、アラートが生成されて重大アラートとして表示され、バックアップの削除といった破壊的な操作の原因となります。
- **警告**:バックアップ操作が成功したもののいくつかの警告を伴う場合、これらは警告アラートとして表示されます。 警告アラートは現在、Azure Backup エージェントのバックアップにのみ使用できます。
- **情報**:現時点では、Azure Backup サービスで情報アラートは生成されません。

## <a name="notification-for-backup-alerts"></a>バックアップ アラートの通知

> [!NOTE]
> 通知の構成は、Azure portal を介してのみ実行できます。 PS/CLI/REST API/Azure Resource Manager テンプレートはサポートされていません。

アラートが生成されると、ユーザーは通知を受け取ります。 Azure Backup では、メール経由での組み込みの通知メカニズムを提供しています。 アラートの生成時に通知を受け取る個別のメール アドレスや配布リストを指定できます。 また、それぞれのアラートについて個別に通知を受け取るか、アラートをまとめて 1 時間ごとのダイジェストとして通知を受け取るかを選択することもできます。

![RS コンテナーの組み込みのメール通知のスクリーンショット](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

通知が構成されている場合、ウェルカム メールまたは導入時のメールを受け取ります。 これは、アラートの生成時に、Azure Backup がこれらのアドレスにメールを送信できるかどうかを確認するものです。<br>

頻度が 1 時間ごとのダイジェストに設定されており、アラートが生成されて 1 時間以内にこれが解決された場合は、次の 1 時間ごとのダイジェストにこのアラートは含まれません。

> [!NOTE]
>
> - 破壊的な操作 (**データを削除して保護を停止** など) が実行されると、アラートが生成され、Recovery Services コンテナー用に通知が構成されていない場合でも、サブスクリプションの所有者、管理者、共同管理者にメールが送信されます。
> - 成功したジョブの通知を構成するには、[Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace) を使用します。

## <a name="inactivating-alerts"></a>アラートの非アクティブ化

アクティブなアラートを無効化または解決するには、非アクティブ化するアラートに対応するリスト アイテムを選択できます。 これにより、アラートに関する詳細情報を表示する画面が開き、上部に **[非アクティブ化]** ボタンが表示されます。 このボタンを選択すると、アラートの状態が **非アクティブ** に変わります。 また、アラートに対応するリスト アイテムを右クリックし、 **[非アクティブ化]** を選択して、アラートを非アクティブにすることもできます。

![バックアップ センター アラートの非アクティブ化のスクリーンショット](media/backup-azure-monitoring-laworkspace/vault-alert-inactivate.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Backup の Azure Monitor アラート (プレビュー)

Azure Backup は、Azure Monitor を介してアラートを提供し、ユーザーがバックアップなどのさまざまな Azure サービス間で一貫したアラート管理エクスペリエンスを得られるようにします。 Azure Monitor アラートを使用すると、Azure Monitor でサポートされている通知チャネル (電子メール、ITSM、Webhook、ロジック アプリなど) にアラートをルーティングできます。

現在、Azure Backup では、主に 2 種類の組み込みアラートを利用できます。

* **セキュリティ アラート**: バックアップ データの削除や、コンテナーの論理的な削除機能の無効化などのシナリオの場合、セキュリティ アラート (重大度 0) が発生し、Azure portal に表示されるか、他のクライアント (PowerShell、CLI、REST API) で使用されます。 セキュリティ アラートは既定で生成され、オフにできません。 ただし、通知 (電子メールなど) が発生するシナリオは制御することができます。 通知を構成する方法の詳細については、「[アクション ルール](../azure-monitor/alerts/alerts-action-rules.md)」を参照してください。
* **ジョブの失敗のアラート**: バックアップ エラーや復元エラーなどのシナリオの場合、Azure Backup は、Azure Monitor 経由で組み込みアラート (重大度 1) を提供します。 セキュリティ アラートとは異なり、ジョブの失敗シナリオに対しては、Azure Monitor アラートをオフにできます。 たとえば、Log Analytics を使用してジョブの失敗のカスタム アラート ルールを既に構成している場合、ジョブの失敗ごとに組み込みアラートを発生させる必要はありません。 既定では、ジョブの失敗のアラートはオフになっています。 詳細については、[これらのシナリオに対してアラートを有効にする方法に関するセクション](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)をご覧ください。
 
次の表は、現在 Azure Monitor 経由で使用できるさまざまなバックアップ アラート (プレビュー段階) と、サポートされているワークロード/コンテナーの種類をまとめたものです。

| **アラートのカテゴリ** | **アラート名** | **サポートされているワークロードの種類/コンテナーの種類** | **説明** | 
| ------------------ | -------------  | ------------------------------------------ | -------- |
| セキュリティ | バックアップ データを削除する | <li> Azure Virtual Machine <br><br> <li> Azure VM 内の SQL (AG 以外のシナリオ) <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Backup エージェント <br><br> <li> DPM <br><br> <li> Azure Backup Server <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure BLOB <br><br> <li> Azure Managed Disks  | このアラートは、ユーザーがバックアップを停止し、バックアップ データを削除するときに発生します (注 - コンテナーに対して論理的な削除機能が無効になっている場合、バックアップ データの削除アラートは受信されません) |
| セキュリティ | 今後の消去 | <li> Azure Virtual Machine <br><br> <li> Azure VM 内の SQL (AG 以外のシナリオ) <br><br> <li> Azure VM 内の SAP HANA | このアラートは、論理的な削除をサポートしているすべてのワークロードについて、項目のバックアップ データが、Azure Backup サービスによって完全に消去されるまであと 2 日になったときに発生します | 
| セキュリティ | 消去の完了 | <li> Azure Virtual Machine <br><br> <li> Azure VM 内の SQL (AG 以外のシナリオ) <br><br> <li> Azure VM 内の SAP HANA | バックアップ データを削除する |
| セキュリティ | コンテナーの論理的な削除が無効 | Recovery Services コンテナー | このアラートは、項目の論理的な削除のバックアップ データが、Azure Backup サービスによって完全に削除されたときに発生します | 
| ジョブ | バックアップの失敗 | <li> Azure Virtual Machine <br><br> <li> Azure VM 内の SQL (AG 以外のシナリオ) <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Backup エージェント <br><br> <li> Azure Files <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure Managed Disks | このアラートは、バックアップ ジョブが失敗したときに発生します。 既定では、バックアップの失敗のアラートはオフになっています。 詳細については、[このシナリオに対してアラートを有効にする方法に関するセクション](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)をご覧ください。 | 
| ジョブ | 復元の失敗 | <li> Azure Virtual Machine <br><br> <li> Azure VM 内の SQL (AG 以外のシナリオ) <br><br> <li> Azure VM 内の SAP HANA <br><br> <li> Azure Backup エージェント <br><br> <li> Azure Files <br><br> <li> Azure Database for PostgreSQL サーバー <br><br> <li> Azure BLOB <br><br> <li> Azure Managed Disks| このアラートは、復元ジョブが失敗したときに発生します。 既定では、復元の失敗のアラートはオフになっています。 詳細については、[このシナリオに対してアラートを有効にする方法に関するセクション](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)をご覧ください。 | 

### <a name="turning-on-azure-monitor-alerts-for-job-failure-scenarios"></a>ジョブの失敗のシナリオに対して Azure Monitor のアラートを有効にする

バックアップ失敗と復元失敗のシナリオに対して Azure Monitor アラートをオプトインするには、以下の手順に従います。

1. Azure portal に移動し、**プレビュー機能** を検索します。

    ![ポータル内のプレビュー機能を表示するためのスクリーンショット](media/backup-azure-monitoring-laworkspace/portal-preview-features.png)

2. オプトインできるすべてのプレビュー機能のリストを表示できます。

    * Recovery Services コンテナーにバックアップされたワークロードのジョブの失敗アラートを受け取るには、Microsoft.RecoveryServices プロバイダー (列 3) に対応する **EnableAzureBackupJobFailureAlertsToAzureMonitor** という名前のフラグを選択します。
    * Backup コンテナーにバックアップされたワークロードのジョブの失敗アラートを受け取るには、Microsoft.DataProtection プロバイダー (列 3) に対応する **EnableAzureBackupJobFailureAlertsToAzureMonitor** という名前のフラグを選択します。

    ![アラート プレビュー登録のスクリーンショット](media/backup-azure-monitoring-laworkspace/alert-preview-feature-flags.png)

3. **[登録]** をクリックして、ご自身のサブスクリプションのこの機能を有効にします。
    > [!NOTE]
    > 登録が有効になるまで、最大で 24 時間かかる可能性があります。 複数のサブスクリプションに対してこの機能を有効にするには、画面上部の関連するサブスクリプションを選択して、上記のプロセスを繰り返します。 また、最初の登録後にサブスクリプション内で新しいリソースが作成された場合は、引き続きアラートを受信するために、プレビュー フラグを再登録することをお勧めします。

4. ベスト プラクティスとして、機能の登録情報が Azure Backup サービスと正常に同期されるように、リソース プロバイダーを登録することをお勧めします。 リソース プロバイダーを登録するには、機能フラグを登録したサブスクリプションで、次の PowerShell コマンドを実行します。

```powershell
Register-AzResourceProvider -ProviderNamespace <ProviderNamespace>
```

Recovery Services Vault のアラートを受け取るには、_ProviderNamespace_ パラメーターに値 _Microsoft.RecoveryServices_ を使用します。 バックアップ Vault のアラートを受信するには、値 _Microsoft.DataProtection_ を使用します。

### <a name="viewing-fired-alerts-in-the-azure-portal"></a>Azure portal 内で始動したアラートを確認 

コンテナーに対してアラートが発生した後、Azure portal 内でアラートを表示するには、バックアップ センターに移動します。 **[概要]** タブには、重大度ごと分類されたアクティブなアラートの概要が示されています。 表示されているアラートは 2 種類です。

* **データソース アラート**: **[データソース アラート]** セクションには、バックアップ中の特定のデータソースに関連付けられているアラート (VM の場合はバックアップまたは復元の失敗、データベースの場合はバックアップ データの削除など) が表示されます。
* **グローバル アラート**: **[グローバル アラート]** セクションには、特定のデータソースに関連付けられていないアラート (コンテナーの論理的な削除機能の無効化など) が表示されます。

上記の種類のアラートはそれぞれ、さらに **セキュリティ** アラートと **構成済み** アラートに分けられます。 現在、セキュリティ アラートには、バックアップ データの削除、コンテナーの論理的な削除の無効化のシナリオが含まれます (該当するワークロードについて詳しくは、上記のセクションを参照)。 構成済みアラートには、バックアップの失敗と復元の失敗が含まれます。これらのアラートは、プレビュー ポータルで機能を登録した後にのみ発生するからです。

![バックアップ センター内のアラートを表示するためのスクリーンショット](media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alerts.png)

任意の数値 (または **[アラート]** メニュー項目) 上をクリックすると、関連するフィルターが適用されたすべてのアクティブなアラートのリストが表示されます。 サブスクリプション、リソース グループ、コンテナー、重大度、状態など、さまざまなプロパティでフィルター処理できます。 いずれかのアラートをクリックすると、影響を受けるデータソース、アラートの説明、推奨されるアクションなど、アラートの詳細を取得できます。

![アラートの詳細を表示するためのスクリーンショット](media/backup-azure-monitoring-laworkspace/backup-center-alert-details.png) 

アラートの状態を **[確認済み]** または **[終了]** に変更するには、 **[アラートの状態の変更]** をクリックします。

![アラートの状態を変更するためのスクリーンショット](media/backup-azure-monitoring-laworkspace/backup-center-change-alert-state.png) 

> [!NOTE]
> - バックアップ センターでは、現在 Azure ベースのワークロードのアラートのみが表示されています。 オンプレミスのリソースのアラートを表示するには、Recovery Services コンテナーに移動し、 **[アラート]** メニュー項目をクリックします。
> - Azure Monitor アラートのみが、バックアップ センターに表示されます。 以前のアラート ソリューションによって発生したアラート (Recovery Services コンテナー内の [[バックアップ アラート]](#backup-alerts-in-recovery-services-vault) タブからアクセス) は、バックアップ センターに表示されません。
Azure Monitor アラートについて詳しくは、「[Azure のアラートの概要](../azure-monitor/alerts/alerts-overview.md)」を参照してください。

### <a name="configuring-notifications-for-alerts"></a>アラートの通知を構成する

Azure Monitor のアラートの通知を構成するには、アクション ルールを作成する必要があります。 次の手順は、特定のメール アドレスにメール通知を送信するアクション ルールを作成する方法を示しています。 ITSM、Webhook、ロジック アプリなど、他の通知チャネルにこれらのアラートをルーティングする場合も、同様の手順が適用されます。

1. Azure portal 内で、**バックアップ センター** に移動します。 **[アラート]** メニュー項目をクリックし、 **[アクションの管理]** を選択します。

    ![バックアップ センター内でのアクション管理のスクリーンショット](media/backup-azure-monitoring-laworkspace/backup-center-manage-actions.png) 

2. **[アクション ルール (プレビュー)]** タブに移動し、 **[新しいアクション ルール]** をクリックします。

    ![新しいアクション ルールを作成するためのスクリーンショット](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-rule.png) 

3. アクション ルールの適用範囲を選択します。 アクション ルールは、サブスクリプション内のすべてのリソースに適用できます。 必要に応じて、アラート上にフィルターを適用し、たとえば、特定の重要度のアラートに対してのみ通知を生成することもできます。

    ![アクション ルールの範囲を設定するためのスクリーンショット](media/backup-azure-monitoring-laworkspace/azure-monitor-action-rule-scope.png) 

4. アクション グループを作成します。 アクション グループは、アラートの通知の送信先です (メール アドレスなど)。
 
    ![新しいアクション グループを作成するためのスクリーンショット](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-group.png) 

5. **[基本]** タブ上で、アクション グループの名前と、そのグループを作成する先のサブスクリプションとリソース グループを選択します。

    ![アクション グループの基本プロパティのスクリーンショット](media/backup-azure-monitoring-laworkspace/azure-monitor-action-groups-basic.png) 

6. **[通知]** タブで **[電子メール/SMS メッセージ/プッシュ/音声]** を選択し、受信者のメール ID を入力します。

    ![通知プロパティを設定するためのスクリーンショット](media/backup-azure-monitoring-laworkspace/azure-monitor-email.png) 

7. **[確認と作成]** をクリックし、 **[作成]** をクリックして、アクション グループをデプロイします。

8. 最後に、アクション ルールを保存します。

[Azure Monitor 内のアクション ルールの詳細](../azure-monitor/alerts/alerts-action-rules.md)


## <a name="next-steps"></a>次のステップ

[Azure Monitor を使用した Azure Backup ワークロードの監視](backup-azure-monitoring-use-azuremonitor.md)
