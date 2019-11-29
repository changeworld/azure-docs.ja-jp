---
title: Azure Monitor ログを使用した Azure Functions の監視
description: Azure Functions で Azure Monitor ログを使用して関数の実行を監視する方法について説明します。
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: 9aac6662304395b1bce5dfc21770d296f6a4f2ab
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226842"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Monitor ログを使用した Azure Functions の監視

Azure Functions は [Azure Monitor ログ](../azure-monitor/platform/data-platform-logs.md)と統合されており、関数を監視することができます。 この記事では、システム生成ログとユーザー生成ログを Azure Monitor ログに送信するように Azure Functions を構成する方法について説明します。

Azure Monitor Logs を使うと、同じワークスペース内の異なるリソースのログを統合できます。また、それを[クエリ](../azure-monitor/log-query/log-query-overview.md)を使って分析し、収集したデータをすばやく取得、統合、分析できます。  Azure portal で [Log Analytics](../azure-monitor/log-query/portals.md) を使用してクエリを作成およびテストした後、これらのツールを使用してデータを直接分析できるほか、クエリを保存して[視覚化](../azure-monitor/visualizations.md)または[アラート ルール](../azure-monitor/platform/alerts-overview.md)に利用することができます。

Azure Monitor では、Azure Data Explorer で使用される [Kusto クエリ言語](/azure/kusto/query/)のバージョンを使用します。それは、単純なログ検索に適していますが、集計、結合、スマート分析などの高度な機能も備えています。 [さまざまなレッスン](../azure-monitor/log-query/get-started-queries.md)を利用すれば、クエリ言語はすぐに覚えることができます。

> [!NOTE]
> 現在、Azure Monitor Logs との統合は、Windows Consumption、Premium、Dedicated ホスティング プランで実行されている関数アプリに関してパブリック プレビュー段階にあります。

## <a name="setting-up"></a>設定

[監視] セクションで **[診断設定]** を選択し、 **[追加]** をクリックします。

![診断設定を追加する](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

[設定] ページで **[Log Analytics への送信]** を選択し、 **[ログ]** の下の **[FunctionAppLogs]** を選択します。このテーブルに目的のログが含まれています。

![診断設定を追加する](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>ユーザー生成ログ

カスタム ログを生成するには、言語に応じて特定のログ ステートメントを使用できます。サンプル コード スニペットを次に示します。

**JavaScript**

```javascript
    context.log('My app logs here.');
```

**Python**

```python
    logging.info('My app logs here.')
```

**.NET**

```csharp
    log.LogInformation("My app logs here.");
```

**Java**

```java
    context.getLogger().info("My app logs here.");
```

**PowerShell**

```powershell
    Write-Host "My app logs here."
```

## <a name="querying-the-logs"></a>ログのクエリ

生成されたログに対してクエリを実行するには、Log Analytics ワークスペースに移動し、 **[ログ]** をクリックします。

![LA ワークスペースのクエリ ウィンドウ](media/functions-monitor-log-analytics/querying.png)

Azure Functions によって、すべてのログが **FunctionAppLogs** テーブルに書き込まれます。サンプル クエリを次に示します。

### <a name="all-logs"></a>すべてのログ

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>特定の関数のログ

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>例外

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>次の手順

- 「[Azure Functions の概要](functions-overview.md)」を確認します。
- [Azure Monitor ログ](../azure-monitor/platform/data-platform-logs.md)の詳細について学習します。
- [クエリ言語](../azure-monitor/log-query/get-started-queries.md)の詳細について学習します。
