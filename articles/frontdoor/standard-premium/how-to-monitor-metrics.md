---
title: Azure Front Door Standard または Premium の監視メトリック
description: この記事では、Azure Front Door Standard または Premium の監視メトリックについて説明します。
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 72388eb8006ff1b9628db5066dc63e6a0811f3d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557328"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Azure Front Door Standard または Premium でのリアルタイム監視

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door Standard または Premium は Azure Monitor と統合されており、これには Azure Front Door Standard または Premium をリアルタイムで監視して問題を追跡、トラブルシューティング、デバッグするのに役立つ 11 個のメトリックがあります。  

Azure Front Door Standard または Premium では、メトリックが 60 秒間隔で測定されて送信されます。 メトリックがポータルに表示されるまでに、最大で 3 分かかることがあります。 メトリックは、任意のグラフやグリッドに表示でき、ポータル、PowerShell、CLI、および API を使用してアクセスできます。 詳細については、 [Azure Monitor のメトリック](../../azure-monitor/essentials/data-platform-metrics.md)に関するページを参照してください。  

既定のメトリックは無料です。 追加のメトリックを有効にできますが、追加コストがかかります。 

4XXErrorRate または 5XXErrorRate のしきい値など、各メトリックのアラートを構成できます。 エラー率がしきい値を超えると、構成されているアラートがトリガーされます。 詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/alerts/alerts-metric.md)」を参照してください。 

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Azure Front Door Standard または Premium でサポートされているメトリック

| メトリック  | 説明 | Dimensions |
| ------------- | ------------- | ------------- |
| バイト ヒット率 | 総エグレスに対して計算される、AFD キャッシュからのエグレスの割合。 </br> **バイト ヒット率** = (エッジからのエグレス - オリジンからのエグレス)/エッジからのエグレス。 </br> **バイト ヒット率の計算で除外されるシナリオ**:</br> 1. ルール エンジンまたはクエリ文字列キャッシュ動作を使用して、キャッシュなしを明示的に構成します。 </br> 2. ストアなしまたはプライベート キャッシュを使用して、キャッシュ制御ディレクティブを明示的に構成します。 </br>3. ほとんどのトラフィックが構成またはシナリオに基づいてキャッシュから提供されるのではなく、配信元に転送される場合、バイト ヒット率は低くなる可能性があります。 | エンドポイント |
| RequestCount | CDN によって処理されたクライアント要求の数。 | エンドポイント、クライアントの国、クライアントのリージョン、HTTP 状態、HTTP 状態グループ |
| ResponseSize | AFD によって処理されたクライアント要求の数。 |エンドポイント、クライアントの国、クライアントのリージョン、HTTP 状態、HTTP 状態グループ |
| TotalLatency | クライアント要求が CDN によって受信されてから、**最後の応答バイトが CDN からクライアントに送信されるまで** の合計時間。 |エンドポイント、クライアントの国、クライアントのリージョン、HTTP 状態、HTTP 状態グループ |
| RequestSize | AFD にクライアントからの要求として送信されたバイト数。 | エンドポイント、クライアントの国、クライアントのリージョン、HTTP 状態、HTTP 状態グループ |
| 4XX % ErrorRate | 応答の状態コードが 4XX であるすべてのクライアント要求の割合。 | エンドポイント、クライアントの国、クライアントのリージョン |
| 5XX % ErrorRate | 応答の状態コードが 5XX であるすべてのクライアント要求の割合。 | エンドポイント、クライアントの国、クライアントのリージョン |
| OriginRequestCount  | AFD から配信元に送信された要求の数 | エンドポイント、配信元、HTTP 状態、HTTP 状態グループ |
| OriginLatency | AFD エッジによってバックエンドに要求が送信されてから、AFD でバックエンドからの最後の応答バイトが受信されるまでの算出時間。 | エンドポイント、配信元 |
| OriginHealth% | AFD から配信元への成功した正常性プローブの割合。| 配信元、配信元グループ |
| WAF 要求数 | 一致した WAF 要求。 | アクション、ルール名、ポリシー名 |

## <a name="access-metrics-in-azure-portal"></a>Azure portal でメトリックにアクセスする

1. Azure portal メニューから、 **[すべてのリソース]**  >>  **\<your-AFD Standard/Premium (Preview) -profile>** を選択します。

2. **[監視]** で **[メトリック]** を選択します。

3. **[メトリック]** で、追加するメトリックを選択します。

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="メトリック ページのスクリーンショット。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. フィルターを追加するには、 **[フィルターの追加]** を選択します。

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="メトリックへのフィルター追加のスクリーンショット。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. さまざまなディメンション別にデータを分割するには、 **[Apply splitting]\(分割の適用\)** を選択します。

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="メトリックへのディメンション追加のスクリーンショット。" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. 新しいグラフを追加するには、 **[新しいグラフ]** を選択します。

## <a name="configure-alerts-in-azure-portal"></a>Azure portal でアラートを構成する

1. **[監視]**  >>  **[アラート]** を選択して、Azure Front Door Standard または Premium (プレビュー) にアラートを設定します。

1. [メトリック] セクションにリストされているメトリックに対して **[新しいアラート ルール]** を選択します。

アラートは Azure Monitor に基づいて課金されます。 アラートの詳細については、[Azure Monitor のアラート](../../azure-monitor/alerts/alerts-overview.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Front Door Standard または Premium のレポート](how-to-reports.md)について説明します。
- [Azure Front Door Standard または Premium のログ](how-to-logs.md)について説明します。