---
title: 'チュートリアル: Azure Database for MySQL - フレキシブル サーバーの監査'
description: 'チュートリアル: Azure Database for MySQL - フレキシブル サーバーの監査'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: b4c8206071ce71cc29a9de45b46fa05c40f9cd91
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621259"
---
# <a name="tutorial-auditing-for-azure-database-for-mysql--flexible-server"></a>チュートリアル: Azure Database for MySQL - フレキシブル サーバーの監査
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL フレキシブル サーバーでは、ユーザーが監査ログを構成することができます。 監査ログは、接続、管理、DDL、DML の各イベントなど、データベースレベルのアクティビティを追跡するために使用できます。 これらの種類のログは、通常、コンプライアンスのために使用されます。 データベース監査は、通常、次のために使用されます。
* 特定のスキーマ、テーブル、または行内で発生している、または特定のコンテンツに影響を与えるすべてのアクションの詳細を把握する
* ユーザー (またはその他) の説明責任に基づいて、不適切な操作を行うのを防ぐ
* 疑わしいアクティビティを調査する
* 特定のデータベース アクティビティに関するデータの監視と収集
 
このチュートリアルでは、MySQL 監査ログ、Log Analytics ツール、または Workbooks テンプレートを使用して、Azure Database for MySQL - フレキシブル サーバーの監査情報を視覚化する方法について説明します。 

## <a name="prerequisites"></a>前提条件
- Azure Database for MySQL – フレキシブル サーバーのインスタンスを作成する必要があります。 詳細な手順については、「[Azure Database for MySQL フレキシブル サーバーのインスタンスを作成する](./quickstart-create-server-portal.md)」を参照してください。
- Log Analytics ワークスペースを作成する必要があります。 詳細な手順については、「[Log Analytics ワークスペースの作成](../../azure-monitor/logs/quick-create-workspace.md)」を参照してください。

このチュートリアルで学習する内容は次のとおりです。
>[!div class="checklist"]
> * ポータルまたは Azure CLI 使用して監査を構成する
> * 診断を設定する
> * Log Analytics を使用して監査ログを表示する 
> * ブックを使用して監査ログを表示する 

## <a name="configure-auditing-from-portal"></a>ポータルから監査を構成する 


1. [Azure portal](https://portal.azure.com/) にサインインします。

1. フレキシブル サーバーを選択します。

1. サイドバーの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="サーバー パラメーター":::

1. **audit_log_enabled** パラメーターを ON に更新します。
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="監査ログの有効化":::

1. **audit_log_events** パラメーターを更新して、ログに記録する [イベントの種類](concepts-audit-logs.md#configure-audit-logging)を選択します。
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="監査ログのイベント":::

1. **audit_log_exclude_users** および **audit_log_include_users** パラメーターを更新して、ログに含めたりログから除外したりする MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="監査ログの除外ユーザー":::

1. パラメーターを変更すると、 **[保存]** をクリックできます。 または変更を **[破棄]** することができます。
    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="および":::



## <a name="configure-auditing--from-azure-cli"></a>Azure CLI から監査を構成する
 
Azure CLI を使用して上記の手順を実行する場合は、CLI を使用してサーバーの監査を有効にし、構成することができます。 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>診断を設定する

監査ログは Azure Monitor 診断設定と統合されており、ログを Azure Monitor ログ、Event Hubs、または Azure Storage にパイプすることができます。

1. サイドバーの **[監視]** セクションの下で、 **[診断設定]** を選択します。

1. [+ 診断設定を追加する] をクリックします :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="診断設定を追加する":::

1. 診断設定の名前を指定します。

1. 監査ログの送信先 (Log Analytics ワークスペース、ストレージ アカウント、イベント ハブ) を指定します。
    >[!Note]
    > このチュートリアルの範囲では、低速クエリ ログを Log Analytics ワークスペースに送信する必要があります。
    
1. ログの種類として **MySqlAuditLogs** を選択します。
    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="診断設定の構成":::

1. 監査ログをパイプするようにデータ シンクを設定したら、 **[保存]** をクリックすることができます。
    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="診断設定の保存":::

    >[!Note]
    >診断設定を構成する前に、データ シンク (Log Analytics ワークスペース、ストレージ アカウント、またはイベント ハブ) を作成する必要があります。 構成したデータ シンク (Log Analytics ワークスペース、ストレージ アカウント、またはイベント ハブ) で低速クエリ ログにアクセスできます。ログが表示されるまでに最大 10 分かかることがあります。

## <a name="view-audit-logs-using-log-analytics"></a>Log Analytics を使用して監査ログを表示する 

**[監視]** セクションで、 **[ログ]** に移動します。 **[クエリ]** ウィンドウを閉じます。  

:::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Log Analytics のスクリーンショット":::

クエリ ウィンドウでは、実行するクエリを記述できます。  ここでは、クエリを使用して、特定のサーバーの監査イベントの集計を検索しました

```kusto
AzureDiagnostics
    |where Category =='MySqlAuditLogs' 
    |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
    |summarize count() by event_class_s,event_subclass_s 
    |order by event_class_s 
```
  
:::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="Log Analytics クエリのスクリーンショット":::

## <a name="view-audit-logs-using-workbooks"></a>ブックを使用して監査ログを表示する 
 
監査に使用するブック テンプレートでは、プラットフォーム ログを送信するための診断設定を作成する必要があります。 

1.  プラットフォーム ログを送信する場合、Azure Monitor メニューの [アクティビティ ログ]、 **[診断設定]** の順にクリックします。 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="診断設定のスクリーンショット":::

2.  新しい設定を追加するか、既存の設定を編集します。 各設定には、各送信先の種類を 1 つだけ含めることができます。

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="診断設定の選択のスクリーンショット":::

    >[!Note]
    >構成したデータ シンク (Log Analytics ワークスペース、ストレージ アカウント、イベント ハブ) で低速クエリ ログにアクセスできます。ログが表示されるまでに最大 10 分かかることがあります。

3.  Azure portal で、Azure Database for MySQL - フレキシブル サーバーの **[監視]** ブレードに移動し、 **[Workbooks]** を選択します。
4.  テンプレートを表示できるようになります。 **[監査]** を選択します。 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="ブック テンプレートのスクリーンショット":::
    
以下の視覚化を表示できます 
>[!div class="checklist"]
> * サービスでの管理操作
> * 監査の概要
> * 接続イベントの監査の概要
> * 接続イベントの監査
> * テーブル アクセスの概要
> * 識別されたエラー


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="ブック テンプレートの管理者イベントのスクリーンショット":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="ブック テンプレートの監査の概要イベントのスクリーンショット":::

>[!Note]
> * これらのテンプレートを編集し、要件に従ってカスタマイズすることもできます。 詳細については、[Azure Monitor ブックの概要 - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode) に関するページを参照してください。
> * 素早く表示するために、ダッシュボードにブックまたは Log Analytics クエリをピン留めすることもできます。 詳細については、「[Azure portal でダッシュボードを作成する - Azure portal](../../azure-portal/azure-portal-dashboards.md)」を参照してください。 

サービスに対する管理アクションでは、サービスで実行されたアクティビティの詳細が表示されます。 これは、サブスクリプションのリソースに対して行われたすべての書き込み操作 (PUT、POST、DELETE) について、何を、誰が、いつ行ったのかを確認するのに役立ちます。 

その他の視覚化は、データベース アクティビティの詳細を取得するのに役立ちます。 データベースのセキュリティは 4 つの部分で構成されます。 それは、サーバー セキュリティ、データベース接続、テーブル アクセスの制御、およびデータベース アクセスの制限です。 サーバー セキュリティは、承認されていないユーザーがデータベースにアクセスするのを防ぐ役割を担います。 データベース接続に関して、管理者は、データベースに対して行われた更新が承認された担当者によって行われたかどうかも確認する必要があります。 データベース アクセスの制限は、データベースがインターネットにアップロードされているユーザーにとって特に重要です。 これにより、外部ソースがデータベースに入り込んだり、データベースにアクセスしたりするのを防ぐことができます。 

## <a name="next-steps"></a>次の手順
- [Azure Monitor ブックの使用を開始](../../azure-monitor/visualize/workbooks-overview.md#visualizations)し、ブックの豊富な視覚化オプションを確認します。
- [監査ログ](concepts-audit-logs.md)の詳細を確認する