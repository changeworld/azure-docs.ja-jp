<properties 
   pageTitle="Operations Manager からのアラートの表示"
   description="インフラストラクチャの監視対象サーバーに対する Operations Manager からのアラートの管理について説明します。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />



# Operations Manager アラートの表示

Microsoft Azure オペレーション インサイトでアラートの管理を使用するには、インテリジェンス パックが事前にインストールされている必要があります。インテリジェンス パックのインストールの詳細については、「[ギャラリーを使用し、インテリジェンス パックを追加または削除する](../operational-insights-add-intelligence-packs.md)」を参照してください。インテリジェンス パックは、サーバーが Operations Manager エージェントによって監視されている場合にのみ機能します。オペレーション インサイトでの Operations Manager の使用については、[マシン データを収集する](../operational-insights-collect-data.md) に関するページを参照してください。

インテリジェンス パックをインストールすると、オペレーション インサイトの **[概要]** ダッシュボードの **[アラートの管理]** タイルを使用して監視対象サーバーのアラートを表示できます。 

![[アラートの管理] タイルの画像](./media/operational-insights-alerts/overview-alert.png)


**[アラート]** ダッシュボードで Microsoft Azure オペレーション インサイトのアラートを表示し、管理することができます。ダッシュボードでは、アラート情報は次のカテゴリ別に表示されます。

- アクティブなアラート
	- 重大なアラート
	- 警告アラート
	- アラートのソース
- すべてのアクティブなアラート
	- 重大、警告、および情報のアラートの割合を表示します。
- 一般的なアラート クエリ
	- この領域には、オペレーション インサイトのソフトウェア開発チームがあらかじめ作成した、アラートの使用に役立つクエリが含まれています。


アラートには、問題が検出されたこと、アラートの影響を受けるサーバー、優先度、およびアラートの名前が示されます。

![[アラート] ダッシュボードの画像](./media/operational-insights-alerts/alert-drilldown1.png)

![[アラート] ダッシュボードの画像](./media/operational-insights-alerts/alert-drilldown2.png)


**[アラートの管理]** ダッシュボードには、Microsoft Azure オペレーション インサイトが検出したすべての警告を表示することができます。

## オペレーション インサイトのアラートを表示するには

1. **[概要]** ページで、**[アラートの管理]** タイルをクリックします。

2. **[アラートの管理]** ダッシュボードで、アラートのカテゴリを表示し、使用するカテゴリを選択します。

3. タイルまたは任意の項目をクリックして、**[検索]** ページに詳細情報を表示します。

4. 表示された情報を使用して、アラートを調査し、問題を解決するために必要な追加のアクションを決定します。


<!--HONumber=52--> 