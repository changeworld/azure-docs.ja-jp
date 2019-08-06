---
title: 一般的なエラーのトラブルシューティング
description: Azure Resource Graph を使用して Azure リソースをクエリする際の問題をトラブルシューティングする方法について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480467"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Azure Resource Graph 使用時のエラーのトラブルシューティング

Azure Resource Graph を使用して Azure リソースをクエリすると、エラーが発生することがあります。 この記事では、発生する可能性があるさまざまなエラーと、その解決方法について説明します。

## <a name="finding-error-details"></a>エラー詳細の検索

ほとんどのエラーは、Azure Resource Graph を使用したクエリ実行中の問題の結果です。 クエリが失敗すると、SDK は失敗したクエリの詳細を提供します。 この情報から問題が示されるので、問題を解決すれば、次回以降のクエリは成功します。

## <a name="general-errors"></a>一般エラー

### <a name="toomanysubscription"></a>シナリオ:サブスクリプションが多すぎる

#### <a name="issue"></a>問題

[Azure Lighthouse](../../../lighthouse/overview.md) によるクロステナント サブスクリプションを含め、1,000 を超えるサブスクリプションにアクセスできるお客様は、Azure Resource Graph の 1 回の呼び出しですべてのサブスクリプションにわたってデータをフェッチできません。

#### <a name="cause"></a>原因

Azure CLI と PowerShell は、最初の 1,000 サブスクリプションのみを Azure Resource Graph に転送します。 Azure Resource Graph 用の REST API は、クエリを実行するために最大数のサブスクリプションを受け付けます。

#### <a name="resolution"></a>解決策

1,000 サブスクリプションの制限を超えないよう、サブスクリプションのサブセットを使用してクエリ要求をバッチ化します。 ソリューションでは PowerShell の **Subscription** パラメーターを使用しています。

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
- [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
- さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。