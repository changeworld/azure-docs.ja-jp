<properties 
	pageTitle="Application Insights 開発チームによるテクニカル サポートを利用する方法 | Microsoft Azure" 
	description="Application Insights 開発チームからの特別なサポートが必要な問題が発生した場合に、詳細を送信してサポートを受ける方法を紹介します。" 
	services="application-insights" 
    documentationCenter=""
	authors="alexbulankou" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="albulank"/>
	
# Application Insights 開発チームによるテクニカル サポートを利用する方法
	
[Visual Studio Application Insights](app-insights-overview.md) で技術的な問題が発生した場合、サポートを利用するためのオプションは次のとおりです。

## 1\.ドキュメントを確認する

* データが見つからない場合は、 [サンプリング](app-insights-sampling.md)、[クォータ、スロットル](app-insights-pricing.md)を確認してください。
* トラブルシューティングの場合は、次のリンク先を確認してください: [ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## 2\.フォーラムを検索する

* [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## 3\.Azure サポート プランを確認する

開発者に自身の具体的な事例を調査してほしい場合もあります。

[Microsoft Azure でサポート プラン](https://azure.microsoft.com/support/plans/)に加入している場合は、[サポート チケットを開く](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ことができます。

## 4\.Application Insights チームに問い合わせる

サポート プランに加入していない場合でも、Microsoft の開発チームは、一般公開のマイルストーンに向けて準備する際に、Application Insights のお客様に対するサポート提供に最善を尽くします。Microsoft では**新しいサポート オプション**を導入しました。これにより、Azure ポータルでフィードバック フォームを送信することで問題を報告すると、Application Insights チームの開発者から問題解決のために連絡をもらうことができるようになりました。


1. [Application Insights ポータル](https://portal.azure.com)で、右上隅にあるスマイル アイコンをクリックします。  

    ![フィードバック ボタン](./media/app-insights-get-dev-support/01.png)

2. コメント ボックスで、最初の行に **AppInsights** を指定した後、次の情報を含めるようにしてください。

    ```

    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>

    ```   

    ![フィードバックのダイアログ](./media/app-insights-get-dev-support/02.png)

3. [はい、このフィードバックに関してメールをお送りいただいてもかまいません] チェック ボックスをオンにします。

    ![送信セクション](./media/app-insights-get-dev-support/03.png)

Application Insights チームのエンジニアからすぐに連絡が来ます。Microsoft では、このサービスの提供に最善を尽くしている最中のため、現時点では正式な SLA を提供できません。

<!---HONumber=AcomDC_0615_2016-->