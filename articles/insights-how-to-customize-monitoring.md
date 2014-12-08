<properties title="How to customize monitoring" pageTitle="監視のカスタマイズ方法" description="Learn how to customize monitoring charts in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-04" ms.author="awills" />

# 監視のカスタマイズ

Azure アプリケーションでは、さまざまなメトリックを監視し、選択した期間にわたってそれらのグラフを表示することができます。

1. [Azure プレビュー ポータル](https://portal.azure.com/)で、**[参照]** をクリックし、監視するリソースをクリックします。
2. **[監視]** レンズには、各 Azure リソースに最も重要なメトリックが含まれています。たとえば、Web サイトには要求、エラー、[Web テスト](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409)、および[分析](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409)があります。**[今日の要求とエラー]** パーツをクリックすると、**[メトリック]** ブレードが表示されます。  
    ![Monitoring lens](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. **[メトリック]** ブレードでは、選択したメトリックに関する詳細が表示されます。ブレードの上部にはグラフがあり、下部にある表では、平均、最小値、最大値など、これらのメトリックに関する集約が表示されます。その下には、既に定義したアラートの一覧があり、ブレード上に表示されているメトリックに関連するものだけが表示されるようにフィルターが適用されています。多数のアラートが発生した場合でも、この方法により、この状況に関連するアラートのみが表示されます。**[Web サイト]** ブレードの **[アラート ルール]** パーツをクリックする方法で、Web サイトに対応するすべてのアラートを表示することもできます。  
    ![Metric blade](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. 表示されるメトリックをカスタマイズするには、グラフを右クリックし、**[クエリの編集]** をクリックします。  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)
5. [クエリの編集] ブレードで、期間の変更および他のメトリックの選択という 2 つの操作を実行できます。  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. 期間の変更は簡単であり、別の範囲 (**[過去 1 時間]** など) を選択して、ブレードの下部にある **[保存]** をクリックするだけです。プレビュー ポータルの新機能である **[カスタム]** を選択することができます。  
    ![Custom time range](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. カスタムを使用すると、過去 2 週間のうち任意の期間を選択できます。たとえば、2 週間全体、または昨日のうち特定の 1 時間のみを表示することができます。別の期間を指定するには、テキスト ボックスに入力します。
8. 期間の下で、グラフに表示するメトリックの数として、任意の数値を選択することができます。新しいメトリックの **[Memory working set]** および **[Average memory working set]** なども表示することもできます。

9. [保存] をクリックすると、[Web サイト] ブレードを閉じるまでは変更内容が維持されます。後でもう一度このブレードを表示すると、変更前のメトリックと期間が表示されます。

## 新しいリソースの監視

Azure プレビュー ポータルの新機能として、次のようなさまざまな新しいリソースに関するパフォーマンス メトリックを監視する機能もあります。
- Web ホスティング プラン
- Redis Cache
- DocumentDB アカウント

Web ホスティング プランは他のリソースより少し複雑です。それは、**Web サイト**が実行されているインスタンスのパフォーマンスを表しているからです。Web ホスティング プランのメトリックにアクセスするには、Web サイトの [概要] レンズ内にある Web ホスティング プランのアイコンをクリックします。

![Web hosting plan](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

**[監視]** レンズ内でグラフを表示できます。次の新しいメトリックを表示できる点を除き、[Web サイト] ブレード内のグラフと同じように動作します。

- CPU の割合
- メモリの割合
- HTTP キューの深さ
- ディスク キューの深さ

## サイド バイ サイドのグラフの作成

Azure プレビュー ポータルの強力なユーザー カスタマイズ機能を使用して、サイド バイ サイドのグラフを作成し、カスタマイズすることができます。

1. 最初に、出発点として使用するグラフを右クリックし、**[カスタマイズ]**   をクリックします。
    ![Customize chart](./media/insights-how-to-customize-monitoring/Insights_Customize.png)
2. 次に、**[...]** メニューの **[複製]** をクリックし、このパーツ、つまりグラフをクリップボードにコピーします。  
    ![Clone part](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)
3. 最後に、画面の上部にあるツール バーで **[完了]** をクリックします。これで、通常のメトリック パーツと同様に、このパーツを扱うことができます。表示されたメトリックを右クリックして変更を加えると、2 つの異なるメトリックをサイド バイ サイドで同時に表示することができます。  
    ![Two metrics Side by Side](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

ポータルを閉じると、グラフの期間と選択したメトリックはリセットされることに注意してください。

