---
title: Microsoft Sentinel でインシデント メトリックを使用して SOC をより適切に管理する | Microsoft Docs
description: Microsoft Sentinel インシデント メトリック画面とブックからの情報を使用し、セキュリティ オペレーション センター (SOC) の管理に役立てます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 9e20c875068387342edf6fd70fe93689e26fb1ad
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523602"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>インシデント メトリックを使用して SOC をより適切に管理する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

セキュリティ オペレーション センター (SOC) のマネージャーとして、チームのパフォーマンスを評価するために、全体的な効率性のメトリックとメジャーを用意しておく必要があります。 さまざまな条件 (重要度、MITRE 戦術、平均トリアージ時間、平均解決時間など) 別に、長期にわたるインシデント操作を確認します。 Microsoft Sentinel では、このデータを Log Analytics の新しい **SecurityIncident** テーブルおよびスキーマと、付属する **[セキュリティ操作の効率性]** ブックで使用できるようになりました。 長期にわたってチームのパフォーマンスを視覚化し、その分析情報を使用して効率性を改善できます。 インシデント テーブルに対する独自の KQL クエリを作成して使用することで、特定の監査ニーズと KPI に合わせてカスタマイズされたブックを作成することもできます。

## <a name="use-the-security-incidents-table"></a>セキュリティ インシデント テーブルを使用する

**SecurityIncident** テーブルは Microsoft Sentinel に組み込まれています。 **[ログ]** にある **SecurityInsights** コレクション内のその他のテーブルでも見つかります。 Log Analytics のその他のテーブルと同様に、クエリを実行できます。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="セキュリティ インシデント テーブル":::

インシデントを作成または更新するたびに、新しいログ エントリがテーブルに追加されます。 これにより、インシデントに加えられた変更を追跡し、さらに強力な SOC メトリックを使用できます。ただし、(実行中のクエリによっては) 重複するインシデント エントリを削除する必要もあるため、このテーブルに対するクエリの構築時は、このことに注意する必要があります。 

たとえば、すべてのインシデントのリストがインシデント番号で並べ替えられて返されるが、インシデントごとに最新のログのみが返されようにする場合は、`arg_max()` [集計関数](/azure/data-explorer/kusto/query/arg-max-aggfunction)とともに KQL [集計演算子](/azure/data-explorer/kusto/query/summarizeoperator) 使用すれば実行できます。

```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>その他のサンプル クエリ

インシデントの状態 - 特定の時間枠における状態と重大度別のすべてのインシデント:

```Kusto
let startTime = ago(14d);
let endTime = now();
SecurityIncident
| where TimeGenerated >= startTime
| summarize arg_max(TimeGenerated, *) by IncidentNumber
| where LastModifiedTime  between (startTime .. endTime)
| where Status in  ('New', 'Active', 'Closed')
| where Severity in ('High','Medium','Low', 'Informational')
```

平均終了時間:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

平均トリアージ時間:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>[セキュリティ操作の効率性] ブック

**SecurityIncidents** テーブルを補完するために、すぐに使用できる **[セキュリティ操作の効率性]** ブック テンプレートが提供されています。これを使用すると、SOC 操作を監視できます。 このブックには、次のメトリックが含まれています。 
- 長期にわたって作成されたインシデント 
- 終了分類、重要度、所有者、および状態別に作成されたインシデント 
- 平均トリアージ時間 
- 平均終了時間 
- 長期にわたって重要度、所有者、状態、製品、および戦術別に作成されたインシデント 
- トリアージまでの時間 (パーセンタイル) 
- 修了までの時間 (パーセンタイル) 
- 所有者ごとの平均トリアージ時間 
- 最近のアクティビティ 
- 最近の終了分類  

この新しいブック テンプレートは、Microsoft Sentinel ナビゲーション メニューから **[ブック]** を選択し、 **[テンプレート]** タブを選択することで見つかります。ギャラリーから **[セキュリティ操作の効率性]** を選択し、 **[保存されたブックの表示]** ボタンか **[テンプレートの表示]** ボタンをクリックします。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="セキュリティ インシデント ブック ギャラリー":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="セキュリティ インシデント ブック完了":::

テンプレートを使用すると、特定のニーズに合わせて独自のカスタム ブックを作成できます。

## <a name="securityincidents-schema"></a>SecurityIncidents スキーマ

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>次のステップ

- Microsoft Sentinel を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
- [データを Microsoft Sentinel にオンボード](quickstart-onboard.md)し、[データや潜在的な脅威を視覚化する](get-visibility.md)方法を確認します。
