<properties
   pageTitle="Operations Manager からのアラートの表示"
   description="インフラストラクチャの監視対象サーバーに対する Operations Manager からのアラートの管理について説明します。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />



# Operations Manager アラートの表示

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights でアラートの管理を使用するには、ソリューションが事前にインストールされている必要があります。ソリューションのインストールについて詳しくは、[ソリューション ギャラリーを使用したソリューションの追加または削除](operational-insights-add-solution.md)に関する記事を参照してください。ソリューションは、サーバーが Operations Manager エージェントによって監視されている場合にのみ機能します。Operational Insights での Operations Manager の使用の詳細については、「[System Center Operations Manager から Operational Insights に接続する](operational-insights-connect-scom.md)」を参照してください。

ソリューションをインストールすると、Operational Insights の **[概要]** ダッシュボードの **[アラートの管理]** タイルを使用して監視対象サーバーのアラートを表示できます。

![[アラートの管理] タイルの画像](./media/operational-insights-alerts/overview-alert.png)


**[アラート]** ダッシュボードで Microsoft Azure Operational Insights のアラートを表示し、管理することができます。ダッシュボードでは、アラート情報は次のカテゴリ別に表示されます。

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


**[アラートの管理]** ダッシュボードには、Microsoft Azure Operational Insights が検出したすべての警告を表示することができます。

## オペレーション インサイトのアラートを表示するには

1. **[概要]** ページで、**[アラートの管理]** タイルをクリックします。
2. **[アラートの管理]** ダッシュボードで、アラートのカテゴリを表示し、使用するカテゴリを選択します。
3. タイルまたは任意の項目をクリックして、**[検索]** ページに詳細情報を表示します。
4. 表示された情報を使用して、アラートを調査し、問題を解決するために必要な追加のアクションを決定します。

<!---HONumber=July15_HO2-->