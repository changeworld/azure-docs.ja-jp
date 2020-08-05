---
title: Azure Monitor でメトリックを表示する
titleSuffix: Azure Digital Twins
description: Azure Monitor で Azure Digital Twins メトリックを表示する方法について確認します。
author: baanders
ms.author: baanders
ms.date: 7/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6db571d64b0fd276519f15a3984848e80c4e18a
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387678"
---
# <a name="view-and-understand-azure-digital-twins-metrics"></a>Azure Digital Twins メトリックを表示して理解する

これらのメトリックにより、Azure サブスクリプション内の Azure Digital Twins リソースの状態に関する情報が提供されます。 Azure Digital Twins メトリックは、Azure Digital Twins サービスとそれに接続されているリソースの全体的な正常性を評価するのに役立ちます。 これらのユーザー向けの統計情報は、Azure Digital Twins で何が起きているかを確認するのに役立ち、Azure サポートに連絡することなく問題の根本原因の分析を実行する助けとなります。

メトリックは既定で有効になっています。 Azure Digital Twins メトリックは [Azure portal](https://portal.azure.com) で表示できます。

## <a name="how-to-view-azure-digital-twins-metrics"></a>Azure Digital Twins メトリックを表示する方法

1. Azure Digital Twins インスタンスを作成します。 Azure Digital Twins インスタンスを設定する方法については、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-scripted.md)に関するページを参照してください。

2. [Azure portal](https:/portal.azure.com) で Azure Digital Twins インスタンスを見つけます (ポータルの検索バーに名前を入力して、そのページを開くことができます)。 

    インスタンスのメニューから、 **[メトリック]** を選択します。
   
    :::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins の [メトリック] ページが示されているスクリーンショット":::

    このページには、Azure Digital Twins インスタンスのメトリックが表示されます。 また、リストから表示したいメトリックを選択して、メトリックのカスタム ビューを作成することもできます。
    
3. メトリック データを Event Hubs エンドポイントまたは Azure Storage のアカウントに送信するように選択するには、メニューから **[診断設定]** 、 **[診断設定の追加]** の順にクリックします。

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="[診断設定] ページと追加するボタンが示されているスクリーンショット":::

    このプロセスの詳細については、[*トラブルシューティング: 診断の設定*](troubleshoot-diagnostics.md)に関するページを参照してください。

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure Digital Twins メトリックとその使用方法

Azure Digital Twins には、インスタンスとその関連リソースの正常性の概要を提供するいくつかのメトリックが用意されています。 複数のメトリックから得た情報を組み合わせることで、インスタンスの状態をより大局的に把握することもできます。 

次の表は、各 Azure Digital Twins インスタンスによって追跡されるメトリックと、各メトリックがインスタンスの全体的な状態にどのように関連しているかを示しています。

#### <a name="api-request-metrics"></a>API 要求のメトリック

API 要求に関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API 要求 (プレビュー) | Count | 合計 | Digital Twins の読み取り、書き込み、削除、およびクエリ操作に対して行われた API 要求の数。 |  認証 </br>操作 </br>Protocol </br>状態コード </br>状態コード クラス </br>状態テキスト |
| ApiRequestsLatency | API Requests Latency (API 要求の待機時間) (プレビュー) | ミリ秒 | Average | API 要求の応答時間。 これは、Azure Digital Twins が要求を受け取ってから、サービスが Digital Twins の読み取り、書き込み、削除、クエリ操作の成功/失敗の結果を送信するまでの時間を指します。 | 認証 </br>操作 </br>Protocol |
| ApiRequestsFailureRate | API Requests Failure Rate (API 要求の失敗率) (プレビュー) | Percent | Average | インスタンスに対してサービスが受信する API 要求のうち、Digital Twins の読み取り、書き込み、削除、クエリ操作に対して内部エラー (500) 応答コードを返す API 要求の割合。 | 認証 </br>操作 </br>Protocol </br>状態コード </br>状態コード クラス </br>状態テキスト

#### <a name="routing-metrics"></a>ルーティング メトリック

ルーティングに関連するメトリック:

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ルーティング | ルーティング (プレビュー) | Count | 合計 | Event Hub、Service Bus、Event Grid など、エンドポイントの Azure サービスにルーティングされたメッセージの数。 | 操作 </br>結果 |
| RoutingLatency | Routing Latency (ルーティングの待機時間) (プレビュー) | ミリ秒 | Average | イベントが Azure Digital Twins からルーティングされてから、Event Hub、Service Bus、Event Grid などのエンドポイントの Azure サービスにポストされるまでの経過時間。 | 操作 </br>結果 |
| RoutingFailureRate | Routing Failure Rate (ルーティングの失敗率) (プレビュー) | Percent | Average | イベントが Azure Digital Twins からエンドポイントの Azure サービス (Event Hub、Service Bus、Event Grid など) にルーティングされるときにエラーが発生するイベントの割合。 | 操作 </br>結果 |

#### <a name="billing-metrics"></a>課金メトリック

課金に関連するメトリック:

>[!NOTE]
> プレビュー期間中は、**課金されません**。 これらのメトリックは選択可能なリストに表示されますが、プレビュー期間中は適用されません。また、サービスがプレビューの範囲を超えない限り、0 のままになります。

| メトリック | メトリックの表示名 | ユニット | 集計の種類| 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Billing API Operations (課金 API 操作) (プレビュー) | Count | 合計 | Azure Digital Twins サービスに対して行われたすべての API 要求の数の課金メトリック。 | `MeterId` |
| BillingQueryUnits | Billing Query Units (課金クエリ単位) (プレビュー) | Count | 合計 | クエリを実行するために消費された、内部で計算されたサービス リソース使用量のメジャーであるクエリ単位の数。 クエリ単位の測定に使用できるヘルパー API もあります。[QueryChargeHelper クラス](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | `MeterId` |
| BillingMessagesProcessed | Billing Messages Processed (処理されたメッセージの課金) (プレビュー) | Count | 合計 | Azure Digital Twins から外部エンドポイントに送信されたメッセージ数の課金メトリック。 | `MeterId` |

## <a name="dimensions"></a>Dimensions

ディメンションは、メトリックに関する詳細を特定するのに役立ちます。 一部のルーティング メトリックでは、エンドポイントごとの情報が提供されます。 次の表に、これらのディメンションに可能な値を示します。

| Dimension | 値 |
| --- | --- |
| 認証 | OAuth |
| 操作 (API 要求用) | Microsoft.DigitalTwins/digitaltwins/delete</br>Microsoft.DigitalTwins/digitaltwins/write</br>Microsoft.DigitalTwins/digitaltwins/read </br>Microsoft.DigitalTwins/eventroutes/read </br>Microsoft.DigitalTwins/eventroutes/write </br>Microsoft.DigitalTwins/eventroutes/delete </br>Microsoft.DigitalTwins/models/read </br>Microsoft.DigitalTwins/models/write </br>Microsoft.DigitalTwins/models/delete </br>Microsoft.DigitalTwins/query/action |
操作 (ルーティング用) | Event Grid </br>イベント ハブ </br>Service Bus |
| Protocol | HTTPS |
| 結果 | Success </br>障害 |
| 状態コード | 200、404、500 など。 |
| 状態コード クラス | 2xx、4xx、5xx など。 |
| 状態テキスト | 内部サーバー エラー、見つかりませんなど。 |

## <a name="next-steps"></a>次のステップ

Azure Digital Twins の記録されたメトリックの管理の詳細については、[*トラブルシューティング: 診断の設定*](troubleshoot-diagnostics.md)に関するページを参照してください。

または、Azure Digital Twins メトリックの概要を確認したので、次のリンクに従って Azure Digital Twins の管理の詳細をご覧ください。

* [*方法: Azure Digital Twins の API および SDK を使用する*](how-to-use-apis-sdks.md)
* [*方法: カスタム モデルを管理する*](how-to-manage-model.md)"
* [*方法: Digital Twins を管理する*](how-to-manage-twin.md)
* [*方法: リレーションシップを使用してツイン グラフを管理する*](how-to-manage-graph.md)
* [*方法: エンドポイントとルートを管理する*](how-to-manage-routes.md)