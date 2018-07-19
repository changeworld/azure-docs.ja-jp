---
title: Azure Log Analytics のデータ インジェスト時間 | Microsoft Docs
description: Azure Log Analytics でのデータ収集の待機時間に影響するさまざまな要因について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/10/2018
ms.author: bwren
ms.openlocfilehash: 0e513cc4f6a7d5d030ded807870de9eb0fdc0ed8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38973184"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Log Analytics のデータ インジェスト時間
Azure Log Analytics とは、毎月増加するテラバイト単位のデータを送信する何千もの顧客にサービスを提供する高スケールのデータ サービスです。 データが収集されてから、Log Analytics でそのデータが使用可能になるまでにかかる時間について、よく質問されることがあります。 この記事では、この待機時間に影響するさまざまな要因について説明します。

## <a name="typical-latency"></a>一般的な待機時間
待機時間は、データが監視対象のシステムで作成される時間と Log Analytics で分析に使用できるようになる時間を指します。 Log Analytics にデータを取り込むための一般的な待機時間は 3 - 10 分で、データの 95% は 7 分未満で取り込まれます。 特定のデータに対する具体的な待機時間は、次に説明するさまざまな要因によって異なります。

## <a name="sla-for-log-analytics"></a>Log Analytics の SLA
[Log Analytics サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) は、サービスがその目標を達成できない場合に、Microsoft がお客様に返金する場合を定めた法的効力のある契約です。 これは、システムの一般的なパフォーマンスではなく、壊滅的な状況になる可能性がある最悪のケースに基づいています。

## <a name="factors-affecting-latency"></a>待機時間に影響する要因
特定のデータ セットの合計インジェスト時間は、次の高度な領域に分割することができます。 

- エージェント時間: イベントを検出し、それを収集して、ログ レコードとして Log Analytics のインジェスト ポイントに送信する時間。 ほとんどの場合、このプロセスは、エージェントによって処理されます。
- パイプライン時間: ログ レコードを処理するインジェスト パイプラインの時間。 これには、イベントのプロパティを解析し、場合によっては計算済みの情報を追加する時間が含まれます。
- インデックス作成時間: ログ レコードを Log Analytics Big Data ストアに取り込むために費やされた時間。

このプロセスによるさまざまな待機時間の詳細を以下に示します。

### <a name="agent-collection-latency"></a>エージェント コレクションの待機時間
エージェントと管理ソリューションは、さまざまな方法を使用して仮想マシンからデータを収集します。これが待機時間に影響する場合があります。 具体的な例をいくつか次に示します。

- Windows イベント、syslog イベント、およびパフォーマンス メトリックは、すぐに収集されます。 Linux パフォーマンス カウンターは、30 秒間隔でポーリングされます。
- IIS ログとカスタム ログのタイムスタンプが変更されると、これらのログが収集されます。 IIS ログの場合、これは [IIS で構成されているロール オーバー スケジュール](log-analytics-data-sources-iis-logs.md)の影響を受けます。 
- Active Directory Assessment では Active Directory インフラストラクチャの評価が毎週実行されるのに対し、Active Directory Replication ソリューションでは評価が 5 日おきに実行されます。 エージェントは評価が完了したときにのみ、これらのログを収集します。

### <a name="agent-upload-frequency"></a>エージェントのアップロードの頻度
Log Analytics エージェントを軽量にするため、エージェントはログをバッファーし、それらを定期的に Log Analytics にアップロードします。 アップロードの頻度は、データの型に応じて 30 秒から 2 分間の範囲で異なります。 ほとんどのデータは、1 分未満でアップロードされます。 ネットワークの状態は、このデータが Log Analytics のインジェスト ポイントに達するまでの待機時間に悪影響を及ぼす可能性があります。

### <a name="azure-logs-and-metrics"></a>Azure ログとメトリック 
アクティビティ ログ データが Log Analytics で利用可能になるまで、約 5 分かかります。 診断ログとメトリックのデータは、Azure サービスに応じて、利用可能になるまでに 1 - 5 分かかります。 さらに、ログに 30 - 60 秒、Log Analytics のインジェスト ポイントに送信されるデータのメトリックに 3 分が追加でかかります。

### <a name="management-solutions-collection"></a>管理ソリューションのコレクション
一部のソリューションでは、エージェントからデータを収集せず、さらに待機時間がかかるコレクション メソッドを使用する場合があります。 一部のソリューションでは、ほぼリアルタイムでの収集を試みることなく、一定の間隔でデータを収集します。 具体的な例を次に示します。

- Office 365 ソリューションでは、Office 365 Management Activity API (現在はほぼリアルタイムの待機時間を保証していません) を使用して、アクティビティ ログがポーリングされます。
- Windows Analytics ソリューション (Update Compliance など) のデータは、毎日の頻度でソリューションによって収集されます。

各ソリューションのコレクションの頻度を判断するには、そのドキュメントを参照してください。

### <a name="pipeline-process-time"></a>パイプライン処理時間
ログ レコードは Log Analytics パイプラインに取り込まれると、テナントを分離してデータが失われないように、一時的なストレージに書き込まれます。 通常、このプロセスにより、さらに 5 - 15 秒が加算されます。 一部の管理ソリューションでは、データのストリーミング時に、データを集計し、分析情報を派生させるため、より重いアルゴリズムが実装されます。 たとえば、ネットワーク パフォーマンス監視は、3 分間隔で受信データを集計するため、事実上 3 分間の待機時間が加算されます。

### <a name="new-custom-data-types-provisioning"></a>新しいカスタム データ型のプロビジョニング
新しいカスタム データの型を[カスタム ログ](../log-analytics/log-analytics-data-sources-custom-logs.md)または[データ コレクター API](../log-analytics/log-analytics-data-collector-api.md) から作成すると、システムにより専用のストレージ コンテナーが作成されます。 これは、最初にこのデータ型が出現したときにのみ発生する 1 回限りのオーバーヘッドです。

### <a name="surge-protection"></a>急激な増加の保護
Log Analytics の最優先事項は、顧客データが失われることがないようにすることです。そのため、システムにはデータの急激な増加に対する保護が組み込まれています。 これには、膨大な負荷でもシステムが機能し続けられるようにするバッファーが含まれます。 通常の負荷では、これらのコントロールにより追加される待機時間は 1 分未満ですが、極端な状態で障害が発生した場合、データの安全を確保する一方で、膨大な時間が追加される場合があります。

### <a name="indexing-time"></a>インデックス作成時間
データにすぐにアクセスを提供するのに対して、すべての Big Data プラットフォームには、分析と高度な検索機能を提供するための組み込みの分散機能があります。 Azure Log Analytics を使用すると、何十億ものレコードに対して強力なクエリを実行し、数秒以内で結果を取得できます。 これを可能にしているのは、インフラストラクチャがデータ インジェスト中にデータを動的に変換して、それを一意のコンパクトな構造に格納しているからです。 システムは、これらの構造を作成するのに十分なデータ量になるまで、データをバッファーします。 これは、ログ レコードが検索結果に表示される前に完了する必要があります。

このプロセスは現在、データ量が少ない場合は約 5 分かかりますが、データ レートが高くなるほど時間は短くなります。 これは直感に反しているように思えますが、このプロセスにより、大量の運用環境のワークロードの待機時間の最適化が可能になります。



## <a name="checking-ingestion-time"></a>インジェスト時間のチェック
**Heartbeat** テーブルを使用して、エージェントからのデータの待機時間の推定値を取得することができます。 ハートビートは 1 分間に 1 回送信されるため、現在の時刻と最後のハートビート レコードとの差は、できるだけ 1 分に近づけるのが理想的です。

次のクエリを使用して、待機時間の最も長いコンピューターを一覧表示します。

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | top 50 by IngestionTime asc

 
大規模な環境では、次のクエリを使用して、コンピューターの合計数の異なる割合の待機時間を集計します。

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | summarize percentiles(IngestionTime, 50,95,99)



## <a name="next-steps"></a>次の手順
* Log Analytics の[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) をお読みください。

