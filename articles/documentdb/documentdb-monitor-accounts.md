<properties 
	pageTitle="DocumentDB アカウントの監視 | Azure" 
	description="パフォーマンス メトリック (要求やサーバー エラーなど) と使用状況メトリック (ストレージ消費など) を利用して、DocumentDB アカウントを監視する方法について説明します。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="mimig"/>

#DocumentDB アカウントを監視する  

DocumentDB アカウントは、[Azure プレビュー ポータル](https://portal.azure.com/)で監視できます。各 DocumentDB アカウントに対して、パフォーマンス メトリック (要求やサーバー エラーなど) と使用状況メトリック (ストレージ消費など) が利用可能です。

## <a id="metrics"></a>  方法:DocumentDB アカウントのパフォーマンス メトリックを表示する
1.	[Azure プレビュー ポータル](https://portal.azure.com/)で、**[参照]**、**[DocumentDB アカウント]** の順にクリックし、パフォーマンス メトリックを表示する DocumentDB アカウントの名前をクリックします。
2.	**[監視]** レンズ内では、既定により以下を表示できます。
	*	当日の要求数合計
	*	当日の 1 秒あたりの要求数平均 
	
	![](./media/documentdb-monitor-accounts/madocdb1.png)


3.	**[要求数合計]** または **[1 秒あたりの要求数平均]** パーツをクリックすると、詳細な **[メトリック]** ブレードが表示されます。
4.	**[メトリック]** ブレードには、選択したメトリックの詳細が表示されます。ブレードの上部にはグラフがあり、その下の表には、平均、最小値、最大値など、選択したメトリックの集計値が表示されます。[メトリック] ブレードには、現在のメトリック ブレードに表示されるメトリックに対してフィルター処理された定義済みアラートの一覧も表示されます (これにより、さまざまなアラートがある場合、ここで示された関連するアラートのみが表示されます)。   

	![](./media/documentdb-monitor-accounts/madocdb2.png)


## <a id="custom"></a>DocumentDB アカウントのパフォーマンス メトリック ビューをカスタマイズする

1.	特定のパーツに表示されるメトリックをカスタマイズするには、メトリック チャートを右クリックし、**[グラフの編集]** を選択します。  
	![](./media/documentdb-monitor-accounts/madocdb3.png)

2.	**[グラフの編集]** ブレードには、パーツに表示されるメトリックおよび期間を変更するオプションがあります。  
	![](./media/documentdb-monitor-accounts/madocdb4.png)

3.	パーツに表示されるメトリックを変更するには、利用可能なパフォーマンス メトリックを選択または選択解除して、ブレードの下部にある **[保存]** をクリックするだけです。  
4.	期間を変更するには、異なる範囲を選択し (たとえば、**[過去 1 時間]**)、ブレードの下部にある **[保存]** をクリックします。  

	![](./media/documentdb-monitor-accounts/madocdb5.png) 


## <a id="create"></a>サイド バイ サイドのパフォーマンス メトリック チャートを作成する
Azure プレビュー ポータルでは、サイド バイ サイドのメトリック チャートを作成できます。  

1.	まず、複製と変更を行うグラフを右クリックし、**[カスタマイズ]** をクリックします。 

	![](./media/documentdb-monitor-accounts/madocdb6.png)

2.	メニューの **[複製]** をクリックしてパーツをコピーし、**[カスタマイズの完了]** をクリックします。 

	![](./media/documentdb-monitor-accounts/madocdb7.png)  


これで、このパーツを他のメトリック パーツとして扱って、パーツに表示されるメトリックと期間をカスタマイズすることができます。これにより、2 種類のメトリック チャートを画面に並べて同時に見ることができます。  
	![](./media/documentdb-monitor-accounts/madocdb8.png)  

## <a id="view"></a>DocumentDB アカウントの使用状況メトリックを表示する
1.	[Azure プレビュー ポータル](https://portal.azure.com/)で、**[参照]**、**[DocumentDB アカウント]** の順にクリックし、使用状況メトリックを表示する DocumentDB アカウントの名前をクリックします。
2.	**[使用状況]** レンズ内では、既定により以下を表示できます。
	*	推定コスト。
	*	アカウント内で消費されているストレージ。
	*	アカウントの利用可能なストレージの最大値。
	*	ユーザーとアクセス許可の使用状況。
	*	容量単位 (CU) の割り当て。
	*	添付ファイルの使用状況。

	![](./media/documentdb-monitor-accounts/madocdb9.png)
 
## <a id="setup"></a>DocumentDB アカウントのパフォーマンス メトリック アラートを設定する
1.	[Azure プレビュー ポータル](https://portal.azure.com/)で、**[参照]**、**[DocumentDB アカウント]** の順にクリックし、パフォーマンス メトリック アラートを設定する DocumentDB アカウントの名前をクリックします。
2.	**[操作]** レンズ内で、**[アラート ルール]** パーツをクリックします。  
	![](./media/documentdb-monitor-accounts/madocdb10.png)

3.	[アラート ルール] ブレードで、**[アラートの追加]** をクリックします。  
	![](./media/documentdb-monitor-accounts/madocdb11.png)

4.	**[アラート ルールの追加]** ブレードで、以下を指定します。
	*	設定するアラート ルールの名前。
	*	新しいアラート ルールの説明。
	*	アラート ルールのメトリック。
	*	いつアラートをアクティブにするかを決定する条件、しきい値、および期間。たとえば、過去 15 分間にサーバー エラー回数が 5 を超える場合を指定できます。
	*	サービス管理者および共同管理者は、アラートが生成されると電子メールを受け取ります。
	*	アラート通知用の追加の電子メール アドレス。  
	![](./media/documentdb-monitor-accounts/madocdb12.png)

 
## <a id="next"></a>次のステップ
DocumentDB の容量とパフォーマンスの詳細については、「[DocumentDB の容量とパフォーマンスの管理](documentdb-manage.md)」を参照してください。 

<!--Anchors-->
[DocumentDB アカウントのパフォーマンス メトリックを表示する方法]: #How-to-view-performance-metrics-for-a-DocumentDB-account
[DocumentDB アカウントのパフォーマンス メトリック ビューをカスタマイズする]: #Customize-performance-metric-views-for-a-DocumentDB-account
[サイド バイ サイドのパフォーマンス メトリック チャートを作成する方法]: #How-to-create-side-by-side-performance-metric-charts
[DocumentDB アカウントの使用状況メトリックを表示する方法]: #How-to-view-usage-metrics-for-a-DocumentDB-account
[DocumentDB アカウントのパフォーマンス メトリック アラートを設定する方法]: #How-to-setup-performance-metric-alerts-for-a-DocumentDB-account
[次のステップ]: #Next-steps

<!--HONumber=49--> 