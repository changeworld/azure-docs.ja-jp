---
title: サービスの制限
titleSuffix: Azure Digital Twins
description: Azure Digital Twins サービスの制限を示すグラフ。
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: cb86fe0d4a99897821b389682c5a131ce0b9118c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472889"
---
# <a name="azure-digital-twins-service-limits"></a>Azure Digital Twins サービスの制限

次のセクションでは、Azure Digital Twins のサービスの制限について説明します。

> [!NOTE]
> このサービスの一部の領域には、調整可能な制限があります。 これは、次の表の *調整可能?* 列に示されています。 制限が調整可能な場合、*調整可能?* の値は *はい* になります。
>
> 調整可能な制限を上げること、または既定の制限を超えたクォータが必要な場合は、[サポート チケットを開いて](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)追加のリソースを要求できます。

## <a name="limits-by-type"></a>種類別の制限

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>制限の使用

制限に達すると、制限を超える要求がサービスによって調整されます。これにより、これらの要求に対して 429 エラー応答が返されます。

これを管理するには、次の推奨事項に留意して制限を使用します。
* **再試行ロジックを使用します。** [Azure Digital Twins SDK](concepts-apis-sdks.md) は、失敗した要求に対する再試行ロジックを実装しています。そのため、提供された SDK を使用している場合は、この機能が既に組み込まれています。 それ以外の場合は、使用しているアプリケーションに再試行ロジックを実装することを検討してください。 サービスは、失敗の応答で `Retry-After` ヘッダーを返送します。これを使用して、再試行までの待機時間を特定できます。
* **しきい値と通知を使用して、制限に近づいていることを警告します。** Azure Digital Twins のサービスの制限には、対応する[メトリック](troubleshoot-metrics.md)があり、これを使用して、これらの領域での使用状況を追跡することができます。 しきい値を構成し、しきい値に近づいたときのメトリックに関するアラートを設定するには、[アラート設定のトラブルシューティング](troubleshoot-alerts.md)に関するページにある手順を参照してください。 メトリックが提供されない他の制限の通知を設定するには、使用しているアプリケーションのコードにこのロジックを実装することを検討してください。
* **複数のインスタンスにわたって大規模にデプロイします。** 単一障害点を回避します。 デプロイ全体に対して 1 つの大きなグラフを作成するのではなく、ツインのサブセットをリージョンまたはテナントごとなどの、複数のインスタンスに論理的に分割することを検討してください。 

## <a name="next-steps"></a>次のステップ

Azure Digital Twins の最新リリースの詳細については、サービスの概要に関するページを参照してください。
* [Azure Digital Twins とは](overview.md)
