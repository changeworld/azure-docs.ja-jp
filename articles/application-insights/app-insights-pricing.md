<properties 
	pageTitle="Application Insights の価格とクォータの管理" 
	description="必要な価格プランを選択します" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="awills"/>

# Application Insights の価格とクォータの管理

*Application Insights はプレビュー段階です。*

[Visual Studio Application Insights][start] の[価格][pricing]は、アプリケーションごとのデータ量に基づきます。真の意味で無料のレベルがあり、いくつかの制限はありますが、ほとんどの機能を利用できます。

各 Application Insights リソースは個々のサービスとして課金され、Azure のサブスクリプションの課金内容に加えられます。

[「料金プラン」を参照してください][pricing]。

## Application Insights リソースのクォータと価格プランの確認

アプリケーション リソースの設定からクォータと価格のブレードを開くことができます。

![設定の選択、クォータと価格](./media/app-insights-pricing/01-pricing.png)

## クォータのしくみ

* 毎月、アプリケーションは、指定された量のテレメトリを Application Insights に送ることができます。実際の数値については、「[料金プラン][pricing]」を参照してください。 
* クォータは、選択した価格レベルによって異なります。
* クォータは、毎月 1 日の午前 0 時 UTC からカウントされます。
* データ ポイントのグラフは、今月クォータのどのくらいが使用されたかを示しています。
* クォータは、*データ ポイント*単位で測定されます。 単一のデータ ポイントは、追跡メソッドの 1 つの呼び出しで、コードで明示的に呼び出されるか、標準テレメトリ モジュールのいずれかによって呼び出されます。診断の検索で確認される各行は、データ ポイントです。メトリックの各測定値 (パフォーマンス カウンターなど) は、データ ポイントです。 
* *セッション データ*は、クォータにカウントされません。これには、ユーザー、セッション、環境、およびデバイスのデータの数が含まれます。


## 超過料金

アプリケーションの送信量が月間クォータを超える場合、次の操作を実行できます。

* 追加データ分を支払います。詳細については、「[料金プラン][pricing]」を参照してください。このオプションは、事前に選択できます。このオプションは、無料の価格レベルでは選択できません。
* 価格レベルをアップグレードします。
* 何もしません。セッション データは引き続き記録されますが、それ以外のデータは、診断の検索またはメトリックのエクスプ ローラーに表示されません。

## 無料の Premium 評価版

最初に新しい Application Insights リソースを作成するときは、無料レベルから開始します。

いつでも 30 日間無料の Premium 評価版に切り替えることができます。これにより、Premium レベルを体験することができます。30 日が経過すると、明示的に別のレベルを選択していなければ、以前のレベルに自動的に戻ります。試用期間中はいつでもご希望のレベルを選択できますが、30 日の期間が終了するまでは無料試用版を取得できます。


## クォータの用途

価格ブレードの下部にあるグラフは、アプリケーションのデータ ポイントの使用状況を示しています。これは、 データ ポイントの種類でグループ化されます。

![価格ブレードの下部](./media/app-insights-pricing/03-allocation.png)

グラフをクリックすると、詳細が表示されます。時間範囲の詳細を入手するには、グラフをドラッグします。

## Azure へのサブスクリプションの課金内容の確認

Application Insights の課金は Azure の課金内容に加えられます。Azure ポータルの [課金] または [Azure Billing Portal](https://account.windowsazure.com/Subscriptions) で、Azure の課金内容の詳細を確認できます。

![サイド メニューで、[課金] を選択します。](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=62-->