<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn" />

# Azure のリソースまたはリソース グループに影響を与えるイベントの監視

1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。
2.  左側のナビゲーション バー (**ジャンプ バー**とも呼びます) にある **[参照]** をクリックします。<br />
    ![ハブの参照][ハブの参照]
3.  (関心のあるイベントに応じて) リソースを選択します。説明の目的で、このドキュメントのスクリーン ショットでは、リソース グループに対応するイベントを使用しています。
4.  **[リソース グループ]** ブレードで、リソース グループの名前をクリックします。この結果、そのリソース グループに対応するブレードに移動します。<br />
    ![リソース グループ][リソース グループ]
5.  リソース グループのブレードには、**[過去 1 週間のイベント]** というパーツが含まれています。このパーツ内にある各バーは、過去 1 週間の各日に発生したイベントの数を表します。各バーに、青およびピンクという 2 つの異なる色が表示されていることがあります。ピンクはその日の**失敗**イベントを表し、青はそれ以外のすべてのイベントを表します。<br />
    ![リソース グループ][1]
6.  ここで、**[過去 1 週間のイベント]** パーツをクリックします。**[イベント]** ブレードという新しいブレードが表示されます。この中には、リソース グループに影響を及ぼした、過去 1 週間のすべてのイベントが含まれています。<br />
    ![リソース グループ][2]
7.  イベントのいずれかをクリックします。<br />
    ![リソース グループ][3]<br />
    イベントに関して多くの詳細を示す新しいブレードが表示されます。**[失敗]** イベントに関しては、このページで通常、デバッグの目的で役立つ詳細を示す**[サブステータス]** および **[プロパティ]** というセクションが表示されます。

  [Azure 管理ポータル]: https://portal.azure.com/
  [ハブの参照]: ./media/insights-debugging-with-events/Insights_Browse.png
  [リソース グループ]: ./media/insights-debugging-with-events/Insights_SelectRG.png
  [1]: ./media/insights-debugging-with-events/Insights_RGBlade.png
  [2]: ./media/insights-debugging-with-events/Insights_AllEvents.png
  [3]: ./media/insights-debugging-with-events/Insights_EventDetails.png
