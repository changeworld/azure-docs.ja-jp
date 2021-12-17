---
title: Azure Container Apps プレビューでアプリを監視する
description: Azure Container Apps でアプリケーションが監視およびログ記録される方法について説明します。
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: c9c324c90ad401d43be1e2d6768ea82121fc1ae8
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027839"
---
# <a name="monitor-an-app-in-azure-container-apps-preview"></a>Azure Container Apps プレビューでアプリを監視する

Azure Container Apps により、コンテナー アプリに関する広範なデータのセットが収集され、[Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) を使って格納されます。 この記事では、利用できるログと、ログを書き込み、表示する方法について説明します。

## <a name="writing-to-a-log"></a>ログへの書き込み

[標準出力 (stdout) または標準エラー (stderr) ストリーム](https://wikipedia.org/wiki/Standard_streams)に書き込むと、Container Apps のログ エージェントによって各メッセージのログが書き込まれます。

メッセージがログに記録されると、ログ テーブルに次の情報が収集されます。

| プロパティ | Remarks |
|---|---|
| `RevisionName` | |
| `ContainerAppName` | |
| `ContainerGroupID` | |
| `ContainerGroupName` | |
| `ContainerImage` | |
| `ContainerID` | コンテナーの一意識別子。 この値を使って、コンテナーのクラッシュを特定できます。 |
| `Stream` | ログ記録に `stdout` または `stderr` が使われているかどうかを示します。 |
| `EnvironmentName` | |

### <a name="simple-text-vs-structured-data"></a>単純なテキストと構造化データ

1 つのテキスト文字列またはシリアル化された JSON データの行をログに記録できます。 情報の表示方法は、ログに記録されたデータの種類によって異なります。

| データの種類 | 説明 |
|---|---|
| 1 行のテキスト | `Log_s` 列にテキストが表示されます。 |
| シリアル化された JSON | ログ エージェントによってデータが解析され、JSON オブジェクトのプロパティ名と一致する列に表示されます。 |

## <a name="viewing-logs"></a>ログの表示

コンテナー アプリを使用してログされたデータは、Log Analytics ワークスペースの `ContainerAppConsoleLogs_CL` カスタム テーブルに格納されます。 Azure portal または CLI を使用してログを表示できます。

リソース グループと Log Analytics ワークスペースの名前を設定した後、次のコマンドを使って `LOG_ANALYTICS_WORKSPACE_CLIENT_ID` を取得します。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP="my-containerapps"
LOG_ANALYTICS_WORKSPACE="containerapps-logs"

LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv`
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$RESOURCE_GROUP="my-containerapps"
$LOG_ANALYTICS_WORKSPACE="containerapps-logs"

$LOG_ANALYTICS_WORKSPACE_CLIENT_ID=az monitor log-analytics workspace show --query customerId -g $RESOURCE_GROUP -n $LOG_ANALYTICS_WORKSPACE --out tsv
```

---

次の CLI コマンドを使用して、コマンド ラインでログを表示します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az monitor log-analytics query \
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID \
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" \
  --out table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az monitor log-analytics query `
  --workspace $LOG_ANALYTICS_WORKSPACE_CLIENT_ID `
  --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'my-container-app' | project ContainerAppName_s, Log_s, TimeGenerated | take 3" `
  --out table
```

---

次の出力は、CLI コマンドから返される応答の種類を示しています。

```console
ContainerAppName_s    Log_s                 TableName      TimeGenerated
--------------------  --------------------  -------------  ------------------------
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:09:00.168Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:36.197Z
my-container-app      listening on port 80  PrimaryResult  2021-10-23T02:11:43.171Z
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [コンテナー アプリをセキュリティで保護する](secure-app.md)
