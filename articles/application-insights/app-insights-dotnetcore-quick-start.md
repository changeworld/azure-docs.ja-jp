---
title: Azure Application Insights のクイック スタート | Microsoft docs
description: Application Insights で監視する ASP.NET Core Web アプリを迅速にセットアップする手順を説明します
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/11/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 008e61841611f36c440bb4896ae5a85d0bf4d874
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991623"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>ASP.NET Core Web アプリケーションの監視を開始する

Azure Application Insights を使うと、Web アプリケーションの可用性、パフォーマンス、利用状況を簡単に監視できます。 アプリケーションのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断することもできます。 

このクイック スタートでは、既存の ASP.Net Core Web アプリケーションに Application Insights SDK を追加する方法を説明します。 

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

- 次のワークロードを使って、[Visual Studio 2017 をインストール](https://www.visualstudio.com/downloads/)します。
  - ASP.NET および Web の開発
  - Azure の開発
- [.NET Core 2.0 SDK のインストール](https://www.microsoft.com/net/core)
- Azure サブスクリプションと既存の .NET Core Web アプリケーションが必要です。

ASP.NET Core Web アプリケーションがない場合は、ステップ バイ ステップ ガイドを使用して [ASP.NET Core アプリを作成し、Application Insights を追加](app-insights-asp-net-core.md)してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Application Insights は、オンプレミスとクラウドのどちらで実行されているかに関係なく、インターネットに接続された任意のアプリケーションからテレメトリ データを収集できます。 このデータの表示を開始するには、次の手順を実行します。

1. **[リソースの作成]** > **[監視 + 管理]** > **[Application Insights]** の順に選択します。

   ![Application Insights リソースの追加](./media/app-insights-dotnetcore-quick-start/0001-dc.png)

    構成ボックスが表示されたら、次の表を使用して入力フィールドに入力します。

    | 設定        |  値           | [説明]  |
   | ------------- |:-------------|:-----|
   | **名前**      | グローバルに一意の値 | 監視しているアプリを識別する名前 |
   | **アプリケーションの種類** | ASP.NET Web アプリケーション | 監視しているアプリの種類 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループの名前 |
   | **場所** | 米国東部 | 近くにある場所か、アプリがホストされている場所の近くを選択します。 |

2. **Create** をクリックしてください。

## <a name="configure-app-insights-sdk"></a>App Insights SDK を構成する

1. Visual Studio で ASP.NET Core Web アプリ **プロジェクト**を開き、**ソリューション エクスプローラー**で AppName を右クリックして **[追加]** > **[Application Insights Telemetry]** を選択します。

    ![Application Insights Telemetry の追加](./media/app-insights-dotnetcore-quick-start/0001.png)

2. **[無料で始める]** ボタンをクリックし、Azure Portal で作成した**既存のリソース**を選択して **[登録]** をクリックします。

3. **[デバッグ]** > **[Start without Debugging (デバッグなしで開始)]** (Ctrl+F5) を選択して、アプリを起動します。

> [!NOTE]
> データがポータルに表示されるようになるまで、3 ～ 5 分かかります。 このアプリがトラフィックの少ないテスト アプリである場合、ほとんどのメトリックはアクティブな要求や操作がある場合にのみキャプチャされることに留意してください。

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal で監視を開始する

1. Azure Portal で、**[プロジェクト]** > **[Application Insights]** > **[Application Insights ポータルを開く]** を選択して Application Insights の**概要**ページを再度開き、現在実行中のアプリケーションに関する詳細情報を表示できます。

   ![Application Insights の概要メニュー](./media/app-insights-dotnetcore-quick-start/overview-001.png)

2. **[アプリケーション マップ]** をクリックして、アプリケーション コンポーネント間の依存関係の視覚的レイアウトを取得します。 各コンポーネントには、負荷、パフォーマンス、障害、アラートなどの KPI が表示されます。

   ![アプリケーション マップ](./media/app-insights-dotnetcore-quick-start/application-map.png)

3. **[アプリ分析]** アイコンをクリックし、![[アプリケーション マップ]](./media/app-insights-dotnetcore-quick-start/006.png) アイコンをクリックします。  これにより、Application Insights で収集されたすべてのデータを分析するための多機能なクエリ言語を利用可能な **Application Insights Analytics** が開きます。 この場合は、要求の数をグラフとして描画するクエリが生成されます。 自分でクエリを作成して他のデータを分析することができます。

   ![一定の期間にわたるユーザー要求に関する Analytics グラフ](./media/app-insights-dotnetcore-quick-start/0007-dc.png)

4. **[概要]** ページに戻って KPI ダッシュボードを観察します。  このダッシュ ボードでは、着信要求の数、要求の期間、発生したエラーなど、アプリケーションの正常性に関する統計情報が提供されます。 

   ![正常性の概要のタイムライン グラフ](./media/app-insights-dotnetcore-quick-start/overview-graphs.png)

   **[ページ ビューの読み込み時間]** グラフに**クライアント側のテレメトリ** データを入力できるようにするには、このスクリプトを追跡する各ページに追加します。

   ```HTML
   <!-- 
   To collect user behavior analytics about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. **[調査]** ヘッダーの下にある **[ブラウザー]** をクリックします。 ここで、アプリ ページのパフォーマンスに関連するメトリックを見つけます。 **[Add new chart (新しいグラフを追加)]** をクリックして追加のカスタム ビューを作成したり、**[編集]** を選択して既存のグラフの種類、高さ、カラー パレット、グループ化、およびメトリックを変更したりできます。

   ![サーバー メトリックのグラフ](./media/app-insights-dotnetcore-quick-start/009-Black.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きクイック スタートまたはチュートリアルの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、以下の手順に従い、このクイック スタートで作成したすべてのリソースを Azure Portal で削除してください。

1. Azure Portal の左側のメニューから、**[リソース グループ]**、**[myResourceGroup]** の順にクリックします。
2. リソース グループのページで **[削除]** をクリックし、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [実行時の例外の特定と診断](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
