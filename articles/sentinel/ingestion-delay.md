---
title: Microsoft Sentinel でインジェストの遅延を処理する | Microsoft Docs
description: Microsoft Sentinel のスケジュールされた分析ルールでインジェストの遅延を処理します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2021
ms.author: bagol
ms.openlocfilehash: 2ce809d9121f97f885888f927a7f1dd2138f2816
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720893"
---
# <a name="handle-ingestion-delay-in-scheduled-analytics-rules"></a>スケジュールされた分析ルールでインジェストの遅延を処理する

Microsoft Sentinel では[さまざまなソース](connect-data-sources.md)からデータを取り込む場合ができますが、状況によって各データ ソースのインジェスト時間が異なる可能性があります。

この記事では、インジェストの遅延が、スケジュールされた分析ルールにどのような影響を与えるか、また、どのように修正すれば、これらのギャップをカバーすできるかについて説明します。

## <a name="why-delay-is-significant"></a>遅延が重大な理由

たとえば、カスタム検出ルールを作成し、 **[クエリの実行間隔]** および **[Lookup data from last]\(データを最後から検索\)** フィールドを、ルールが 5 分ごとに実行され、その最後の 5 分間からデータを検索するように設定できます。

:::image type="content" source="media/ingestion-delay/create-rule.png" alt-text="分析ルール ウィザード - 新しいルールの作成ウィンドウを示すスクリーンショット。" border="false":::

**[Lookup data from last]\(データを最後から検索\)** フィールドには、"*ルックバック*" 期間と呼ばれる設定を定義します。 遅延がない場合は、次の図に示すように、この検出で見逃されるイベントがないことが理想的です。

:::image type="content" source="media/ingestion-delay/look-back.png" alt-text="5 分間のルックバック枠が表示されている図。" border="false":::

イベントは生成されると同時に到着し、"*ルックバック*" 期間に含まれます。

次に、データ ソースに何らかの遅延があるとします。 この例では、イベントが "*生成された*" 2 分後に "*取り込まれた*" とします。 この遅延は 2 分です。

:::image type="content" source="media/ingestion-delay/look-back-delay.png" alt-text="5 分間のルックバック枠と 2 分の遅延が示されている図。" border="false":::

イベントは最初のルックバック期間中に生成されますが、最初の実行時に Microsoft Sentinel ワークスペースに取り込まれません。 このイベントは、スケジュールされたクエリが次に実行されるときに取り込まれますが、イベントの発生から 5 分を超過しているため、時間生成フィルターによって削除されます。 この場合、**ルールによるアラートの発生はありません**。

## <a name="how-to-handle-delay"></a>遅延を処理する方法

この問題を解決するには、お使いのデータ型に対する遅延を知る必要があります。 この例では、遅延が 2 分であることは既にわかっています。 

独自のデータの場合は、Kusto `ingestion_time()` 関数を使用して遅延を把握し、**TimeGenerated** とインジェスト時間の差を計算できます。 詳細については、「[インジェストの遅延を計算する](#calculate-ingestion-delay)」を参照してください。

遅延を判断した後、次のように問題に対処できます。

- **ルックバック期間を長くする**。 基本的な直感では、ルックバック期間のサイズを大きくすればよさそうです。 ルックバック期間は 5 分、遅延は 2 分なので、ルックバック期間を *7* 分に設定すると、この問題に対処できるようになります。 たとえば、ルール設定で、次のように設定します。

    :::image type="content" source="media/ingestion-delay/set-look-back.png" alt-text="ルックバック枠を 7 分に設定しているスクリーンショット。":::

    次の図は、見逃されたイベントがどのようにルックパック期間に含まれるようになったのかを示しています。

    :::image type="content" source="media/ingestion-delay/longer-look-back.png" alt-text="7 分間のルックバック枠と 2 分の遅延が示されている図。" border="false":::

- **重複を処理する**。 ルックバック期間を単に長くするだけでは、ルックバック枠が重なることになるため、重複が生じる可能性があります。 たとえば、次の図に示すように、別のイベントが表示される可能性があります。

    :::image type="content" source="media/ingestion-delay/overlapping-look-back.png" alt-text="ルックバック枠が重なっているために重複が作成されている図。" border="false":::

    イベント **TimeGenerated** 値が両方のルックバック期間で見つかるため、このイベントから 2 つのアラートが発生します。 この重複を解決する方法を見つける必要があります。

- **イベントを特定のルックバック期間に関連付ける**。 最初の例では、スケジュールされたクエリの実行時にデータが取り込まれなかったため、イベントを見逃しました。 イベントが含まれるようにルックバックを拡張しましたが、この結果、重複が発生しました。 イベントを、それを格納するために拡張した枠に関連付ける必要があります。

    これを行うには、元のルール `look-back = 5m` の代わりに、`ingestion_time() > ago(5m)` を設定します。 この設定を使って、イベントを最初のルックバック枠に関連付けます。 次に例を示します。

    :::image type="content" source="media/ingestion-delay/ago-restriction.png" alt-text="ago 制限を設定して重複を回避する方法を示す図。" border="false":::

    インジェスト時間を制限すると、今度は、ルックバック期間に追加した余分の 2 分が切り取られます。 最初の例では、2 回目の実行のルックバック期間にイベントがキャプチャされるようになります。

    :::image type="content" source="media/ingestion-delay/ago-restriction-capture.png" alt-text="ago 制限を設定してイベントをキャプチャする方法を示す図。" border="false":::

次のサンプル クエリは、インジェストの遅延の問題解決のソリューションの概要を示しています。

```kusto
let ingestion_delay = 2min;
let rule_look_back = 5min;
CommonSecurityLog
| where TimeGenerated >= ago(ingestion_delay + rule_look_back)
| where ingestion_time() > ago(rule_look_back)
```


## <a name="calculate-ingestion-delay"></a>インジェストの遅延を計算する

既定では、Microsoft Sentinel のスケジュールされたアラート ルールには、5 分間のルックバック期間が構成されています。 しかしながら、各データ ソースで、独自の個別のインジェストの遅延が発生する場合があります。 複数のデータ型を結合する場合、ルックバック期間を正しく構成するには、データ型ごとに異なる遅延を把握する必要があります。

Microsoft Sentinel に用意されている **ワークスペース使用状況レポート** には、ワークスペースに入るさまざまなデータ型の待機時間と遅延を示すダッシュボードが含まれます。

次に例を示します。

:::image type="content" source="media/ingestion-delay/end-to-end-latency.png" alt-text="ワークスペース使用状況レポートにテーブル別のエンドツーエンドの待機時間が示されているスクリーンショット":::


## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)
- [Azure Sentinel でアラートの詳細をカスタマイズする](customize-alert-details.md)
- [Azure Sentinel でスケジュール化された分析ルールのテンプレート バージョンを管理する](manage-analytics-rule-templates.md)