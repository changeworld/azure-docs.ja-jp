---
title: 'チュートリアル: Azure Database for MySQL フレキシブル サーバーを使用して監査ログを構成する'
description: このチュートリアルでは、Azure Database for MySQL フレキシブル サーバーを使用して監査ログを構成する方法を示します。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 4a819814b8c5bb8cf6d4bea949463fddd05405a8
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065865"
---
# <a name="tutorial-configure-audit-logs-by-using-azure-database-for-mysql-flexible-server"></a>チュートリアル: Azure Database for MySQL フレキシブル サーバーを使用して監査ログを構成する

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL フレキシブル サーバーを使用して監査ログを構成することができます。 監査ログは、接続、管理、データ定義言語 (DDL)、データ操作言語 (DML) のイベントなどのデータベースレベルのアクティビティを追跡するために使用できます。 これらの種類のログは、通常、コンプライアンスのために使用されます。 データベース監査は通常、次のために使用されます。
* 特定のスキーマ、テーブル、または行で発生する、または特定のコンテンツに影響を与えるすべてのアクションについて説明する。
* ユーザー (またはその他) の説明責任に基づいて、不適切な操作を行うのを防ぐ。
* 疑わしいアクティビティを調査する。
* 特定のデータベース アクティビティに関するデータを監視し収集する。
 
この記事では、MySQL 監査ログ、Log Analytics ツール、またはブック テンプレートを使用して Azure Database for MySQL フレキシブル サーバーの監査情報を視覚化する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。
>[!div class="checklist"]
> * Azure portal または Azure CLI を使用して監査を構成する
> * 診断を設定する
> * Log Analytics を使用して監査ログを表示する 
> * ブックを使用して監査ログを表示する  

## <a name="prerequisites"></a>前提条件

- [Azure Database for MySQL フレキシブル サーバー インスタンスを作成します](./quickstart-create-server-portal.md)。
- [Log Analytics ワークスペースを作成します](../../azure-monitor/logs/quick-create-workspace.md)。

## <a name="configure-auditing-by-using-the-azure-portal"></a>Azure portal を使用して監査を構成する 

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. フレキシブル サーバー インスタンスを選択します。

1. 左側のペインの **[設定]** の下で **[サーバー パラメーター]** を選択します。

    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="[サーバーパラメーター] の一覧を示すスクリーンショット。":::

1. **audit_log_enabled** パラメーターで、 **[ON]\(オン\)** を選択します。

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="[ON]\(オン\) に切り替えた 'audit_log_enabled' パラメーターを示すスクリーンショット。":::

1. **audit_log_events** パラメーターのドロップダウン リストで、ログに記録する[イベントの種類](concepts-audit-logs.md#configure-audit-logging)を選択します。

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="'audit_log_events' ドロップダウン リストのイベント オプションのスクリーンショット。":::

1. **audit_log_exclude_users** および **audit_log_include_users** パラメーターについては、MySQL ユーザー名を入力することによって、ログに含めたりログから除外したりする MySQL ユーザーを指定します。

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="ログに含めるかログから除外する MySQL ユーザー名を示すスクリーンショット。":::

1. **[保存]** を選択します。

    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="パラメーター値の変更を保存するための [保存] ボタンのスクリーンショット。":::


## <a name="configure-auditing-by-using-the-azure-cli"></a>Azure CLI を使用して監査を構成する
 
別の方法として、次のコマンドを実行して、Azure CLI からフレキシブル サーバーの監査を有効にして構成することもできます。 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>診断を設定する

監査ログは Azure Monitor 診断設定と統合されているため、次の 3 つのデータ シンクにログをパイプすることができます。
* Log Analytics ワークスペース
* イベント ハブ
* ストレージ アカウント

>[!Note]
>診断設定を構成する前に、データ シンクを作成する必要があります。 構成したデータ シンクで監査ログにアクセスできます。 ログが表示されるまでに最大で 10 分かかる可能性があります。

1. 左側のウィンドウの **[モニター]** の下で、 **[診断設定]** を選択します。

1. **[診断設定]** ウィンドウで、 **[診断設定を追加する]** を選択します。

    :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="[診断設定] ウィンドウの [診断設定を追加する] リンクのスクリーンショット。":::

1. **[名前]** ボックスに、診断設定の名前を入力します。

    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="構成オプションを選択するための [診断設定] ウィンドウのスクリーンショット。":::

1. 監査ログの送信先 (Log Analytics ワークスペース、イベント ハブ、またはストレージ アカウント) を指定するために、対応するチェックボックスをオンにします。

    >[!Note]
    > このチュートリアルでは、監査ログを Log Analytics ワークスペースに送信します。
    
1. **[ログ]** の [ログの種類] で、 **[MySqlAuditLogs]** のチェックボックスをオンにします。

1. 監査ログをパイプするようにデータ シンクを設定した後、 **[保存]** を選択します。

    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="[診断設定] ウィンドウの上部にある [保存] ボタンのスクリーンショット。":::

## <a name="view-audit-logs-by-using-log-analytics"></a>Log Analytics を使用して監査ログを表示する 

1. Log Analytics の左側のペインの **[監視]** から **[ログ]** を選択します。

1. **[クエリ]** ウィンドウを閉じます。  

   :::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Log Analytics の [クエリ] ウィンドウのスクリーンショット。":::

1. クエリ ウィンドウでは、実行するクエリを記述できます。 たとえば、特定のサーバーで監査イベントの概要を確認するために、次のクエリを使用しました。 

    ```kusto
    AzureDiagnostics
        |where Category =='MySqlAuditLogs' 
        |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
        |summarize count() by event_class_s,event_subclass_s 
        |order by event_class_s 
    ```
    
    :::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="特定のサーバーで監査イベントの概要を検索するための Log Analytics クエリの例のスクリーンショット。":::

## <a name="view-audit-logs-by-using-workbooks"></a>ブックを使用して監査ログを表示する 
 
監査に使用するブック テンプレートでは、プラットフォーム ログを送信するための診断設定を作成する必要があります。 

1. Azure Monitor の左側のペインで、 **[アクティビティ ログ]** を選択し、 **[診断設定]** を選択します。 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="Azure Monitor の [アクティビティ ログ] ウィンドウの [診断設定] タブを示すスクリーンショット。":::

1. **[診断設定]** ウィンドウでは、新しい設定を追加したり、既存の設定を編集したりすることができます。 各設定には、各送信先の種類を 1 つだけ含めることができます。

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="ログ出力先を選択するための Azure Monitor の [診断設定] ウィンドウのスクリーンショット。":::

    > [!Note]
    > 既に構成してあるデータ シンク (Log Analytics ワークスペース、ストレージ アカウント、またはイベント ハブ) で低速クエリ ログにアクセスできます。 ログが表示されるまでに最大で 10 分かかる可能性があります。

1. Azure portal の左側のペインで、Azure Database for MySQL フレキシブル サーバー インスタンスの **[監視]** の下にある **[ブック]** を選択します。
1. **[監査]** ブックを選択します。 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="ブック ギャラリー内のすべてのブックを示すスクリーンショット。":::
    
ブックでは、次の視覚化を表示できます。 
>[!div class="checklist"]
> * サービスでの管理操作
> * 監査の概要
> * 接続イベントの監査の概要
> * 接続イベントの監査
> * テーブル アクセスの概要
> * 識別されたエラー


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="ブック テンプレート &quot;サービスでの管理操作&quot; のスクリーンショット。":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="ブック テンプレート &quot;接続イベントの監査&quot; のスクリーンショット。":::

>[!Note]
> * これらのテンプレートを編集し、要件に従ってカスタマイズすることもできます。 詳細については、[Azure Monitor ブックの概要](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)に関する記事の「編集モード」セクションを参照してください。
> * 素早く表示するために、ブックまたは Log Analytics クエリをダッシュボードにピン留めすることもできます。 詳細については、「[Azure portal でダッシュボードを作成する](../../azure-portal/azure-portal-dashboards.md)」を参照してください。 

*[Administrative Actions on the service]\(サービスでの管理操作\)* ビューでは、サービスで実行されたアクティビティの詳細が表示されます。 これは、サブスクリプションのリソースに対して行われたすべての書き込み操作 (PUT、POST、DELETE) について、"*何を、誰が、いつ*" 行ったのかを確認するのに役立ちます。 

他の視覚化を使用して、データベースの利用状況の詳細を把握することができます。 データベース セキュリティには、次の 4 つの部分があります。 
* **サーバー セキュリティ**: 承認されていないユーザーがデータベースにアクセスするのを防ぐ役割を担います。  
* **データベース接続**: 管理者は、承認された担当者がデータベースの更新を実行したかどうかを確認する必要があります。
* **テーブルのアクセス制御**: 許可されているユーザーのアクセス キーと、各ユーザーが扱うことを許可されているデータベース内のテーブルを表示します。
* **データベース アクセス制限**: データベースをインターネットにアップロードしたユーザーにとっては特に重要であり、外部ソースからデータベースにアクセスできないようにするのに役立ちます。 

## <a name="next-steps"></a>次のステップ
- [Azure Monitor ブック](../../azure-monitor/visualize/workbooks-overview.md#visualizations)とその豊富な視覚化オプションについて学習します。
- [監査ログについて学習します](concepts-audit-logs.md)。
