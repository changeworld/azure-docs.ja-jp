---
title: "Azure Monitor での Azure Service Bus メトリック (プレビュー) | Microsoft Docs"
description: "Azure 監視を使用した Service Bus エンティティの監視"
services: service-bus-messaging
documentationcenter: .NET
author: christianwolf42
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: sethm
ms.openlocfilehash: fcc7e1cbacc7889c9525207b238162e6caa6b00b
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2017
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Azure Monitor での Azure Service Bus メトリック (プレビュー)

Service Bus メトリックによって、Azure サブスクリプション内のリソースの状態が提供されます。 豊富な一連のメトリック データにより、名前空間レベルだけでなくエンティティ レベルでも、Service Bus リソースの全体的な正常性を評価できます。 これらの統計は Service Bus の状態の監視に役立つため、重要になる場合があります。 メトリックはまた、Azure サポートに問い合わせることなく、根本的な問題をトラブルシューティングするのにも役立ちます。

Azure Monitor には、さまざまな Azure サービスにわたって監視するための統合ユーザー インターフェイスが用意されています。 詳細については、「[Microsoft Azure での監視](../monitoring-and-diagnostics/monitoring-overview.md)」および GitHub 上の「[Retrieve Azure Monitor metrics with .NET (.NET を使用した Azure Monitor メトリックの取得)](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)」のサンプルを参照してください。

## <a name="access-metrics"></a>メトリックにアクセスする

Azure Monitor では、複数の方法でメトリックにアクセスできます。 メトリックには [Azure Portal](https://portal.azure.com) 経由でアクセスするか、または Azure Monitor API (REST および .NET) と Operation Management Suite や Event Hubs などの分析ソリューションを使用できます。 詳細については、「[Azure Monitor metrics (Azure Monitor メトリック)](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api)」を参照してください。

メトリックは既定で有効になっており、過去 30 日間のデータにアクセスできます。 データを長期にわたって保持する必要がある場合は、メトリック データを Azure ストレージ アカウントにアーカイブできます。 これは、Azure Monitor の[診断設定](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)で構成されます。

## <a name="access-metrics-in-the-portal"></a>ポータルでメトリックにアクセスする

[Azure Portal](https://portal.azure.com) では、メトリックを時間経過に沿って監視できます。 次の例は、アカウント レベルでの成功した要求と受信要求を表示する方法を示しています。

![][1]

また、名前空間経由でメトリックに直接アクセスすることもできます。 それを行うには、名前空間を選択してから、**[Metrics (Peview)] (メトリック (プレビュー))** をクリックします。 エンティティのスコープにフィルター処理されたメトリックを表示するには、エンティティを選択してから、**[Metrics (preview)] (メトリック (プレビュー))** をクリックします。

![][2]

ディメンションをサポートするメトリックについては、目的のディメンション値でフィルター処理する必要があります。

## <a name="billing"></a>課金

Azure Monitor でのメトリックの使用は現在、プレビュー段階にある間は無料です。 ただし、メトリック データを取り込む追加のソリューションを使用する場合は、これらのソリューションによって課金される可能性があります。 たとえば、メトリック データを Azure ストレージ アカウントにアーカイブする場合は、Azure Storage によって課金されます。 また、高度な分析のためにメトリック データを Operation Management Suite (OMS) にストリーミングする場合は、OMS によっても課金されます。

次のメトリックによって、サービスの正常性の概要が提供されます。 

> [!NOTE]
> いくつかのメトリックは別の名前で移行されているため、非推奨にしています。 これにより、参照の更新が必要になる可能性があります。 "非推奨" というキーワードでマークされているメトリックは、将来サポートされなくなります。

すべてのメトリック値が 1 分ごとに Azure Monitor に送信されます。 時間の粒度は、メトリック値が提供される時間間隔を定義します。 すべての Service Bus メトリックに対してサポートされる時間間隔は 1 分です。

## <a name="request-metrics"></a>要求のメトリック

データおよび管理操作要求の数をカウントします。

| メトリックの名前 | Description |
| ------------------- | ----------------- |
| 受信要求 (プレビュー) | 指定された期間にわたって Service Bus サービスに対して実行された要求の数。 <br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|
|成功した要求 (プレビュー)|指定された期間にわたって Service Bus サービスに対して実行された成功した要求の数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|
|サーバー エラー (プレビュー)|指定された期間にわたって Service Bus サービスでエラーのために処理されなかった要求の数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|
|ユーザー エラー (プレビュー)|指定された期間にわたってユーザー エラーのために処理されなかった要求の数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|
|調整された要求 (プレビュー)|使用量を超えたため調整された要求の数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|

## <a name="message-metrics"></a>メッセージのメトリック

| メトリックの名前 | Description |
| ------------------- | ----------------- |
|受信メッセージ (プレビュー)|指定された期間にわたって Service Bus に送信されたイベントまたはメッセージの数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|
|送信メッセージ (プレビュー)|指定された期間にわたって Service Bus から受信されたイベントまたはメッセージの数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|

## <a name="connection-metrics"></a>接続のメトリック

| メトリックの名前 | Description |
| ------------------- | ----------------- |
|アクティブな接続 (プレビュー)|名前空間およびエンティティ上のアクティブな接続の数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|
|開かれた接続 (プレビュー)|開かれている接続の数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName|
|閉じられた接続 (プレビュー)|閉じられている接続の数。<br/><br/> 単位: カウント <br/> 集計の種類: 合計 <br/> ディメンション: EntityName |

## <a name="resource-usage-metrics"></a>リソース使用状況のメトリック

| メトリックの名前 | Description |
| ------------------- | ----------------- |
|名前空間あたりの CPU 使用率 (プレビュー)|名前空間の CPU 使用率 (%)。<br/><br/> 単位: パーセント <br/> 集計の種類: 最大 <br/> ディメンション: EntityName|
|名前空間あたりのメモリ サイズ使用率 (プレビュー)|名前空間のメモリ使用率 (%)。<br/><br/> 単位: パーセント <br/> 集計の種類: 最大 <br/> ディメンション: EntityName|

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure Service Bus は、Azure Monitor でのメトリックの次のディメンションをサポートします。 メトリックへのディメンションの追加は省略可能です。 ディメンションを追加しない場合、メトリックは名前空間レベルで指定されます。 

|ディメンション名|Description|
| ------------------- | ----------------- |
|EntityName| Service Bus は、名前空間の下のメッセージング エンティティをサポートします。|

## <a name="next-steps"></a>次のステップ

「[Azure 監視の概要](../monitoring-and-diagnostics/monitoring-overview.md)」を参照してください。

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


