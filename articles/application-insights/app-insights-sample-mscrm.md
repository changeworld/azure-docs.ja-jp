<properties 
	pageTitle="チュートリアル: Application Insights を使用して Microsoft Dynamics CRM を監視する" 
	description="Application Insights を使用して Microsoft Dynamics CRM Online からテレメトリを取得します。設定、データの取得、視覚化、およびエクスポートをしてみましょう。" 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="awills"/>
 
# チュートリアル: Application Insights を使用して Microsoft Dynamics CRM Online のテレメトリを有効にする

この記事では、[Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/) を使用して [Microsoft Dynamics CRM Online](https://www.dynamics.com/) からテレメトリ データを取得する方法について説明します。アプリケーションに Application Insights のスクリプトを追加し、データをキャプチャし、データを視覚化するすべてのプロセスを見てみましょう。

>[AZURE.NOTE] [Browse the sample solution](https://dynamicsandappinsights.codeplex.com/)。

## 新規または既存の CRM Online インスタンスに Application Insights を追加する 

アプリケーションを監視するには、アプリケーションに Application Insights SDK を追加します。この SDK はテレメトリを [Application Insights ポータル](https://portal.azure.com)に送信します。そのポータルでは、強力な分析ツールおよび診断ツールを使用したり、データをストレージにエクスポートしたりできます。

### Azure で Application Insights のリソースを作成する

1. [Microsoft Azure のアカウント](http://azure.com/pricing)を取得します。 
2. [Azure ポータル](https://portal.azure.com)にサインインし、Application Insights の新しいリソースを追加します。これは、データを処理し表示する場所になります。

    ![[+]、[開発者向けサービス]、[Application Insights] をクリックします。](./media/app-insights-sample-mscrm/01.png)

    アプリケーションの種類として ASP.NET を選択します。

3. [クイック スタート] タブを開き、コードのスクリプトを開きます。

    ![](./media/app-insights-sample-mscrm/03.png)

**このコードのページは開いたまま**、別のブラウザー ウィンドウで次の手順を行います。このコードはすぐに必要になります。

### Microsoft CRM で JavaScript の Web リソースを作成する

1. CRM Online インスタンスを開き、管理者権限でログインします。
2. Microsoft Dynamics CRM の [設定]、[カスタマイズ]、[システム] を開きます。

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. JavaScript リソースを作成します。

    ![](./media/app-insights-sample-mscrm/07.png)

    名前を付け、[**スクリプト (JScript)**] を選択してテキスト エディターを開きます。

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Application Insights からコードをコピーします。

    ![](./media/app-insights-sample-mscrm/09.png)

    コードにはアプリケーション リソースを識別するインストルメンテーション キーが含まれています。

5. 保存して発行します。

    ![](./media/app-insights-sample-mscrm/10.png)

### フォームをインストルメント化する

1. Microsoft CRM Online で Account フォームを開きます。

    ![](./media/app-insights-sample-mscrm/11.png)

2. フォームのプロパティを開きます。

    ![](./media/app-insights-sample-mscrm/12.png)

3. 作成した JavaScript の Web リソースを追加します。

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. 保存し、フォームのカスタマイズ内容を発行します。


## キャプチャされるメトリック

フォームのテレメトリ キャプチャを設定しました。使用するたびに、Application Insights のリソースにデータが送信されます。

表示されるデータのサンプルを次に示します。

#### アプリケーションの健全性

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

ブラウザーの例外:

![](./media/app-insights-sample-mscrm/17.png)

詳細情報を表示するグラフをクリックします。

![](./media/app-insights-sample-mscrm/18.png)

#### 使用法

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### ブラウザー

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### 地理的位置情報

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### 内部ページ表示要求

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## コード サンプル

[サンプル コードを参照します](https://dynamicsandappinsights.codeplex.com/)。

## Power BI

[データを Microsoft Power BI にエクスポート](app-insights-export-power-bi.md)すると、より深い分析を行うことができます。

## Dynamics CRM ソリューションのサンプル

[Dynamics CRM で実装されたサンプルのソリューションを示します](https://dynamicsandappinsights.codeplex.com/)

## 詳細情報

* [Application Insights とは何か?](app-insights-overview.md)
* [Web ページ向けの Application Insights](app-insights-javascript.md)
* [その他のサンプルとチュートリアル](app-insights-code-samples.md)

 

<!---HONumber=August15_HO7-->