---
title: Azure App Services のパフォーマンスを監視する | Microsoft Docs
description: Azure App Services のアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122167"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service のパフォーマンスの監視
[Azure portal](https://portal.azure.com) では、[Azure App Service](../../app-service/overview.md) の Web アプリ、モバイル バック エンド、および API アプリのアプリケーション パフォーマンス監視を設定できます。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) は、アクティビティに関するテレメトリを Application Insights サービスに送信するようにアプリをインストルメント化します。これにより、Application Insights サービスでテレメトリを保存および分析できるようになります。 Application Insights では、メトリック グラフや検索ツールを使用して、問題の診断、パフォーマンスの改善、使用状況の評価などを行うことができます。

## <a name="runtime-or-build-time"></a>実行時またはビルド時
監視は、アプリを次の 2 つの方法のどちらかでインストルメント化することによって構成できます。

* **実行時** - アプリ サービスが既に実行されているときにパフォーマンス監視拡張機能を選択できます。 アプリを再構築または再インストールする必要はありません。 応答時間、成功率、例外、依存関係などを監視するパッケージの標準セットを利用できます。

* **ビルド時** - 開発時にアプリにパッケージをインストールすることができます。 これは、汎用性が高い方法です。 同じ標準パッケージに加えて、コードを記述してテレメトリをカスタマイズしたり、独自のテレメトリを送信したりすることができます。 アプリのドメインのセマンティクスに従って、特定のアクティビティをログに記録したり、イベントを記録したりすることができます。 さらに、実行時の監視は最新の安定版リリースに限られているのに対し、この方法であればベータ版 SDK も評価できるので、Application Insights SDK の最新バージョンをテストすることも可能になります。

## <a name="runtime-instrumentation-with-application-insights"></a>Application Insights を使用した実行時インストルメンテーション
Azure でアプリ サービスを現在実行している場合、何らかの監視が既に実行されています。既定では、要求率とエラー率を監視しています。 応答時間、依存関係の呼び出しの監視、スマート検出、強力な Kusto クエリ言語へのアクセスなど、さらに多くの機能を利用するには、Application Insights を追加します。 

1. アプリ サービスの Azure コントロール パネルで **[Application Insights]** を選択します。

    ![[設定] の [Application Insights] を選択する](./media/azure-web-apps/settings-app-insights.png)

   * このアプリケーションの Application Insights リソースを既に設定している場合を除き、新しいリソースの作成を選択します。 

     > [!NOTE]
     > **[OK]** をクリックして新しいリソースを作成すると、**監視の設定を適用する**ように求めるメッセージが表示されます。 **[続行]** を選択すると、新しい Application Insights リソースがアプリ サービスにリンクされ、**アプリ サービスの再起動がトリガー**されます。 

     ![Web アプリをインストルメント化する](./media/azure-web-apps/create-resource.png)

2. 使用するリソースを指定した後、アプリケーションのプラットフォームごとのデータを Application Insights でどのように収集するかを選択できます。 ASP.NET アプリの監視は既定でオンであり、2 つの異なる収集レベルがあります。

    ![プラットフォームごとのオプションを選択する](./media/azure-web-apps/choose-options-new.png)

   * .NET **基本収集**レベルの場合、必要なシングルインスタンス APM 機能が提供されます。
    
   * .NET **推奨収集**レベルの場合、
       * CPU、メモリ、I/O の使用状況傾向が追加されます。
       * 要求/依存関係の境界を越えてマイクロサービスが相互に関連付けられます。
       * 使用状況傾向が収集され、可用性の結果とトランザクションを関連付けることができます。
       * ホスト プロセスで処理されていない例外が収集されます。
       * サンプリングが使用されるとき、負荷の下で APM メトリックの精度が上がります。
    
     .NET Core では、.NET Core 2.0 と 2.1 の場合、**推奨収集**か**無効**が提供されます。

3. Application Insights をインストールしたら、**アプリ サービスをインストルメント化**します。

   ページ ビューとユーザー テレメトリに対する**クライアント側の監視を有効にします**。

    (アプリ設定 'APPINSIGHTS_JAVASCRIPT_ENABLED' の有無に関係なく、.NET Core アプリと**推奨収集**の組み合わせの場合、これは既定で有効になります。 .NET Core では現在のところ、クライアント側の監視を詳細な UI で無効にすることはできません。)
    
   * [設定]、[アプリケーションの設定] の順に選択します
   * [アプリ設定] で、新しいキー値ペアを追加します。

     キー: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     値: `true`
   * 設定を **[保存]** し、アプリを **[再起動]** します。

4. **[設定]** > **[Application Insights]** > **[Application Insights でさらに表示]** を選択して、アプリの監視データを探索します。

後で必要に応じて、Application Insights でアプリを構築できます。

*Application Insights を削除するか、送信先を別のリソースに切り替えるにはどうすればよいですか。*

* Azure 上で Web アプリの制御ブレードを開き、[設定] の下の **[Application Insights]** を開きます。 上部にある **[無効にする]** をクリックするか、**[Change your resource]\(自分のリソースの変更\)** セクションで新しいリソースを選択することで、Application Insights をオフにすることができます。

## <a name="build-the-app-with-application-insights"></a>Application Insights でのアプリのビルド
Application Insights では、アプリへの SDK のインストールによって、より詳細なテレメトリを提供できます。 具体的には、トレース ログの収集、[カスタム テレメトリの作成](../../azure-monitor/app/api-custom-events-metrics.md)、より詳細な例外レポートの取得が可能です。

1. **Visual Studio** (2013 Update 2 以降) で、Application Insights SDK をプロジェクト用に構成します。

    Web プロジェクトを右クリックし、**[追加] > [Application Insights]** または **[プロジェクト]** > **[Application Insights]** > **[Application Insights の構成]** を選択します。

    ![Web プロジェクトを右クリックし、Application Insights の追加または構成を選択する](./media/azure-web-apps/03-add.png)

    サインインが要求されたら、Azure アカウントの資格情報を使用します。

    この操作には、次の 2 つの効果があります。

   1. Azure に Application Insights リソースが作成されます。このリソースでテレメトリが格納、分析、表示されます。
   2. Application Insights NuGet パッケージがコードに追加され (まだ追加されていない場合)、テレメトリを Azure リソースに送信するように構成されます。
2. 開発用コンピューターでアプリを実行して (F5)、**テレメトリをテスト**します。
3. 通常の方法で Azure に**アプリを発行**します。 

*送信先を別の Application Insights リソースに切り替えるにはどうすればよいですか。*

* Visual Studio でプロジェクトを右クリックし、**[Application Insights の構成]** を選択して、目的のリソースを選択します。 新しいリソースを作成するオプションが表示されます。 リビルドして再デプロイします。

## <a name="automate-monitoring"></a>監視の自動化

Application Insights を使用したテレメトリの収集を有効にするのに必要なのは、アプリケーション設定の設定のみです。

   ![利用可能な Application Insights の設定が表示された App Service のアプリケーション設定](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>アプリケーション設定の定義

|アプリ設定の名前 |  定義 | 値 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | メインの拡張機能で、実行時の監視を制御します。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  既定モードのみ、最適なパフォーマンスを保証するため重要な機能が有効になります。 | `default` または `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | バイナリ再書き込みエンジン `InstrumentationEngine` がオンにされるかどうかを制御します。 この設定は、パフォーマンスに影響し、コールド スタート/起動時間に影響を与えます。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL と Azure テーブル テキストが依存関係呼び出しと共にキャプチャされるかどうかを制御します。 パフォーマンスの警告: これには `InstrumentationEngine` が必要です。 | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager を使用した App Service のアプリケーション設定

App Services のアプリケーション設定は、[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)を使用して管理および構成できます。 この手法は、Azure Resource Manager オートメーションで新しい App Service リソースをデプロイするとき、または既存のリソースの設定を変更するときに使用できます。

アプリ サービスに使用されるアプリケーション設定 JSON の基本構造を次に示します。

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

アプリケーション設定が Application Insights 用に構成されている Azure Resource Manager テンプレートの例として、この[テンプレート](https://github.com/Andrew-MSFT/BasicImageGallery)は役立ちます。[238 行目](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)以降のセクションは特に有益です。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Application Insights のリソース、および新しく作成された App Service へのリンクの作成を自動化します。

Application Insights の設定をすべて既定の設定にして Azure Resource Manager テンプレートを作成するには、Application Insights を有効にして新しい Web アプリを作成する場合のようにプロセスを開始します。

**Automation オプション**の選択

   ![App Service の Web アプリ作成メニュー](./media/azure-web-apps/create-web-app.png)

この手順により、必要な設定がすべて設定済みの、最新の Azure Resource Manager テンプレートが生成されます。

  ![App Service の Web アプリ テンプレート](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> このテンプレートは、"既定値" モードのアプリケーション設定を生成します。 ユーザーはテンプレートを変更して任意の機能をアクティブにできますが、このモードはパフォーマンスが最適化されています。

## <a name="more-telemetry"></a>テレメトリの追加

* [Web ページの読み込みデータ](../../azure-monitor/app/javascript.md)
* [カスタムのテレメトリ](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>[拡張機能] - [追加] - [Application Insights extension for new App Service apps (新しい App Service アプリ用 Application Insights の拡張機能)] の順に移動する必要がありますか?

いいえ。拡張機能を手動で追加する必要はもうありません。 [設定] ブレードから Application Insights を有効にすると、監視を有効にするのに必要なすべてのアプリケーション設定が追加されます。 以前は拡張機能によって追加されたファイルが、今では App Service のイメージの一部として[プレインストール](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)されているため、これが可能になっています。 ファイルは `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` にあります。

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>実行時の監視とビルド時の監視を両方とも有効にすると、データが重複しますか?

いいえ。既定で、ビルド時の監視が検出されると、拡張機能を経由する実行時の監視はデータ送信を停止し、ビルド時の監視の設定が優先されます。 実行時の監視を無効にするかどうかの決定は、次の 3 つのファイルのいずれかが検出されるかに基づいて行われます。

* Microsoft.ApplicationInsights dll
* Microsoft.ASP.NET.TelemetryCorrelation dll
* System.Diagnostics.DiagnosticSource dll

Visual Studio の多くのバージョンでは、これらのファイルの一部またはすべてが、既定で ASP.NET テンプレート ファイルや ASP.NET Core Visual Studio テンプレート ファイルに追加されていることを覚えておく必要があります。 プロジェクトがこれらのテンプレートのいずれかに基づいて作成されている場合、ユーザーが明示的に Application Insights を有効にしていない場合でも、ファイルの依存関係が存在することが原因で実行時の監視はアクティブになりません。

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED により、NET CORE Web アプリケーションで HTML の不完全な応答が発生します。

App Services で JavaScript を有効にすると、HTML の応答が切り捨てられる可能性があります。

* 回避策 1: APPINSIGHTS_JAVASCRIPT_ENABLED アプリケーション設定を false に設定するか、完全に削除して、再起動します
* 回避策 2: コードで SDK を追加して、拡張機能を削除します (プロファイラーとスナップショット デバッガーは、この構成では動作しません)

この問題を追跡するには、「[Azure extension causing incomplete HTML response (不完全な HTML 応答の原因となる Azure の拡張機能)](https://github.com/Microsoft/ApplicationInsights-Home/issues/277)」を参照してください。

.NET Core では現在のところ、次は**サポートされていません**。

* 自己完結型のデプロイ。
* .NET Framework を対象とするアプリ。
* .NET Core 2.2 アプリケーション。

> [!NOTE]
> .NET Core 2.0 と .NET Core 2.1 はサポートされています。 .NET Core 2.2 のサポートが追加されるときは、この記事も更新されます。

## <a name="next-steps"></a>次の手順
* [実行中のアプリに対してプロファイラーを実行](../../azure-monitor/app/profiler.md)します。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights で Azure Functions を監視する
* [Azure 診断](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) が Application Insights に送信されるように設定します。
* [サービスの正常性メトリックを監視](../../azure-monitor/platform/data-collection.md)して、サービスの可用性と応答性を確認します。
* 操作イベントが発生したり、メトリックがしきい値を超えたりするたびに、[アラート通知を受け取り](../../azure-monitor/platform/alerts-overview.md)ます。
* [JavaScript のアプリや Web ページに Application Insights](../../azure-monitor/app/javascript.md) を使用して、Web ページを参照しているブラウザーからクライアント テレメトリを取得します。
* [可用性 Web テストを設定](../../azure-monitor/app/monitor-web-app-availability.md) して、サイトがダウンした場合にアラートを送信するようにします。