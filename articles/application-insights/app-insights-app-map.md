<properties 
	pageTitle="Application Insights のアプリケーション マップ | Microsoft Azure" 
	description="アプリケーション コンポーネント間の依存関係を、KPI やアラートと共に視覚的に表します。" 
	services="application-insights" 
    documentationCenter=""
	authors="SoubhagyaDash" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2016" 
	ms.author="awills"/>
 
# Application Insights のアプリケーション マップ

[Visual Studio Application Insights](app-insights-overview.md) のアプリケーション マップは、アプリケーション コンポーネントにおける依存関係のレイアウトを視覚的に表したものです。負荷やパフォーマンス、エラー、アラートといった KPI がコンポーネントごとに表示されるので、パフォーマンスの問題やエラーの原因となっているコンポーネントを容易に検出することができます。Application Insights から、またはアプリで Azure サービスを使用している場合は Azure 診断 (SQL Database アドバイザーのアドバイス情報など) から任意のコンポーネントをクリックして、さらに詳しい診断結果にアクセスすることができます。

アプリケーション マップは他のグラフと同様、Azure ダッシュボードにピン留めし、すべての機能を利用することができます。

## アプリケーション マップを開く

アプリケーション マップは、対象アプリケーションの概要ブレードから開きます。

![open app map](./media/app-insights-app-map/01.png)

![app map](./media/app-insights-app-map/02.png)

マップには次の情報が表示されます。

* 可用性テスト
* クライアント側コンポーネント (JavaScript SDK で監視)
* サーバー側コンポーネント
* クライアント コンポーネントとサーバー コンポーネントの依存関係

依存関係リンク グループは、展開したり折りたたんだりすることができます。

![collapse](./media/app-insights-app-map/03.png)
 
特定の種類 (SQL、HTTP など) の依存関係が多数存在する場合、それらがグループ化されて表示されます。


![grouped dependencies](./media/app-insights-app-map/03-2.png)
 
 
## 問題の特定

それぞれのノードには、関連するパフォーマンス指標 (対応するコンポーネントの負荷、パフォーマンス、エラー率など) が表示されます。

問題のリスクは、警告アイコンによって強調表示されます。オレンジ色の警告は、要求、ページ ビュー、依存関係の呼び出しにおけるエラーの存在を意味します。赤色は、エラー率が 5% を超えていることを示します。


![failure icons](./media/app-insights-app-map/04.png)

 
アクティブな警告も表示されます。


![active alerts](./media/app-insights-app-map/05.png)
 
SQL Azure を使用している場合、パフォーマンスを高める方法についての推奨事項が存在するときにアイコンが表示されます。


![Azure recommendation](./media/app-insights-app-map/06.png)

アイコンをクリックすると、詳しい情報にアクセスできます。


![azure recommendation](./media/app-insights-app-map/07.png)
 
 
## 診断のリンク

マップ上の各ノードには、診断を目的としたリンクが表示されます。表示されるオプションは、ノードの種類によって異なります。

![server options](./media/app-insights-app-map/09.png)

 
Azure でホストされるコンポーネントの場合、そのコンポーネントに直接アクセスするためのリンクがオプションに含まれます。


## フィルターと時間範囲

既定では、選択した時間範囲に関して入手できるすべてのデータがマップに集約されます。ただしフィルターを適用することで、表示内容を特定の操作名や依存関係に限定することができます。

* 操作名: ページ ビューのほか、サーバー側の要求の種類が表示対象となります。このオプションでは、選択した操作に限定して、サーバー/クライアント側ノードの KPI がマップに表示されます。その特定の操作のコンテキストで呼び出された依存関係が表示されます。
* 依存関係のベース名: AJAX ブラウザー側の依存関係とサーバー側の依存関係が表示対象となります。TrackDependency API を使ってカスタムの依存関係のテレメトリを報告対象にしている場合、それらもここに表示されます。マップに表示する依存関係は選択できます。現時点では、サーバー側の要求やクライアント側のページ ビューはフィルタリングされません。


![Set filters](./media/app-insights-app-map/11.png)

 
 
## フィルターの保存

適用したフィルターを保存するには、フィルタリングされたビューを[ダッシュボード](app-insights-dashboards.md)にピン留めします。


![ダッシュボードにピン留めする](./media/app-insights-app-map/12.png)
 


## フィードバック

[ご意見やご感想は、ポータルのフィードバック オプションからお寄せください](app-insights-get-dev-support.md)。


![MapLink-1 image](./media/app-insights-app-map/13.png)

<!---HONumber=AcomDC_0622_2016-->