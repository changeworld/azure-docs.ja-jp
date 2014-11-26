<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic" />

# 監視のカスタマイズ

Azure プレビュー ポータルで、以前より多くの方法で監視データを表示できるようになりました。その中には、期間のカスタマイズや、より多くのメトリックを選択することが含まれます。

1.  [Azure プレビュー ポータル][Azure プレビュー ポータル]で、**[参照]** をクリックし、監視するリソースをクリックします。
2.  **[監視]** レンズには、各 Azure リソースに最も重要なメトリックが含まれています。たとえば、Web サイトには要求、エラー、[Web テスト][Web テスト]、および[分析][分析]があります。**[今日の要求とエラー]** パーツをクリックすると、**[メトリック]** ブレードが表示されます。<br />
    ![監視レンズ][監視レンズ]
3.  **[メトリック]** ブレードでは、選択したメトリックに関する詳細が表示されます。ブレードの上部にはグラフがあり、下部にある表では、平均、最小値、最大値など、これらのメトリックに関する集約が表示されます。その下には、既に定義したアラートの一覧があり、ブレード上に表示されているメトリックに関連するものだけが表示されるようにフィルターが適用されています。多数のアラートが発生した場合でも、この方法により、この状況に関連するアラートのみが表示されます。**[Web サイト]** ブレードの **[アラート ルール]** パーツをクリックして、Web サイトに対応するすべてのアラートを表示することもできます。<br />
    ![[メトリック] ブレード](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4.  表示されるメトリックをカスタマイズするには、グラフを右クリックし、**[クエリの編集]** をクリックします。<br />
    ![クエリの編集][クエリの編集]
5.  [クエリの編集] ブレードで、期間の変更および他のメトリックの選択という 2 つの操作を実行できます。<br />
    ![クエリの編集][1]
6.  期間の変更は簡単で、別の範囲 (**[過去 1 時間]** など) を選択して、ブレードの下部にある **[保存]** をクリックするだけです。ポータルのプレビューの新機能である **[カスタム]** を選択することができます。<br />
    ![カスタム期間][カスタム期間]
7.  カスタムを使用すると、過去 2 週間のうち任意の期間を選択できます。たとえば、2 週間全体、または昨日のうち特定の 1 時間のみを表示することができます。別の期間を指定するには、テキスト ボックスに入力します。
8.  期間の下で、グラフに表示するメトリックを任意の数だけ選択できます。新しいメトリックの **[Memory working set]** および **[Average memory working set]** なども表示することもできます。

9.  [保存] をクリックすると、[Web サイト] ブレードを閉じるまでは変更内容が維持されます。後でもう一度このブレードを表示するときは、初期のメトリックと期間が表示されます。

## 新しいリソースの監視

Azure プレビュー ポータルの新機能として、次のようなさまざまな新しいリソースに関するパフォーマンス メトリックを監視する機能もあります。

-   Web ホスティング プラン
-   Redis Cache
-   DocumentDB アカウント

Web ホスティング プランは他のリソースより少し複雑です。それは、**Web サイト**が実行されているインスタンスのパフォーマンスを表しているからです。Web ホスティング プランのメトリックにアクセスするには、Web サイトの [概要] レンズ内にある Web ホスティング プランのアイコンをクリックします。

![Web ホスティング プラン][Web ホスティング プラン]

**[監視]** レンズ内でグラフを表示できます。次の新しいメトリックを表示できる点を除き、[Web サイト] ブレード内のグラフと同じように動作します。

-   CPU の割合
-   メモリの割合
-   HTTP キューの深さ
-   ディスク キューの深さ

## サイド バイ サイドのグラフの作成

Azure プレビュー ポータルの強力なユーザー カスタマイズ機能を使用して、サイド バイ サイドのグラフを作成し、カスタマイズすることができます。

1.  最初に、出発点として使用するグラフを右クリックし、**[カスタマイズ]** をクリックします。<br />
    ![グラフのカスタマイズ][グラフのカスタマイズ]
2.  次に、**[...]** メニューの **[複製]** をクリックし、このパーツ、つまりグラフをクリップボードにコピーします。<br />
    ![パーツの複製][パーツの複製]
3.  最後に、画面の上部にあるツール バーで **[完了]** をクリックします。これで、通常のメトリック パーツと同様に、このパーツを扱うことができます。表示されたメトリックを右クリックして変更を加えると、2 つの異なるメトリックをサイド バイ サイドで同時に表示することができます。<br />
    ![2 つのメトリックをサイド バイ サイドで表示][2 つのメトリックをサイド バイ サイドで表示]

ポータルを閉じると、グラフの期間と選択したメトリックはリセットされることに注意してください。

  [Azure プレビュー ポータル]: https://portal.azure.com/
  [Web テスト]: http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409
  [分析]: http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409
  [監視レンズ]: ./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png

  [クエリの編集]: ./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png
  [1]: ./media/insights-how-to-customize-monitoring/Insights_EditQuery.png
  [カスタム期間]: ./media/insights-how-to-customize-monitoring/Insights_CustomTime.png
  [Web ホスティング プラン]: ./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png
  [グラフのカスタマイズ]: ./media/insights-how-to-customize-monitoring/Insights_Customize.png
  [パーツの複製]: ./media/insights-how-to-customize-monitoring/Insights_ClonePart.png
  [2 つのメトリックをサイド バイ サイドで表示]: ./media/insights-how-to-customize-monitoring/Insights_SideBySide.png
