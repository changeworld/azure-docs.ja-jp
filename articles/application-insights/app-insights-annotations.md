<properties
    pageTitle="Application Insights のデプロイの注釈 | Microsoft Azure"
    description="Application Insights で、メトリックス エクスプローラーのグラフにデプロイ マーカーまたはビルド マーカーを追加します。"
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="01/05/2016"
    ms.author="awills"/>

# Application Insights のリリース注釈

[メトリックス エクスプローラー](app-insights-metrics-explorer.md)のグラフのリリース注釈により、新しいビルドのデプロイ先が示されます。これにより、変更内容がアプリケーションのパフォーマンスに影響を与えたかどうかを簡単に把握できます。リリース注釈は、[Visual Studio Team Services ビルド システム](https://www.visualstudio.com/ja-JP/get-started/build/build-your-app-vs)により自動で作成されます。

![サーバー応答時間と明確な相関関係のある注釈の例](./media/app-insights-annotations/00.png)

リリース注釈は、Visual Studio Team Services のクラウド ベースのビルドとリリース サービスの機能です。

## 注釈拡張機能のインストール (1 回限り)

リリース注釈を作成できるようにするには、Visual Studio Marketplace で入手可能な Team Services 拡張機能のいずれかをインストールする必要があります。

1. [Visual Studio Team Services](https://www.visualstudio.com/ja-JP/get-started/setup/sign-up-for-visual-studio-online) プロジェクトにサインインします。
2. Visual Studio Marketplace を開き、Application Insights Annotations 拡張機能を検索して、Team Services アカウントに追加します。

![Team Service Web ページの右上で Marketplace を開くアカウントで Application Insights 注釈を検索してインストール](./media/app-insights-annotations/10.png)

この操作は、Visual Studio Team Services アカウントごとに 1 回行うだけですみます。これで、リリース注釈をアカウントのプロジェクトに対して構成できるようになりました。



## リリース テンプレートへの注釈タスクの追加

この作業は、リリース注釈を作成するリリース テンプレートごとに行う必要があります。

Visual Studio Team Services のデプロイを管理するリリース テンプレートを開きます (または作成します)。

タスクを追加し、メニューから [Application Insights Release Annotation] \(Application Insights リリース注釈) タスクを選択します。

![Team Service Web ページの右上で Marketplace を開くアカウントで Application Insights 注釈を検索してインストール](./media/app-insights-annotations/40.png)

この手順を完了するには、アプリケーションの監視に使用する Application Insights リソースの詳細が必要になります。

Application Insights で詳細を確認する間、Team Services のウィンドウは開いたままにします。

## Application Insights からの API キーのコピー

ブラウザーの別のウィンドウで、以下の操作を行います。

1. [Microsoft Azure ポータル](https://portal.azure.com)にサインインし、アプリケーションを監視する Application Insights リソースを開きます (まだリソースを作成していない場合は、[この時点で作成します](app-insights-overview.md))。
2. **[要点]** ドロップダウンを開き、リリース注釈タスクにリソースのサブスクリプション ID、リソース グループ、名前をコピーします。![](./media/app-insights-annotations/50.png)
2. **[設定]**、**[API キー]** の順に開いて、新しいキーを作成します。作成したキーをコピーします。![](./media/app-insights-annotations/30.png)

最後に、リリースの定義を**保存**します。

## デプロイ マーカー

これで、このリリース テンプレートを使用して新しいリリースをデプロイするたびに、注釈が Application Insights に送信されるようになりました。注釈は、メトリックス エクスプローラーのグラフに表示されます。

<!----HONumber=AcomDC_0107_2016-->