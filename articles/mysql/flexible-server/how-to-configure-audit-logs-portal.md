---
title: 監査ログの構成 - Azure portal - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure portal から Azure Database for MySQL フレキシブル サーバーの監査ログを構成し、それにアクセスする方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 1d0687e6cdcc8208218183cb873f9620a5525d37
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90932514"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL - フレキシブル サーバーの監査ログの構成とアクセス

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure Database for MySQL フレキシブル サーバーの[監査ログ](concepts-audit-logs.md)と診断設定を Azure portal から構成することができます。

## <a name="prerequisites"></a>前提条件
この記事の手順では、[フレキシブル サーバー](quickstart-create-server-portal.md)が必要です。

## <a name="configure-audit-logging"></a>監査ログを構成する

>[!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

監査ログを有効化および構成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. フレキシブル サーバーを選択します。

1. サイドバーの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Server parameters":::-->

1. **audit_log_enabled** パラメーターを ON に更新します。
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Enable audit logs":::-->

1. **audit_log_events** パラメーターを更新して、ログに記録する[イベントの種類](concepts-audit-logs.md#configure-audit-logging)を選択します。
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Audit log events":::-->

1. **audit_log_exclude_users** パラメーターを更新して、ログから除外する MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Audit log exclude users":::-->

1. パラメーターを変更すると、 **[保存]** をクリックできます。 または変更を **[破棄]** することができます。
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Save":::-->

## <a name="set-up-diagnostics"></a>診断を設定する

監査ログは Azure Monitor 診断設定と統合されており、ログを Azure Monitor ログ、Event Hubs、または Azure Storage にパイプすることができます。

1. サイドバーの **[監視]** セクションの下で、 **[診断設定]** を選択します。

1. [+ 診断設定の追加] をクリックします <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Add diagnostic setting":::-->

1. 診断設定の名前を指定します。

1. どの送信先 (ストレージ アカウント、イベント ハブ、Log Analytics ワークスペース) に監査ログを送信するか指定します。

1. ログの種類として **MySqlAuditLogs** を選択します。
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Configure diagnostic setting"::: -->

1. 監査ログをパイプするようにデータ シンクを設定したら、 **[保存]** をクリックすることができます。
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Save diagnostic setting":::-->

1. 構成したデータ シンクを調べて監査ログにアクセスします。 ログが表示されるまでに最大で 10 分かかる可能性があります。

監査ログを Azure Monitor ログ (Log Analytics) にパイプした場合は、分析に使用できるいくつかの[サンプル クエリ](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs)を参照してください。  

## <a name="next-steps"></a>次のステップ

- [監査ログ](concepts-audit-logs.md)の詳細を確認する
- [低速クエリ ログ](concepts-slow-query-logs.md)を確認する
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->