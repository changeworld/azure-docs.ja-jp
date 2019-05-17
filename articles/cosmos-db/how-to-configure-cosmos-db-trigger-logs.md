---
title: Azure Cosmos DB トリガーのログ
description: Azure Functions のログ記録パイプラインに Azure Cosmos DB トリガーのログを公開する方法について説明します
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/2/2019
ms.author: maquaran
ms.openlocfilehash: a598842ecde9508a6c2185a6097f689252fda60c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515020"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Azure Cosmos DB トリガーのログを構成して読み取る方法

この記事では、Azure Cosmos DB トリガーのログを構成済みの[監視ソリューション](../azure-functions/functions-monitoring.md)に送信するように、Azure Functions 環境を構成する方法について説明します。

## <a name="included-logs"></a>含まれるログ

Azure Cosmos DB トリガーでは [Change Feed プロセッサ ライブラリ](./change-feed-processor.md)が内部的に使われており、ライブラリでは[トラブルシューティングの目的](./troubleshoot-changefeed-functions.md)で内部操作を監視するために使用できる正常性ログのセットが生成されます。

正常性ログでは、負荷分散シナリオまたは初期化の間に操作を試みるときの、Azure Cosmos DB トリガーの動作方法が記述されています。

## <a name="enabling-logging"></a>ログの記録を有効にする

Azure Cosmos DB トリガーのログ記録を有効にするには、Azure Functions プロジェクトまたは Azure Function App で `host.json` ファイルを探し、[必要なログ記録のレベルを構成](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson)します。 次のサンプルで示すように、`Host.Triggers.CosmosDB` のトレースを有効にする必要があります。

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

更新した構成で Azure 関数をデプロイした後は、トレースの一部として Azure Cosmos DB トリガーのログが表示されるようになります。 構成済みログ プロバイダーでのログは、"*カテゴリ*" `Host.Triggers.CosmosDB` で見ることができます。

## <a name="query-the-logs"></a>ログのクエリを実行する

[Azure Application Insights の Analytics](../azure-monitor/app/analytics.md) で Azure Cosmos DB トリガーによって生成されたログを照会するには、次のクエリを実行します。

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>次の手順

* Azure Functions アプリケーションで[監視を有効にします](../azure-functions/functions-monitoring.md)。
* Azure Functions で Azure Cosmos DB トリガーを使用するときに[問題の診断とトラブルシューティングを行う](./troubleshoot-changefeed-functions.md)方法を学習します。