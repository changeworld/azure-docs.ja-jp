<properties
    pageTitle="Application Insights のリリース注釈 | Microsoft Azure"
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
	ms.date="06/28/2016"
    ms.author="awills"/>

# Application Insights のリリース注釈

[メトリックス エクスプローラー](app-insights-metrics-explorer.md)のグラフのリリース注釈により、新しいビルドのデプロイ先が示されます。これにより、変更内容がアプリケーションのパフォーマンスに影響を与えたかどうかを簡単に把握できます。リリース注釈は、[Visual Studio Team Services ビルド システム](https://www.visualstudio.com/ja-JP/get-started/build/build-your-app-vs)により自動で作成されます。[PowerShell から作成](#create-annotations-from-powershell)することもできます。

![サーバー応答時間と明確な相関関係のある注釈の例](./media/app-insights-annotations/00.png)

リリース注釈は、Visual Studio Team Services のクラウド ベースのビルドとリリース サービスの機能です。

## 注釈拡張機能のインストール (1 回限り)

リリース注釈を作成できるようにするには、Visual Studio Marketplace で入手可能な Team Services 拡張機能のいずれかをインストールする必要があります。

1. [Visual Studio Team Services](https://www.visualstudio.com/ja-JP/get-started/setup/sign-up-for-visual-studio-online) プロジェクトにサインインします。
2. Visual Studio Marketplace で[リリース注釈拡張機能を取得](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)して、Team Services アカウントに追加します。

![Team Service Web ページの右上で Marketplace を開くSelect Visual Team Services and then under Build and Release, choose See More.](./media/app-insights-annotations/10.png)

この操作は、Visual Studio Team Services アカウントごとに 1 回行うだけですみます。これで、リリース注釈をアカウントのプロジェクトに対して構成できるようになりました。

## Application Insights から API キーを取得する

この作業は、リリース注釈を作成するリリース テンプレートごとに行う必要があります。


1. [Microsoft Azure ポータル](https://portal.azure.com)にサインインし、アプリケーションを監視する Application Insights リソースを開きます (まだリソースを作成していない場合は、[この時点で作成します](app-insights-overview.md))。
2. **[API アクセス]** を開き、**Application Insights ID** をコピーします。

    ![portal.azure.com で、Application Insights リソースを開き、[設定] を選択します。[API アクセス] を開きます。アプリケーション ID をコピーする](./media/app-insights-annotations/20.png)

2. 別のブラウザー ウィンドウで、Visual Studio Team Services のデプロイを管理するリリース テンプレートを開きます (または作成します)。

    タスクを追加し、メニューから [Application Insights Release Annotation] \(Application Insights リリース注釈) タスクを選択します。

    [API アクセス] ブレードからコピーした**アプリケーション ID** を貼り付けます。

    ![Visual Studio Team Services で [リリース] を開き、リリース定義を選択して [編集] を選択します。[タスクの追加] をクリックし、[Application Insights Release Annotation] \(Application Insights リリース注釈) を選択します。Application Insights ID を貼り付けます。](./media/app-insights-annotations/30.png)

3. **[APIKey]** フィールドを `$(ApiKey)` 変数に設定します。

4. [API アクセス] ブレードに戻り、新しい API キーを作成し、それをコピーします。

    ![[Azure] ウィンドウの [API アクセス] ブレードで、[API キーの作成] をクリックします。コメントを指定し、[書き込み] 注釈にチェックを入れ、[キーの生成] をクリックします。新しいキーをコピーします。](./media/app-insights-annotations/40.png)

4. リリース テンプレートの [構成] タブを開きます。

    `ApiKey` の変数の定義を作成します。

    ApiKey の変数の定義に API キーを貼り付けます。

    ![[Team Services] ウィンドウで、[構成] タブを選択し、[変数の追加] をクリックします。ApiKey の名前を設定し、[値] に生成したキーを貼り付けます。](./media/app-insights-annotations/50.png)


5. 最後に、リリースの定義を**保存**します。

## PowerShell から注釈を作成する

(VS Team System を利用せずに) あらゆるプロセスから注釈を作成することもできます。

[GitHub から Powershell スクリプト](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)を入手します。

それを次のように利用します。

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Application Insights リソースから `applicationId` と `apiKey` を取得します。[設定]、[API アクセス] の順に開き、アプリケーション ID をコピーします。[API キーの作成] をクリックし、キーをコピーします。

## リリース注釈

これで、このリリース テンプレートを使用して新しいリリースをデプロイするたびに、注釈が Application Insights に送信されるようになりました。注釈は、メトリックス エクスプローラーのグラフに表示されます。

要求元、ソース管理の分岐、リリース定義、環境などを含む、リリースに関する詳細を表示するための注釈マーカーをクリックします。


![任意のリリース注釈マーカーをクリックします。](./media/app-insights-annotations/60.png)

<!---HONumber=AcomDC_0713_2016-->