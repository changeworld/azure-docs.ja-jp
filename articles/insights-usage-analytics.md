<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills" />

# Web サイトの分析

何人のユーザーがサイトにアクセスしたでしょうか。ページの平均読み込み時間とはどれくらいで、どのブラウザーが使用されているでしょうか。Web ページに数行のスクリプトを挿入すると、顧客が Web サイトをどのように使用しているかを示すデータを収集できます。

![エンド ユーザー分析][エンド ユーザー分析]

## エンド ユーザー分析機能を設定する方法

1.  **[Web サイト]** ブレードの **[エンド ユーザー分析]** パーツをクリックします。
2.  **[構成]** ブレードで、インストルメンテーション スクリプト全体を選択し、コピーします。<br />
    ![構成][構成]
3.  各 Web ページで、タグを閉じる直前に、このスクリプトを貼り付けます。
    タグ) の値になります。すべての Web ページにこのスクリプトを挿入することをお勧めします。ASP.NET を使用している場合は、アプリケーションのマスター ページにこのスクリプトを挿入する方法で、この作業を実現できます。
4.  Web アプリケーションをデプロイし、使用します。約 5 ～ 10 分後に、利用状況分析情報の表示が開始されます。

## データの探索

[ブラウザー セッション] パーツを使用して、さまざまなブラウザーの詳細と、それらのブラウザーのバージョンを表示することができます。

![ブラウザー][ブラウザー]

[分析] パーツでは、次のものが表示されます。

-   デスクトップやモバイルを含む、さまざまなデバイスの種類ごとの分類。
-   過去 1 週間における上位 5 つのページと、ページの読み込み時間を示すグラフ。セッション数と表示回数も利用できます。<br />
    ![トップ ページ][トップ ページ]
-   過去 1 週間における最も遅いページも表示されるため、ビジネスの目標と目的を満たすために、それらのページ改善することができます。

  [エンド ユーザー分析]: ./media/insights-usage-analytics/Insights_ConfiguredExperience.png
  [構成]: ./media/insights-usage-analytics/Insights_CopyCode.png
  [ブラウザー]: ./media/insights-usage-analytics/Insights_Browsers.png
  [トップ ページ]: ./media/insights-usage-analytics/Insights_TopPages.png
