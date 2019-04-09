---
title: Azure Front Door Service - メトリックとログ | Microsoft Docs
description: この記事では、Azure Front Door Service がサポートするさまざまなメトリックとアクセス ログについて説明します
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 5d4d591c465cae91c59e8f86ea9d3e421db0f952
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112111"
---
# <a name="monitoring-metrics-for-front-door"></a>Front Door のメトリックの監視

Azure Front Door Service を使用すると、次の方法でリソースを監視できます。
* [ログ](#diagnostic-logging):リソースのパフォーマンス、アクセス、その他のデータを記録したログは、監視のために保存し使用することができます。

* [メトリック](#metrics):現在、Application Gateway にはパフォーマンス カウンターを表示する 7 つのメトリックがあります。

## <a name="metrics"></a>メトリック

メトリックは特定の Azure リソース用の機能で、ポータルでパフォーマンス カウンターを表示できます。 Front Door について、次のメトリックを使用できます。

| メトリック | メトリックの表示名 | 単位 | Dimensions | 説明 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によって処理されるクライアント要求の数。  |
| RequestSize | 要求サイズ | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door にクライアントからの要求として送信されたバイト数。 |
| ResponseSize | 応答サイズ | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | クライアントに Front Door からの応答として送信されたバイト数。 |
| TotalLatency | 合計待機時間 | ミリ秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によってクライアント要求が受信されてから、Front Door からの最後の応答バイトがクライアントで確認されるまでの算出時間。 |
| BackendRequestCount | バックエンド要求数 | Count | HttpStatus</br>HttpStatusGroup</br>バックエンド | バックエンドに Front Door から送信された要求の数。 |
| BackendRequestLatency | バックエンド要求の待機時間 | ミリ秒 | バックエンド | Front Door によってバックエンドに要求が送信されてから、Front Door でバックエンドから最後の応答バイトが受信されるまでの算出時間。 |
| BackendHealthPercentage | バックエンドの正常性の割合 | Percent | バックエンド</br>BackendPool | Front Door からバックエンドへの成功した正常性プローブの割合。 |
| WebApplicationFirewallRequestCount | Web アプリケーション ファイアウォール要求の数 | Count | PolicyName</br>RuleName</br>Action | Front Door のアプリケーション レイヤー セキュリティによって処理されたクライアント要求の数。 |

## <a name="activity-log"></a>アクティビティ ログ

アクティビティ ログは、Front Door で実行された操作に関する情報を提供します。 アクティビティ ログを使用すると、Front Door で行われた書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。

> [!NOTE]
> アクティビティ ログには、読み取り (GET) 操作、Azure Portal で実行された操作、または元の管理 API の使用に関する情報は含まれません。

アクティビティ ログには Front Door でアクセスできます。または、Azure Monitor ですべての Azure リソースのログにアクセスすることもできます。 

アクティビティ ログを表示するには、次の手順に従います。

1. Front Door インスタンスを選択します。
2. **[アクティビティ ログ]** をクリックします。

    ![アクティビティ ログ](./media/front-door-diagnostics/activity-log.png)

3. 目的のフィルター処理の範囲を選択し、**[適用]** をクリックします。

## <a name="diagnostic-logging"></a>診断ログ
診断ログは、監査とトラブルシューティングを行うために重要な、操作とエラーについての豊富な情報を提供します。 診断ログは、アクティビティ ログとは異なります。 アクティビティ ログは、API リソースで実行された操作に関する情報を提供します。 診断ログでは、リソースが実行した操作を調査できます。 [Azure Monitor の診断ログ](../azure-monitor/platform/diagnostic-logs-overview.md)の詳細情報を確認します。 

Front Door の診断ログを構成するには、以下の手順を実行します。

1. APIM サービス インスタンスを選びます。
2. **[診断設定]** をクリックします。

    ![診断ログ](./media/front-door-diagnostics/diagnostic-log.png)

3. **[診断を有効にする]** をクリックします。 診断ログをメトリックと共にストレージ アカウントにアーカイブし、それらをイベント ハブにストリーム配信したり、Azure Monitor ログに送信したりすることができます。 

現在、Azure Front Door Service は、各エントリが次のスキーマを持つ個々の API 要求に関する診断ログ (1 時間ごとにバッチ処理) を提供します。

| プロパティ  | 説明 |
| ------------- | ------------- |
| ClientIp | 要求を行ったクライアントの IP アドレス。 |
| ClientPort | 要求を行ったクライアントの IP アドレス。 |
| HttpMethod | 要求で使用される HTTP メソッド。 |
| HttpStatusCode | プロキシから返された HTTP 状態コード。 |
| HttpStatusDetails | 要求の結果の状態。 この文字列値の意味は、状態参照テーブルで確認できます。 |
| HttpVersion | 要求または接続の種類。 |
| RequestBytes | 要求ヘッダーと要求本文を含む HTTP 要求メッセージのサイズ (バイト単位)。 |
| RequestUri | 受信した要求の URI。 |
| ResponseBytes | 応答としてバックエンド サーバーによって送信されたバイト数。  |
| RoutingRuleName | 要求が一致したルーティング規則の名前。 |
| SecurityProtocol | 要求によって使用された TLS/SSL プロトコルのバージョン。暗号化がない場合は、null 値。 |
| TimeTaken | アクションにかかった時間の長さ (ミリ秒単位)。 |
| UserAgent | クライアントで使用されたブラウザーの種類 |
| TrackingReference | Front Door によって提供された要求を識別する一意の参照文字列。X-Azure-Ref ヘッダーとしてクライアントにも送信されます。 アクセス ログで特定の要求の詳細を検索するために必要です。 |

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
