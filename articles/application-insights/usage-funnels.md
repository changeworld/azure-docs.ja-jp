---
title: "Azure Application Insights のファネル"
description: "ファネルを使って、顧客によるアプリケーションの操作状況を把握する方法を説明します。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Application Insights のファネルを使って、顧客がどのようにアプリケーションを利用しているかを把握します。

カスタマー エクスペリエンスを理解することは、ビジネスにとって極めて重要です。 アプリケーションが複数のステージに関わっている場合は、ほとんどの顧客がプロセス全体を円滑に処理しているか、または任意の時点でプロセスを終了しているかを把握する必要があります。 Web アプリケーションにおける一連の手順の進行は、"ファネル" と呼ばれています。 Application Insights のファネルを使って、ユーザーに対する洞察を得て、手順ごとの変換率を監視します。 

## <a name="create-your-funnel"></a>ファネルを作成する
ファネルを作成する前に、自分が答えを知りたいテーマを決めておく必要があります。 たとえば、ホーム ページを閲覧しているユーザー数、顧客プロファイルを閲覧しているユーザー数、チケットを作成しているユーザー数を知りたい場合があるとします。 この例では、Fabrikam Fiber 社の事業主は、顧客チケットの作成に成功した顧客の割合を知りたいと考えています。

この事業主がファネルを作成するために行う手順は、次のとおりです。

1. じょうごツールの [新規] ボタンをクリックします。
1. **[時間の範囲]** ボックスの一覧から [90 日以内] という時間の範囲を選択します。 [私のじょうご] または [共有じょうご] を選択します。
1. **[手順 1]** ボックスの一覧から、**[インデックス]** イベントを選択します。 
1. **[手順 2]** ボックスの一覧から、**[顧客]** イベントを選択します。
1. **[手順 3]** ボックスの一覧から、**[作成]** イベントを選択します。
1. ファネルに名前を付けて、**[保存]** をクリックします。

次の図は、じょうごツールによって生成されたデータを示します。 ここから、Fabrikam の事業主は、過去 90 日間に、ホーム ページを閲覧した顧客の 54.3% が顧客チケットを作成したことがわかります。 また、顧客の 2,700 人がホーム ページからインデックスに来たこともわかりました。これは、更新に関する問題を示す可能性があります。


![じょうごツールとデータ](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>じょうごの機能
1. アプリがサンプリングされる場合は、サンプリング バナーが表示されます。 バナーをクリックすると、サンプリングをオフにする方法を示すコンテキスト ペインが表示されます。 
2. じょうごを [Power BI](app-insights-export-power-bi.md) にエクスポートできます。
3. 手順をクリックすると、右側により詳しい洞察が表示されます。 
4. 履歴の変換により、過去 90 日間の変換を示します。 
5. じょうごからユーザーのツールに移動すると、ユーザーについて理解を深めることができます。 各手順では、選別されたユーザー フィルターが表示されます。 

## <a name="next-steps"></a>次のステップ
  * [利用状況の概要](app-insights-usage-overview.md)
  * [ユーザー、セッション、およびイベント](app-insights-usage-segmentation.md)
  * [保持](app-insights-usage-retention.md)
  * [ブック](app-insights-usage-workbooks.md)
  * [ユーザー コンテキストの追加](app-insights-usage-send-user-context.md)
  * [Power BI へのエクスポート](app-insights-export-power-bi.md)

