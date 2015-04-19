<properties 
	pageTitle="エンド ユーザー分析の使用方法" 
	description="Microsoft Azure Websites のエンド ユーザー分析です。" 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>

# Microsoft Azure Web サイトの分析

何人のユーザーがサイトにアクセスしたでしょうか。ページの平均読み込み時間とはどれくらいで、どのブラウザーが使用されているでしょうか。Web ページに数行のスクリプトを挿入すると、顧客が Web サイトをどのように使用しているかを示すデータを収集できます。 

*これは Azure 以外の Web サイトでも実行できます。[Application Insights による Web アプリケーションの使用状況の監視](app-insights-web-track-usage.md)*

![End User Analytics](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

## エンド ユーザー分析機能を設定する方法

1. **[Web サイト]** ブレードの **[エンド ユーザー分析]** パーツをクリックします。
2. **[構成]** ブレードで、インストルメンテーション スクリプト全体を選択し、コピーします。  
    ![Configuration](./media/insights-usage-analytics/Insights_CopyCode.png)
3. 各 Web ページで、</head> タグを閉じる直前に、このスクリプトを貼り付けます。すべての Web ページにこのスクリプトを挿入することをお勧めします。ASP.NET を使用している場合は、アプリケーションのマスター ページにこのスクリプトを挿入する方法で、この作業を実現できます。
4. Web アプリケーションをデプロイし、使用します。約 5 ～ 10 分後に、利用状況分析情報の表示が開始されます。

## データの探索

[ブラウザー セッション] パーツを使用して、さまざまなブラウザーの詳細と、それらのブラウザーのバージョンを表示することができます。

![Browsers](./media/insights-usage-analytics/Insights_Browsers.png)

[分析] パーツでは、次のものが表示されます。

- デスクトップやモバイルを含む、さまざまなデバイスの種類ごとの分類。
- 過去 1 週間における上位 5 つのページと、ページの読み込み時間を示すグラフ。セッション数と表示回数も利用できます。  
    ![Top Pages](./media/insights-usage-analytics/Insights_TopPages.png)
- 過去 1 週間における最も遅いページも表示されるため、ビジネスの目標と目的を満たすために、それらのページ改善することができます。


<!--HONumber=46--> 
