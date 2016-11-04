---
title: Application Insights ポータルの使用
description: Application Insights ポータルのメトリック、ダッシュボード、および設定。
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 07/30/2016
ms.author: awills

---
# Application Insights ポータルのナビゲーションおよびダッシュボード
[Application Insights をプロジェクトに設定](app-insights-overview.md)すると、アプリのパフォーマンスと使用状況に関するテレメトリ データが、[Azure ポータル](https://portal.azure.com)でプロジェクトの Application Insights リソースに表示されます。

## テレメトリの検索
[Azure ポータル](https://portal.azure.com)にサインインし、アプリ用に作成した Application Insights リソースを参照します。

![[参照] をクリックし、[Application Insights]、アプリの順に選択します。](./media/app-insights-dashboards/00-start.png)

[概要] ページには、いくつかの基本的なテレメトリとその他のリンクが示されます。内容は、アプリの種類によって異なり、カスタマイズすることができます。

## アプリの [概要] ブレード
アプリの [概要] ブレード (ページ) には、アプリの主要な診断メトリックの概要が表示されます。また、[概要] ブレードはポータルの他の機能のゲートウェイでもあります。

次の機能を利用できます。

* **グラフやタイル**をクリックすると、グラフに表示されているアイテムの詳細が表示されます。
* **[設定]** をクリックすると、他のメトリックの定義済みブレードと、構成ページが表示されます。
* [**[メトリックス エクスプ ローラー]**](app-insights-metrics-explorer.md) をクリックすると、選択したメトリックのページが作成されます。
* [**[検索]**](app-insights-diagnostic-search.md) をクリックすると、要求、例外、またはログ トレースなど、特定のイベントのインスタンスを見つけることができます。
* [**[分析]**](app-insights-analytics.md) をクリックすると、テレメトリに対して強力なクエリを実行することができます。
* [**[Live Stream (ライブ ストリーム)]**](app-insights-metrics-explorer.md#live-stream) をクリックすると、ほぼ即時メトリックの固定セットが表示されます。これは、新しいビルドを展開するとき、またはデバッグのときに便利です。

![テレメトリを表示する主な方法](./media/app-insights-dashboards/010-oview.png)

### [概要] ブレードのカスタマイズ
概要を表示する対象を選択します。[カスタマイズ] では、セクション タイトルの挿入、タイルやグラフのドラッグ、項目の削除、ギャラリーからの新しいタイルやグラフの追加ができます。

![[編集] をクリックします。タイルやグラフをドラッグします。ギャラリーからタイルを追加します。[完了] をクリックします。](./media/app-insights-dashboards/020-customize.png)

## ダッシュボード
[Microsoft Azure ポータル](https://portal.azure.com)にサインインすると、まず、ダッシュボードが表示されます。ここでは、[Visual Studio Application Insights](app-insights-overview.md) のテレメトリなどを含む、すべての Azure リソースから最も重要なグラフをまとめて表示することができます。

![カスタマイズされたダッシュボード](./media/app-insights-dashboards/31.png)

1. ダッシュボードを編集、作成、共有するには、ダッシュボードのツールバーを使用します。
2. 現在のダッシュボードに戻るか、または最近アクセスした他のビューに切り替えます。
3. ダッシュボードを切り替えます。
4. ダッシュボードを作成、編集、共有します。
5. タイルにマウス ポインターを合わせてから、上部のバーを使ってタイルを移動、カスタマイズ、削除します。

## ダッシュボードへの追加
特に重要なブレードやグラフのセットがあれば、そのコピーをダッシュボードにピン留めしておきましょう。ダッシュボードを開いたときにすぐに見ることができます。

![グラフをピン留めするには、マウスでポイントし、ヘッダーの [...] をクリックします。](./media/app-insights-dashboards/33.png)

1. ダッシュボードにグラフをピン留めします。グラフのコピーがダッシュボードに表示されます。
2. ブレード全体をダッシュボードにピン留めします。クリックして進むことのできるタイルとしてブレード全体がダッシュボードに表示されます。
3. 現在のダッシュボードに戻るには、左上隅をクリックします。その後ドロップダウン メニューを使用して現在のビューに戻ることができます。

グラフはタイルにグループ化されます。1 つのタイルに複数のグラフを含めることができます。タイル全体をダッシュボードにピン留めします。

### Analytics のグラフ
[共有](#share-dashboards-with-your-team)ダッシュボードに[Analytics のグラフをピン留め](app-insights-analytics-using.md#pin-to-dashboard)することもできます。これにより、任意のクエリのグラフを標準的なメトリックの横に追加できます。

## ダッシュボード上のタイルの調整
タイルをダッシュボード上に追加すると、タイルを調整することができます。

![グラフを編集するには、そのグラフ上にポインターを置きます。](./media/app-insights-dashboards/36.png)

1. タイルにグラフを追加します。
2. メトリック、グループ化ディメンションおよびグラフのスタイル (テーブル、グラフ) を設定します。
3. タイルのグラフに、期間とフィルターのプロパティを設定します。グラフの独自の期間を設定したり、ダッシュ ボードの期間を継承したりすることができます。
4. タイルのタイトルを設定します。

メトリックス エクスプ ローラーのブレードからピン留めされたタイルには、概要ブレードからピン留めされたタイルよりも多くの編集オプションがあります。

ピン留めした元のタイルは、編集の影響を受けません。

## ダッシュボード間の切り替え
複数のダッシュボードを保存して、切り替えることができます。ピン留めしたグラフやブレードは、そのとき開いていたダッシュボードに追加されます。

![ダッシュボード間を切り替えるには、[ダッシュボード] をクリックし、保存されているダッシュボードを選択します。新しいダッシュボードを作成して保存するには、[新規] をクリックします。並べ替えるには、[編集] をクリックします。](./media/app-insights-dashboards/32.png)

たとえば、チーム ルームを全画面で表示するダッシュボードを作成し、全般的な開発用に別のダッシュボードを作成することができます。

このダッシュボードでは、ブレードがタイルとして表示されています。タイルをクリックするとブレードに移動します。グラフは、元の場所のグラフを複製したものです。

![タイルを表すブレードを開くには、そのタイルをクリックします](./media/app-insights-dashboards/35.png)

## チームとのダッシュボードの共有
ダッシュボードを作成すると、他のユーザーとそのダッシュボードを共有できます。

![ダッシュボードのヘッダーで [共有] をクリックします](./media/app-insights-dashboards/41.png)

詳細については、[ロールとアクセス制御](app-insights-resources-roles-access-control.md)に関するページをご覧ください。

## Settings
[概要] ブレードの [設定] ボタンは、構成に関するものだけでなく、複数の種類のグラフを設定することもできます。

* **監査ログ** - アラートと、アプリ リソース上で実行されたアクションを確認します。
* [新しいサポート要求](app-insights-get-dev-support.md) - Azure のさまざまな側面が記載されたヘルプのオプションです。Application Insights では、ウィンドウの右上にある [スマイリー] ボタンを使用してヘルプを取得することもできます。
* [アプリケーション マップ](app-insights-dependencies.md#application-map) - 依存関係情報から取得されるアプリケーションのコンポーネントを示すアクティブ マップです。
* [ライブ メトリック ストリーム](app-insights-metrics-explorer.md#live-metrics-stream) - 新しいリリースを監視するための、待機時間が約 1 秒の重要なメトリックです。
* **エラー** - 失敗した要求、失敗した依存関係の呼び出し、例外です。[編集可能なメトリック ブレード](app-insights-metrics-explorer.md)です。
* **パフォーマンス** - 応答時間および依存関係の応答時間です。[編集可能なメトリック ブレード](app-insights-metrics-explorer.md)です。
* [サーバー](app-insights-web-monitor-performance.md) - パフォーマンス カウンターです。[Status Monitor をインストール](app-insights-monitor-performance-live-website-now.md)している場合に使用できます。
* **ブラウザー** - ページ ビューと AJAX パフォーマンスです。[Web ページをインストルメント化](app-insights-javascript.md)している場合に使用できます。
* **使用状況** - ページ ビュー、ユーザー、およびセッションの数です。[Web ページをインストルメント化](app-insights-javascript.md)している場合に使用できます。
* **作業の開始** - インライン チュートリアルです。
* **プロパティ** - インストルメンテーション キー、サブスクリプション、およびリソース ID です。
* [状態](app-insights-monitor-web-app-availability.md) - Web テストの構成と結果です。
* [アラート](app-insights-alerts.md) - メトリック アラートの構成です。
* [連続エクスポート](app-insights-export-telemetry.md) - Azure ストレージへのテレメトリのエクスポートを構成します。
* [パフォーマンス テスト](app-insights-monitor-web-app-availability.md#performance-tests) - Web サイトでの代理負荷を設定します。
* [クォータと価格](app-insights-pricing.md)および[インジェスト サンプリング](app-insights-sampling.md)。
* **API アクセス** - 現在は、[リリース注釈](app-insights-annotations.md)の作成とデータ アクセス API で使用されています。
* [**作業項目**](app-insights-diagnostic-search.md#create-work-item) - テレメトリを確認しながらバグを作成できるように、作業項目追跡システムに接続します。
* [**ユーザー**](app-insights-resources-roles-access-control.md) - アプリ リソースにアクセスできるユーザーを管理します。
* [**ロール**](app-insights-resources-roles-access-control.md) - ユーザーができることを管理します。
* [**タグ**](../resource-group-using-tags.md) - Azure リソースを整理します。
* [**ロック**](../resource-group-lock-resources.md) - Azure リソースをロックします。
* [**テンプレートのエクスポート**](app-insights-powershell.md) - 新しいリソースを作成するテンプレートとして使用できるように、Azure リソースの定義をエクスポートします。

## 次の手順
|  |  |
| --- | --- |
| [メトリックス エクスプローラー](app-insights-metrics-explorer.md)<br/>メトリックのフィルター処理と分割 |![検索例](./media/app-insights-dashboards/64.png) |
| [診断検索](app-insights-diagnostic-search.md)<br/>イベントや関連イベントを検索して確認し、バグを作成 |![検索例](./media/app-insights-dashboards/61.png) |
| [Analytics](app-insights-analytics.md)<br/>強力なクエリ言語 |![検索例](./media/app-insights-dashboards/63.png) |

<!---HONumber=AcomDC_0817_2016-->