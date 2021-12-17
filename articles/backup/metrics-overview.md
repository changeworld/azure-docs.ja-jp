---
title: Azure Backup のメトリックを使用してバックアップの正常性を監視する (プレビュー)
description: この記事では、バックアップの正常性を監視するために Azure Backup で利用できるメトリックについて説明します
ms.topic: conceptual
author: v-amallick
ms.date: 10/20/2021
ms.author: v-amallick
ms.service: backup
ms.openlocfilehash: 158235789186748b4acb887a7861299d749a984d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709880"
---
# <a name="monitor-the-health-of-your-backups-using-azure-backup-metrics-preview"></a>Azure Backup のメトリックを使用してバックアップの正常性を監視する (プレビュー)

Azure Backup には、バックアップの正常性を監視できる、Azure Monitor の組み込みメトリックのセットが用意されています。 また、メトリックが定義したしきい値を超えたときにトリガーされるアラート ルールを構成することもできます。

Azure Backup では、次の主要機能が提供されています。

* バックアップ項目のバックアップと復元の正常性に関連するすぐに使用できるメトリックと、それに関連する傾向を表示する機能
* バックアップ項目の正常性を効率的に監視するために、これらのメトリックに対するカスタム アラート ルールを記述する機能
* メール、ITSM、Webhook、ロジック アプリなど、Azure Monitor によってサポートされるさまざまな通知チャネルに、発生したメトリック アラートをルーティングする機能。

[Azure Monitor のメトリックについて詳しくは、こちらをご覧ください](../azure-monitor/essentials/data-platform-metrics.md)。

## <a name="supported-scenarios"></a>サポートされるシナリオ

- 次のワークロードの種類に対する組み込みメトリックがサポートされます。

  - Azure VM、Azure VM 内の SQL データベース
  - Azure VM 内の SAP HANA データベース
  - Azure Files。

  HANA インスタンスのワークロードの種類に対するメトリックは、現在はサポートされていません。

- 各リージョンとサブスクリプション内のすべての Recovery Services コンテナーについて、メトリックを一度に表示できます。 現在、さらに大きなスコープのメトリックを Azure portal で表示することは、サポートされていません。 メトリック アラート ルールの構成にも、同じ制限が適用されます。

## <a name="supported-built-in-metrics"></a>サポートされている組み込みメトリック

現在、Azure Backup では次のメトリックがサポートされています。

- **バックアップ正常性イベント**: このメトリックの値は、特定の時間内にコンテナーで発生した、バックアップ ジョブの正常性に関連する正常性イベントの数を表します。 バックアップ ジョブが完了すると、Azure Backup サービスによってバックアップ正常性イベントが作成されます。 ジョブの状態 (成功または失敗など) に基づいて、イベントに関連付けられるディメンションは異なります。

- **復元正常性イベント**: このメトリックの値は、特定の時間内にコンテナーで発生した、復元ジョブの正常性に関連する正常性イベントの数を表します。 復元ジョブが完了すると、Azure Backup サービスによって復元正常性イベントが作成されます。 ジョブの状態 (成功または失敗など) に基づいて、イベントに関連付けられるディメンションは異なります。

既定では、件数はコンテナー レベルで表示されます。 特定のバックアップ項目についての件数とジョブの状態を表示するには、サポートされている任意のディメンションでメトリックをフィルター処理できます。

次の表に、バックアップ正常性イベントと復元正常性イベントのメトリックでサポートされるディメンションを示します。
 
| **ディメンション名**        | **説明**   | 
| ----------------------------| ----------------- |
| Datasource ID (データソース ID)               | ジョブに関連付けられた[データソース](azure-backup-glossary.md#datasource)の一意の ID。 <br><br> <ul><li> VM や Files などの Azure リソースの場合、これにはリソースの Azure Resource Manager ID (ARM ID) が含まれます。 <br> たとえば、`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM` のように指定します。 </li><br><br> <li> VM 内の SQL または HANA データベースの場合、これには VM の ARM ID とデータベースの詳細が含まれます。 <br> たとえば、`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM/providers/Microsoft.RecoveryServices/backupProtectedItem/SQLDataBase;mssqlserver;msdb` のように指定します。 </li></ul>  <br><br> SQL AG データベース バックアップの場合、このようなシナリオにはデータソース (VM) が含まれていないため、 **[Datasource ID]\(データソース ID\)** フィールドは空です。 AG 内の特定のデータベースのメトリックを表示するには、 **[Backup Instance ID]\(バックアップ インスタンス ID\)** フィールドを使用します。|
| データソースの種類             | ジョブに関連付けられた[データソース](azure-backup-glossary.md#datasource)の種類。 サポートされているデータソースの種類を次に示します。 <br><br> <ul><li> Microsoft.Compute/virtualMachines (Azure Virtual Machines)</li> <br><br> <li> Microsoft.Storage/storageAccounts/fileServices/shares (Azure Files) </li>  <br><br> <li> SQLDatabase (Azure VM 内の SQL) </li><br><br> <li> SAPHANADataBase (Azure VM 内の SAP HANA)</li></ul>   |
| Backup Instance ID (バックアップ インスタンス ID)          | ジョブに関連付けられた[バックアップ インスタンス](azure-backup-glossary.md#backup-instance--backup-item)の ARM ID。 <br><br> たとえば、`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;iaasvmcontainerv2;testRG;testVM` のように指定します。 |
| Backup Instance Name (バックアップ インスタンス名)        | 読みやすくするためのバックアップ インスタンスのフレンドリ名。 `{protectedContainerName};{backupItemFriendlyName}` のような形式です。 <br><br> たとえば、`testStorageAccount;testFileShare` のように指定します。      |
| 正常性状態               | ジョブが完了した後のバックアップ項目の正常性を表します。 次のいずれかの値になります: Healthy (正常)、Transient Unhealthy (一時的に異常)、Persistent Unhealthy (永続的に異常)、Transient Degraded (一時的に機能低下)、Persistent Degraded (永続的に機能低下)。 <br> <br> <ul> <li> バックアップまたは復元ジョブが成功すると、状態が "_正常_" の正常性イベントが表示されます。 </li><br><br><li>サービス エラーが原因のジョブ障害には "_異常_" が表示され、ユーザー エラーが原因の障害には "_機能低下_" が表示されます。 </li> <br><br><li> 同じバックアップ項目に対して同じエラーが繰り返し発生すると、状態が "_一時的な異常または機能低下_" から "_永続的な異常または機能低下_" に変わります。 </li></ul> |

## <a name="monitoring-scenarios"></a>監視シナリオ

### <a name="view-metrics-in-the-azure-portal"></a>Azure portal でメトリックを表示する

Azure portal でメトリックを表示するには、次の手順のようにします。

1. Azure portal で、 **[バックアップ センター]** に移動し、メニューの **[メトリック]** をクリックします。

   :::image type="content" source="./media/metrics-overview/backup-center-metrics-selection-inline.png" alt-text="バックアップ センターでの [メトリック] の選択を示すスクリーンショット。" lightbox="./media/metrics-overview/backup-center-metrics-selection-expanded.png":::

1. メトリックを表示するコンテナーまたはコンテナーのグループを選びます。

   現在、メトリックを表示できる最大のスコープは、特定のサブスクリプションとリージョン内のすべての Recovery Services コンテナーです。 たとえば、_TestSubscription1_ の米国東部のすべての Recovery Services コンテナーなどです。

1. **メトリックを選択** して、"_バックアップ正常性イベントまたは復元正常性イベント_" を表示します。

   これにより、コンテナーの正常性イベントの数を示すグラフがレンダリングされます。 画面の上部にあるフィルターを使用して、時間の範囲と集計の単位を調整できます。

   :::image type="content" source="./media/metrics-overview/metrics-chart-inline.png" alt-text="メトリックの選択プロセスを示すスクリーンショット。" lightbox="./media/metrics-overview/metrics-chart-expanded.png":::

1. 異なるディメンションでメトリックをフィルター処理するには、 **[フィルターの追加]** ボタンをクリックして、関連するディメンション値を選びます。 

   - たとえば、Azure VM バックアップの正常性イベント数のみを表示する場合は、フィルター `Datasource Type = Microsoft.Compute/virtualMachines` を追加します。 
   - コンテナー内の特定のデータソースまたはバックアップ インスタンスの正常性イベントを表示するには、データソース ID またはバックアップ インスタンス ID のフィルターを使います。
   - 失敗したバックアップの正常性イベントのみを表示するには、異常または機能低下の正常性状態に対応する値を選んで、HealthStatus のフィルターを使用します。

   :::image type="content" source="./media/metrics-overview/metrics-filters-inline.png" alt-text="異なるディメンションによるメトリックのフィルター処理プロセスを示すスクリーンショット。" lightbox="./media/metrics-overview/metrics-filters-expanded.png":::

### <a name="configure-alerts-and-notifications-on-your-metrics"></a>メトリックに関するアラートと通知を構成する

メトリックに関するアラートと通知を構成するには、次の手順のようにします。

1. メトリック グラフの上部にある **[新しいアラート ルール]** をクリックします。

1. アラートを作成するスコープを選びます。   <br><br>    スコープの制限は、[メトリックの表示](#view-metrics-in-the-azure-portal)に関するセクションで説明されている制限と同じです。

1. アラートが生成される条件を選びます。      <br><br>   既定では、メトリック グラフの選択に基づいて一部のフィールドが事前に設定されています。 必要に応じてパラメーターを編集できます。 コンテナー内の各データソースに対して個別のアラートを生成するには、メトリック アラート ルールでの **ディメンション** の選択を使用します。 いくつかのシナリオを次に示します。

   - データソースごとに失敗したバックアップ ジョブに対してアラートを生成します。

     **アラート ルール: 過去 24 時間のバックアップ正常性イベントが 0 より多い場合はアラートを生成する**:
     - Dimensions["HealthStatus"]= “Persistent Unhealthy / Transient Unhealthy”
     - Dimensions["DatasourceId"]= "現在と将来のすべての値"

   - その日のコンテナーでのすべてのバックアップが成功した場合にアラートを生成します。

     **アラート ルール: 過去 24 時間のバックアップ正常性イベントが 1 より小さい場合はアラートを生成する**:
     - Dimensions["HealthStatus"]="Persistent Unhealthy / Transient Unhealthy / Persistent Degraded / Transient Degraded"

   :::image type="content" source="./media/metrics-overview/metric-alert-condition-inline.png" alt-text="アラートを生成する条件を選択するオプションを示すスクリーンショット。" lightbox="./media/metrics-overview/metric-alert-condition-expanded.png":::

   >[!NOTE]
   >アラート ルール条件の一部としてより多くのディメンションを選択すると、コストが増加します (可能なディメンション値の一意の組み合わせの数に比例します)。 より多くのディメンションを選択すると、生成されたアラートについて、より多くのコンテキストを得られます。


1. アクション グループを使ってこれらのアラートの通知を構成するには、アラート ルールの一部としてアクション グループを構成するか、別のアクション ルールを作成します。

   メール、ITSM、Webhook、ロジック アプリ、SMS など、さまざまな通知チャネルがサポートされています。 [アクション グループについて詳しくは、こちらをご覧ください](../azure-monitor/alerts/action-groups.md)。

   :::image type="content" source="./media/metrics-overview/action-group-inline.png" alt-text="アクション グループを使用してこれらのアラートの通知を構成するプロセスを示すスクリーンショット。" lightbox="./media/metrics-overview/action-group-expanded.png":::

1. 自動解決動作を構成する - メトリックアラートは、必要に応じて "_ステートレス_" または "_ステートフル_" として構成できます。

   - 障害の根本原因が同じかどうかに関係なく、すべてのジョブ障害についてアラートを生成するには (ステートレス動作)、アラート ルールの **[アラートを自動的に解決する]** オプションをオフにします。
   - または、アラートをステートフルとして構成するには、同じチェック ボックスをオンにします。 したがって、そのスコープでメトリック アラートが生成されると、別の障害で新しいメトリック アラートが作成されることはありません。 連続する 3 回の評価サイクルで、アラート生成条件が false と評価された場合、そのアラートは自動的に解決されます。 条件が再び true と評価された場合は、新しいアラートが生成されます。

[Azure Monitor メトリック アラートのステートフルとステートレスの動作について詳しくは、こちらをご覧ください](../azure-monitor/alerts/alerts-troubleshoot-metric.md#make-metric-alerts-occur-every-time-my-condition-is-met)。

:::image type="content" source="./media/metrics-overview/auto-resolve-alert-inline.png" alt-text="自動解決動作を構成するプロセスを示すスクリーンショット。" lightbox="./media/metrics-overview/auto-resolve-alert-expanded.png":::

### <a name="managing-alerts"></a>アラートの管理

生成されたメトリック アラートを表示するには、次の手順のようにします。

1. **[バックアップ センター]**  >  **[アラート]** に移動します。
1. **シグナルの種類** = **メトリック** および **アラートの種類** = **構成済み** でフィルター処理します。
1. アラートの詳細を表示し、状態を変更するには、アラートをクリックします。

   :::image type="content" source="./media/metrics-overview/backup-center-metric-alerts-inline.png" alt-text="生成されたメトリック アラートを表示するプロセスを示すスクリーンショット。" lightbox="./media/metrics-overview/backup-center-metric-alerts-expanded.png":::

>[!NOTE]
>このアラートには、 **[監視の状態] (発生/解決済み)** と **[アラートの状態] (新規/確認/終了)** の 2 つのフィールドがあります。
>- **[アラートの状態]** : このフィールドは編集できます (次のスクリーンショットを参照)。
>- **[監視の状態]** : このフィールドは編集できません。 このフィールドは、サービス自体によってアラートが解決されるシナリオでよく使用されます。 たとえば、メトリック アラートの自動解決動作では、 **[監視の状態]** フィールドを使用してアラートが解決されます。


#### <a name="datasource-alerts-and-global-alerts"></a>データソース アラートとグローバル アラート

生成されたアラートは、アラート ルールの構成に基づいて、バックアップ センターの **[データソース アラート]** セクションまたは **[グローバル アラート]** セクションに表示されます。

- アラートにデータソース ID ディメンションが関連付けられている場合は、 **[データソース アラート]** の下に生成されたアラートが表示されます。
- アラートにデータソース ID ディメンションが関連付けられていない場合は、アラートを特定のデータソースに結び付ける情報がないため、生成されたアラートは **[グローバル アラート]** に表示されます。

[データソース アラートとグローバル アラートについて詳しくは、こちらをご覧ください](backup-center-monitor-operate.md#alerts)

### <a name="accessing-metrics-programmatically"></a>プログラムでのメトリックへのアクセス

PowerShell、CLI、REST API などのさまざまなプログラム クライアントを使用して、メトリック機能にアクセスできます。 詳しくは、[Azure Monitor REST API のドキュメント](../azure-monitor/essentials/rest-api-walkthrough.md)をご覧ください。

## <a name="next-steps"></a>次の手順
- [Azure Backup での監視とレポートについて詳しくは、こちらをご覧ください](monitoring-and-alerts-overview.md)。
- [Azure Monitor のメトリックについて詳しくは、こちらをご覧ください](../azure-monitor/essentials/data-platform-metrics.md)。
- [Azure のアラートについて詳しくは、こちらをご覧ください](../azure-monitor/alerts/alerts-overview.md)。