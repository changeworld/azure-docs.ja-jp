---
title: Azure Application Insights のユーザー動作分析ツールをトラブルシューティングする
description: トラブルシューティング ガイド - Application Insights でのサイトとアプリの利用状況の分析。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/11/2018
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 725f67af8178c6c851999d18c771ebdd360d6d01
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992375"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Application Insights のユーザー動作分析ツールをトラブルシューティングする
[Application Insights のユーザー動作分析ツール](app-insights-usage-overview.md): [ユーザー、セッション、イベント](app-insights-usage-segmentation.md)、[じょうご](usage-funnels.md)、[ユーザー フロー](app-insights-usage-flows.md)、[リテンション](app-insights-usage-retention.md)、またはコーホートについて質問がありますか?  いくつかの答えを次に示します。

## <a name="counting-users"></a>ユーザーのカウント
**ユーザー動作分析ツールでは、アプリにはユーザー/セッションが 1 つだけあることが示されていますが、私はアプリには複数のユーザー/セッションがあることを知っています。これらの正しくないカウントを修正するにはどうすればよいですか。**

Application Insights のすべてのテレメトリ イベントには、2 つの標準プロパティとして[匿名ユーザー ID](application-insights-data-model-context.md) と[セッション ID](application-insights-data-model-context.md) があります。 既定では、すべての利用状況分析ツールは、これらの ID に基づいてユーザーとセッションをカウントします。 これらの標準プロパティにアプリの各ユーザーとセッションの一意の ID が設定されていない場合、利用状況分析ツールには正しくないユーザーとセッションのカウントが表示されます。

Web アプリを監視している場合の最も簡単なソリューションは、[Application Insights JavaScript SDK](app-insights-javascript.md) をアプリに追加し、監視する各ページにスクリプト スニペットが読み込まれていることを確認することです。 JavaScript SDK は自動的に匿名ユーザーとセッション ID を生成し、アプリからテレメトリ イベントを送信するときにこれらの ID を設定します。

ユーザー インターフェイスのない Web サービスを監視している場合は、サービスの一意のユーザーとセッションの概念に従って、[匿名ユーザー ID プロパティとセッション ID プロパティを設定するテレメトリ初期化子を作成します](app-insights-usage-send-user-context.md)。

アプリが[認証ユーザー ID](app-insights-api-custom-events-metrics.md#authenticated-users) を送信する場合は、ユーザー ツールで認証ユーザー ID に基づいてカウントできます。 [表示] ドロップダウンで、[認証ユーザー] を選択します。

ユーザー動作分析ツールは、現時点では、匿名ユーザー ID、認証ユーザー ID、またはセッション ID 以外のプロパティに基づくユーザーまたはセッションのカウントはサポートしていません。

## <a name="naming-events"></a>イベントの名前付け
**アプリのページ ビューとカスタム イベント名が数千もあります。それらを見分けるのは難しく、さらにユーザー動作分析ツールがしばしば応答しなくなります。これらの名前付けの問題を修正するにはどうすればよいですか。**

ページ ビューとカスタム イベント名は、ユーザー動作分析ツール全体で使用されます。 イベントに適切な名前を付けることは、これらのツールから価値を引き出すためにきわめて重要です。 目標は、少数の過度に汎用的な名前 ("ボタンがクリックされた") と、多数の過度に具体的な名前 ("http://www.contoso.com/index で [編集] ボタンがクリックされた") の間でバランスを取ることです。

アプリが送信するページ ビューとカスタム イベント名を変更するには、アプリのソース コードを変更し、再展開する必要があります。 **Application Insights のすべてのテレメトリ データは 90 日間保存され、削除することはできません。** このため、イベント名に対して行った変更が完全に有効になるには 90 日かかります。 名前を変更した後 90 日間は、新旧のイベント名の両方がテレメトリに表示されるため、適切な方法でクエリを調整し、チームに伝達してください。

アプリが送信するページ ビューの名前が多すぎる場合は、これらのページ ビュー名がコード内に手動で指定されているか、Application Insights JavaScript SDK によって自動的に送信されているかをチェックします。

* ページ ビューの名前が [`trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) を使用してコード内に手動で指定されている場合は、具体性が少ない名前に変更します。 ページ ビューの名前の中に URL を配置するような一般的な誤りを回避します。 代わりに、`trackPageView` API の URL パラメーターを使用します。 その他の詳細をページ ビューの名前からカスタム プロパティに移動します。

* Application Insights JavaScript SDK がページ ビューの名前を自動的に送信している場合は、ページのタイトルを変更するか、ページ ビュー名の手動送信に切り替えます。 SDK は、既定では、各ページの[タイトル](https://developer.mozilla.org/docs/Web/HTML/Element/title)をページ ビューの名前として送信します。 より一般的なタイトルに変更できますが、この変更による SEO とその他の影響の可能性に注意してください。 `trackPageView` API を使用してページ ビューの名前を手動で指定すると、自動的に収集される名前がオーバーライドされるため、ページ タイトルの変更なしで、より一般的な名前をテレメトリで送信できます。   

アプリによって送信されるカスタム イベント名が多すぎる場合は、コード内の名前を具体性の少ないものに変更します。 この場合も、URL やその他のページ単位の情報や動的情報をカスタム イベント名の中に直接配置するのは避けてください。 代わりに、これらの詳細を、`trackEvent` API を使用して、カスタム イベントのカスタム プロパティに移動します。 たとえば、`appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` の代わりに、`appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` のようにすることをお勧めします。

## <a name="next-steps"></a>次の手順

* [ユーザー動作分析ツールの概要](app-insights-usage-overview.md)

## <a name="get-help"></a>問い合わせ
* [スタック オーバーフロー](http://stackoverflow.com/questions/tagged/ms-application-insights)

