---
title: 監査ログにアクセスする - Azure portal - Azure Database for MySQL
description: この記事では、Azure portal から Azure Database for MySQL の監査ログを構成し、それにアクセスする方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: c406fa6b49e800912edb5738b4d60596d828fc94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570494"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Azure portal での Azure Database for MySQL の監査ログの構成とアクセス

[Azure Database for MySQL の監査ログ](concepts-audit-logs.md)と診断設定を Azure portal から構成することができます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、以下が必要です。

- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>監査ログを構成する

>[!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

監査ログを有効化および構成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Database for MySQL サーバーを選択します。

1. サイドバーの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="サーバー パラメーター":::

1. **audit_log_enabled** パラメーターを ON に更新します。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="サーバー パラメーター":::

1. **audit_log_events** パラメーターを更新して、ログに記録する[イベントの種類](concepts-audit-logs.md#configure-audit-logging)を選択します。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="サーバー パラメーター":::

1. **audit_log_exclude_users** および**audit_log_include_users** パラメーターを更新して、ログに含めたりログから除外したりする MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="サーバー パラメーター":::

1. パラメーターを変更すると、 **[保存]** をクリックできます。 または変更を **[破棄]** することができます。
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="サーバー パラメーター":::

## <a name="set-up-diagnostic-logs"></a>診断ログの設定

1. サイドバーの **[監視]** セクションの下で、 **[診断設定]** を選択します。

1. [+ Add diagnostic setting] (診断設定の追加) をクリックします。:::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="サーバー パラメーター" を選択します。
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="サーバー パラメーター":::

1. 監査ログをパイプするようにデータ シンクを設定したら、 **[保存]** をクリックすることができます。
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="サーバー パラメーター":::

1. 構成したデータ シンクを調べて監査ログにアクセスします。 ログが表示されるまでに最大で 10 分かかる可能性があります。

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL の[監査ログ](concepts-audit-logs.md)の詳細について学習します
- [Azure CLI](howto-configure-audit-logs-cli.md) で監査ログを構成する方法を学習します