---
title: Azure Application Insights データを利用したカスタム レポートの自動化
description: Azure Application Insights データを利用して 日/週/月 1 回のレポートを自動化します
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072659"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Azure Application Insights データを利用したカスタム レポートの自動化

定期レポートは､ビジネスに重要なサービスの実施状況についてチームが常に情報を入手するのに役立ちます｡ 開発者や DevOps/SRE チーム､彼らのマネージャーは､誰もポータルにログオンしなくても､自動化されたレポートによって信頼できる形で知見が提供されることで生産的であることができます｡ そうしたレポートによって､アラート ルールをトリガーすることのない待ち時間や負荷､故障発生率の漸増を発見することもできます｡

エンタープライズはそれぞれに次のような独自のレポート ニーズがあります｡ 

* メトリックの特定の百分率集計またはカスタム メトリックのレポート
* 報告対象者に応じたデータの 日､ 週､および月 1 回のロールアップ レポート
* リージョンや環境などのカスタム 属性別に区分けしたレポート 
* いくつかの AI リソースを 1 つのグループにまとめたレポート (リソースの属するサブスクリプションあるいはリソース グループが異なっていてもよい)
* 報告対象者別の送信した重要なメトリックからなるレポート
* ポータル リソースへのアクセス権を持たない利害関係者向けレポート

> [!NOTE] 
> 週 1 回の Application Insights ダイジェスト 電子メールはカスタマイズができず、以下のカスタム オプションで代替できることから廃止されます。 週 1 回の最後のダイジェスト電子メールは 2018 年 6 月 11 日に送信されます。 次のオプションのいずれかを構成して、類似のカスタム レポートを取得するようにしてください （以下で提案するクエリを利用）。

## <a name="to-automate-custom-report-emails"></a>カスタム レポート電子メールを自動化する

[Application Insights データに対してプログラムからクエリを実行する](https://dev.applicationinsights.io/) ことで、スケジュールに従ってカスタム レポートを作成できます。 次のオプションを利用して、すぐに始めることができます。

* [Microsoft Flow を利用してレポートを自動化する](app-insights-automate-with-flow.md)
* [Logic Apps を利用してレポートを自動化する](automate-with-logic-apps.md)
* Monitoring シナリオで "Application Insights scheduled digest" [Azure 関数](https://azure.microsoft.com/services/functions/)テンプレートを利用する。 この関数は SendGrid を使って電子メールを配信します。 

    ![Azure 関数テンプレート](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>月 1 回のレポート用のクエリ例
次のクエリは、レポートのような月 1 回のダイジェスト電子メール用に複数のデータセットにまたがって結合を行う例です。 必要に応じてカスタマイズし、上記のオプションと組み合わせることで週 1 回のレポートを自動化することができます。   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>次の手順

- [Analytics クエリ](app-insights-analytics-using.md)の作成についての詳細を見る
- [Application Insights データに対してプログラムからクエリを実行する](https://dev.applicationinsights.io/)の詳細を見る
- [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) の詳細を見る
- [Microsoft Flow](https://ms.flow.microsoft.com)についての詳細を見る


