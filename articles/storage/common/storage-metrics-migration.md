---
title: Azure Storage メトリックの移行 | Microsoft Docs
description: 従来のメトリックを Azure Monitor によって管理される新しいメトリックに移行する方法について説明します。
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: c4dc9b231668315af16c625314c737fee99d672d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-migration"></a>Azure Storage メトリックの移行

Azure の統合モニター エクスペリエンスの戦略に合わせて、Azure Storage はメトリックを Azure Monitor プラットフォームに追加します。 今後、Azure ポリシーに基づいて、従来のメトリックのサービスは事前に通知されたうえで終了します。 従来のストレージ メトリックに依存している場合は、メトリック情報を維持するためにサービス終了日より前に移行する必要があります。

この記事では、従来のメトリックから新しいメトリックに移行する手順について説明します。

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Azure Storage によって管理される従来のメトリックを理解する

Azure Storage では、従来のメトリック値を収集し、集計して、同じストレージ アカウント内の $Metric テーブルに保存します。 Azure Portal を使用して監視グラフを設定できます。Azure Storage SDK を使用して、スキーマに基づく $Metric テーブルからデータを読み取ることもできます。 詳しくは、「[Storage Analytics](./storage-analytics.md)」をご覧ください。

従来のメトリックは、容量メトリックを BLOB に対してのみ提供し、トランザクション メトリックを BLOB、Table、File、Queue に対して提供します。

従来のメトリックは、フラット スキーマで設計されています。 このデザインでは、メトリックをトリガーするトラフィック パターンがない場合はメトリック値が 0 になります。 たとえば、ライブ トラフィックからストレージ アカウントへのサーバー タイムアウト エラーを受信しない場合でも、$Metric テーブル内の **ServerTimeoutError** は 0 に設定されます。

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Azure Monitor によって管理される新しいメトリックを理解する

新しいストレージ メトリックでは、Azure Storage は Azure Monitor バックエンドにメトリック データを出力します。 Azure Monitor は、ポータルやデータ インジェストからのデータを含む、統合監視エクスペリエンスを提供します。 詳しくは、こちらの[記事](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)をご覧ください。

新しいメトリックは、容量メトリックとトランザクション メトリックを BLOB、Table、File、Queue、Premium Storage に対して提供します。

マルチディメンションは、Azure Monitor によって提供される機能の 1 つです。 Azure Storage では、新しいメトリック スキーマの定義にこの設計が採用されています。 メトリックでサポートされているディメンションについては、「[Azure Monitor の Azure Storage メトリック](./storage-metrics-in-azure-monitor.md)」で詳細を確認できます。 マルチディメンション設計では、インジェストの帯域幅とメトリック保存の容量の両方のコスト効率が向上します。 そのため、トラフィックによって関連メトリックがトリガーされていない場合、関連メトリック データは生成されません。 たとえば、トラフィックによってサーバー タイムアウト エラーがトリガーされていない場合、ディメンション **ResponseType** が **ServerTimeoutError** と等しいメトリック **Transactions** の値をクエリしたときに Azure Monitor はデータを返しません。

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>従来のメトリックと新しいメトリック間のメトリック マッピング

メトリック データをプログラムで読み取る場合は、プログラム内で新しいメトリック スキーマを採用する必要があります。 変更をより深く理解するために、次の表に示すマッピングを参照できます。

**容量メトリック**

| 従来のメトリック | 新しいメトリック |
| ------------------- | ----------------- |
| 容量            | ディメンション BlobType が BlockBlob または PageBlob と等しい BlobCapacity |
| ObjectCount         | ディメンション BlobType が BlockBlob または PageBlob と等しい BlobCount |
| ContainerCount      | ContainerCount |

次のメトリックは、従来のメトリックでサポートされていない新しいサービスです。
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**トランザクション メトリック**

| 従来のメトリック | 新しいメトリック |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | ディメンション ResponseType が AuthorizationError と等しい Transactions |
| AnonymousClientOtherError | ディメンション ResponseType が ClientOtherError と等しい Transactions |
| AnonymousClientTimeoutError | ディメンション ResponseType が ClientTimeoutError と等しい Transactions |
| AnonymousNetworkError | ディメンション ResponseType が NetworkError と等しい Transactions |
| AnonymousServerOtherError | ディメンション ResponseType が ServerOtherError と等しい Transactions |
| AnonymousServerTimeoutError | ディメンション ResponseType が ServerTimeoutError と等しい Transactions |
| AnonymousSuccess | ディメンション ResponseType が Success と等しい Transactions |
| AnonymousThrottlingError | ディメンション ResponseType が ClientThrottlingError または ServerBusyError と等しい Transactions |
| AuthorizationError | ディメンション ResponseType が AuthorizationError と等しい Transactions |
| 可用性 | 可用性 |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | ディメンション ResponseType が ClientOtherError と等しい Transactions |
| ClientTimeoutError | ディメンション ResponseType が ClientTimeoutError と等しい Transactions |
| NetworkError | ディメンション ResponseType が NetworkError と等しい Transactions |
| PercentAuthorizationError | ディメンション ResponseType が AuthorizationError と等しい Transactions |
| PercentClientOtherError | ディメンション ResponseType が ClientOtherError と等しい Transactions |
| PercentNetworkError | ディメンション ResponseType が NetworkError と等しい Transactions |
| PercentServerOtherError | ディメンション ResponseType が ServerOtherError と等しい Transactions |
| PercentSuccess | ディメンション ResponseType が Success と等しい Transactions |
| PercentThrottlingError | ディメンション ResponseType が ClientThrottlingError または ServerBusyError と等しい Transactions |
| PercentTimeoutError | ディメンション ResponseType が ServerTimeoutError と等しいか、ResponseType が ClientTimeoutError と等しい Transactions|
| SASAuthorizationError | ディメンション ResponseType が AuthorizationError と等しい Transactions |
| SASClientOtherError | ディメンション ResponseType が ClientOtherError と等しい Transactions |
| SASClientTimeoutError | ディメンション ResponseType が ClientTimeoutError と等しい Transactions |
| SASNetworkError | ディメンション ResponseType が NetworkError と等しい Transactions |
| SASServerOtherError | ディメンション ResponseType が ServerOtherError と等しい Transactions |
| SASServerTimeoutError | ディメンション ResponseType が ServerTimeoutError と等しい Transactions |
| SASSuccess | ディメンション ResponseType が Success と等しい Transactions |
| SASThrottlingError | ディメンション ResponseType が ClientThrottlingError または ServerBusyError と等しい Transactions |
| ServerOtherError | ディメンション ResponseType が ServerOtherError と等しい Transactions |
| ServerTimeoutError | ディメンション ResponseType が ServerTimeoutError と等しい Transactions |
| 成功 | ディメンション ResponseType が Success と等しい Transactions |
| ThrottlingError | ディメンション ResponseType が ClientThrottlingError または ServerBusyError と等しい Transactions |
| TotalBillableRequests | トランザクション |
| TotalEgress | エグレス |
| TotalIngress | イングレス |
| TotalRequests | トランザクション |

## <a name="faq"></a>FAQ

* 既存のアラート ルールを移行する必要はありますか?

A: 従来のストレージ メトリックに基づいてクラシック アラート ルールを作成した場合は、新しいメトリック スキーマに基づいて新しいアラート ルールを作成する必要があります。

* 既定では、新しいメトリック データは同じストレージ アカウントに格納されますか?

A: いいえ。 メトリック データをストレージ アカウントにアーカイブする必要がある場合は、[Azure Monitor の診断設定](https://azure.microsoft.com/blog/azure-monitor-multiple-diagnostic-settings/)を使用できます

## <a name="next-steps"></a>次の手順

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure Monitor の Storage メトリック](./storage-metrics-in-azure-monitor.md)
