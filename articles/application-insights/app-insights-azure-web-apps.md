---
title: Azure Web アプリのパフォーマンスの監視 |Microsoft Docs
description: Azure Web アプリのアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: 00af4cc67dc7490727af46e12c387fb0f147371d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339843"
---
# <a name="monitor-azure-web-app-performance"></a>Azure Web アプリのパフォーマンスの監視
[Azure Portal](https://portal.azure.com) では、[Azure Web アプリ](../app-service/app-service-web-overview.md)のアプリケーション パフォーマンス監視を設定できます。 [Azure Application Insights](app-insights-overview.md) は、アクティビティに関するテレメトリを Application Insights サービスに送信するようにアプリをインストルメント化します。これにより、Application Insights サービスでテレメトリを保存および分析できるようになります。 Application Insights では、メトリック グラフや検索ツールを使用して、問題の診断、パフォーマンスの改善、使用状況の評価などを行うことができます。

## <a name="run-time-or-build-time"></a>実行時またはビルド時
監視は、アプリを次の 2 つの方法のどちらかでインストルメント化することによって構成できます。

* **実行時** - Web アプリが既に実行されているときにパフォーマンス監視拡張機能を選択できます。 アプリを再構築または再インストールする必要はありません。 応答時間、成功率、例外、依存関係などを監視するパッケージの標準セットを利用できます。 
* **ビルド時** - 開発時にアプリにパッケージをインストールすることができます。 これは、汎用性が高い方法です。 同じ標準パッケージに加えて、コードを記述してテレメトリをカスタマイズしたり、独自のテレメトリを送信したりすることができます。 アプリのドメインのセマンティクスに従って、特定のアクティビティをログに記録したり、イベントを記録したりすることができます。 

## <a name="run-time-instrumentation-with-application-insights"></a>Application Insights での実行時のインストルメント化
Azure で Web アプリを既に実行している場合、要求率とエラー発生率を既に監視しています。 応答時間、依存関係の呼び出しの監視、スマート検出、強力な Log Analytics クエリ言語など、さらに多くの機能を利用するには、Application Insights を追加します。 

1. Web アプリの Azure コントロール パネルで **[Application Insights] を選択**します。

    ![[設定] の [Application Insights] を選択する](./media/app-insights-azure-web-apps/settings-app-insights.png)

   * このアプリケーションの Application Insights リソースを既に設定している場合を除き、新しいリソースの作成を選択します。 

    > [!NOTE]
    > **[OK]** をクリックして新しいリソースを作成すると、**監視の設定を適用する**ように求めるメッセージが表示されます。 **[続行]** を選択すると、新しい Application Insights リソースが Web アプリにリンクされ、**Web アプリの再起動がトリガー**されます。 

    ![Web アプリをインストルメント化する](./media/app-insights-azure-web-apps/create-resource.png)

2. 使用するリソースを指定した後、アプリケーションのプラットフォームごとのデータを Application Insights でどのように収集するかを選択できます。

    ![プラットフォームごとのオプションを選択する](./media/app-insights-azure-web-apps/choose-options.png)

3. Application Insights をインストールしたら、**Web アプリをインストルメント化**します。

   ページ ビューとユーザー テレメトリに対する**クライアント側の監視を有効にします**。

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
Application Insights では、アプリへの SDK のインストールによって、より詳細なテレメトリを提供できます。 具体的には、トレース ログの収集、[カスタム テレメトリの作成](app-insights-api-custom-events-metrics.md)、より詳細な例外レポートの取得が可能です。

1. **Visual Studio** (2013 Update 2 以降) で、Application Insights SDK をプロジェクト用に構成します。

    Web プロジェクトを右クリックし、**[追加] > [Application Insights]** または **[プロジェクト]** > **[Application Insights]** > **[Application Insights の構成]** を選択します。

    ![Web プロジェクトを右クリックし、Application Insights の追加または構成を選択する](./media/app-insights-azure-web-apps/03-add.png)

    サインインが要求されたら、Azure アカウントの資格情報を使用します。

    この操作には、次の 2 つの効果があります。

   1. Azure に Application Insights リソースが作成されます。このリソースでテレメトリが格納、分析、表示されます。
   2. Application Insights NuGet パッケージがコードに追加され (まだ追加されていない場合)、テレメトリを Azure リソースに送信するように構成されます。
2. 開発用コンピューターでアプリを実行して (F5)、**テレメトリをテスト**します。
3. 通常の方法で Azure に**アプリを発行**します。 

*送信先を別の Application Insights リソースに切り替えるにはどうすればよいですか。*

* Visual Studio でプロジェクトを右クリックし、**[Application Insights の構成]** を選択して、目的のリソースを選択します。 新しいリソースを作成するオプションが表示されます。 リビルドして再デプロイします。

## <a name="more-telemetry"></a>テレメトリの追加

* [Web ページの読み込みデータ](app-insights-javascript.md)
* [カスタムのテレメトリ](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>次の手順
* [実行中のアプリに対してプロファイラーを実行](app-insights-profiler.md)します。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights で Azure Functions を監視する
* [Azure 診断](../azure-monitor/platform/diagnostics-extension-to-application-insights.md) が Application Insights に送信されるように設定します。
* [サービスの正常性メトリックを監視](../azure-monitor/platform/data-collection.md)して、サービスの可用性と応答性を確認します。
* 操作イベントが発生したり、メトリックがしきい値を超えたりするたびに、[アラート通知を受け取り](../azure-monitor/platform/alerts-overview.md)ます。
* [JavaScript のアプリや Web ページに Application Insights](app-insights-javascript.md) を使用して、Web ページを参照しているブラウザーからクライアント テレメトリを取得します。
* [可用性 Web テストを設定](app-insights-monitor-web-app-availability.md) して、サイトがダウンした場合にアラートを送信するようにします。

