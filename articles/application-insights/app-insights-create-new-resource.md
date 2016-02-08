<properties 
	pageTitle="新しい Application Insights リソースを作成します。" 
	description="新しいライブ アプリケーションを対象にした Application Insights 監視をセットアップします。Web ベースのアプローチです。" 
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
	ms.date="11/06/2015" 
	ms.author="awills"/>

# 新しい Application Insights リソースを作成します。



[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights には、Microsoft Azure *リソース*のアプリケーションに関するデータが表示されます。したがって、新しいリソースの作成は、[新しいアプリケーションを監視するための Application Insights の設定の一部です][start]。多くの場合、これは IDE によって自動的に行うことができ、可能な場合には常にそうするよう勧められています。しかし、リソースを手動で作成する場合もあります。

リソースを作成した後、インストルメンテーション キーを取得し、それを使用してアプリケーション内の SDK を構成します。これは、リソースにテレメトリを送信します。

## Microsoft Azure へのサインアップ

取得していない場合、[Microsoft アカウントをここで取得してください](http://live.com)。(Outlook.com、OneDrive、Windows Phone、XBox Live などのサービスを利用している場合、Microsoft アカウントを持っています。)

使用を開始するには、[Microsoft Azure](http://azure.com) のサブスクリプションも必要です。チームまたは組織で Azure サブスクリプションを取得している場合、所有者は Windows Live ID を使用してあなたを追加できます。

または、新しいサブスクリプションを作成することもできます。無料試用版では、Azure 内のすべてを試すことができます。評価期間が過ぎた後、無料サービスでは課金されないため、従量制サブスクリプションが適切であると感じるかもしれません。

サブスクリプションへのアクセスを取得したら、Application Insights ([http://portal.azure.com](https://portal.azure.com)) にログインし、Live ID を使用してログインしてください。


## Application Insights リソースの作成
  

[portal.azure.com](https://portal.azure.com) で、Application Insights リソースを追加します。

![[新規]、[Application Insights] の順にクリックする](./media/app-insights-create-new-resource/01-new.png)


* **アプリケーションの種類**に応じて、概要ブレードに表示されるものと[メトリック エクスプローラー][metrics]で使用できるプロパティが決まります。自分のアプリの種類が表示されない場合、Web ページの Web の種類のいずれかと、その他のデバイスの電話の種類のいずれかを選択します。
* **リソース グループ**は、アクセス制御などのプロパティを管理するのに便利です。その他の Azure リソースが既に作成されている場合、新しいリソースを同じグループに入れることもできます。
* **サブスクリプション**は、Azure での支払いアカウントです。
* **場所**は、データの保存場所です。現在、これは変更できません。
* **スタート画面への追加**は、Azure ホーム ページ上のリソースに対するクイック アクセス タイルを入れます。推奨。

アプリが作成されると、新しいブレードが開きます。そのブレードには、アプリに関するパフォーマンスと使用状況データが表示されます。

次回 Azure にログインするときにそこへ戻るには、スタート画面 (ホーム画面) 上のアプリのクイック スタート タイルを探してください。あるいは、[参照] ボタンをクリックして探します。


## インストルメンテーション キーのコピー

インストルメンテーション キーは作成したリソースを識別します。これは、SDK に渡すために必要です。

![Essentials、インストルメンテーション キーの順にクリックし、Ctrl キーを押しながら C キーを押します。](./media/app-insights-create-new-resource/02-props.png)

## アプリケーションでの SDK のインストール

アプリで Application Insights SDK をインストールします。この手順は、アプリケーションの種類に大きく依存します。

インストルメンテーション キーを使用して、[アプリケーションにインストールする SDK][start] を構成します。

SDK には、コードを記述せずにテレメトリを送信する標準的なモジュールが含まれています。ユーザーの操作を追跡したり、問題をより詳しく診断したりするには、[API を使用][api]して、独自のテレメトリを送信します。


## <a name="monitor"></a>テレメトリ データを参照

クイック スタート ブレードを閉じ、Azure ポータルのアプリケーション ブレードに戻ります。

[検索] タイルをクリックして [[診断検索]][diagnostic] を確認します。ここには、最初のイベントが表示されます。

大量のデータが予想される場合は、数秒後に [最新の情報に更新] をクリックします。

## リソースの自動作成

[PowerShell スクリプト](app-insights-powershell-script-create-resource.md)を作成して、リソースを自動で作成できます。




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0128_2016-->