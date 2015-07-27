<properties 
	pageTitle="Application Insights でのデータ保持と保存" 
	description="データ保持およびプライバシー ポリシー ステートメント" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="awills"/>

# Application Insights でのデータの収集、保持、保存 

*Application Insights はプレビュー段階です。*

## 概要

この記事では、[Visual Studio Application Insights][start] によって収集されるデータに関する質問に回答すると共に、データがどのように処理され保存されるかについて説明します。

Application Insights は、プレビュー段階の Azure サービスです。プレビュー段階の間、Microsoft は、[Azure のセキュリティ、プライバシー、コンプライアンスに関するホワイト ペーパー](http://go.microsoft.com/fwlink/?linkid=392408)に記載されているポリシーに従ってお客様のデータを保護するよう務めます。


## コレクション

#### Application Insights はどのようにデータを収集しますか。

Application Insights SDK およびエージェントは、アプリケーションに結び付けられると、データを Application Insights サービスに送信します。サービスは、このデータを処理して、レポート、アラート、その他の機能を提供します。これには、プロパティ、カスタム イベントなど、API を使用してキャプチャ対象として選択したデータが含まれます。

#### どれくらいの量のデータをキャプチャできますか。 

**1 秒あたり**: インストルメンテーション キー (つまり、アプリケーション) ごとに 1 秒あたり最大 500 データ ポイントとなっています。[価格レベル][pricing]が Free の場合、上限は1 秒あたり 100 データ ポイントです。

**1 か月あたり**: [料金プラン](http://azure.microsoft.com/pricing/details/application-insights/)に応じて、1 か月あたり 500 万～ 1500 万データ ポイントとなっています。Free [価格レベル][pricing]以外は、上限に達した場合に追加の容量を購入できます。


*データ ポイント*は、テレメトリの項目です。これには、次の項目が含まれます。

* API `Track...` 呼び出し (たとえば、`TrackEvent`、`trackPageView`)。
* 要求、クラッシュなどを報告するために SDK モジュールによって送信されるテレメトリ項目。
* パフォーマンス カウンター データ (測定ごとに 1 つのポイント)。

*アプリがどれだけのデータ ポイントを送信しているかを知る方法はありますか。*

* [設定]、[クォータと価格] の順に開き、[データ ボリューム] グラフを表示します。
* または、メトリックス エクスプローラーで、新しいグラフを追加し、**[データ ポイントの量]** メトリックを選択します。[グループ化] を有効にし、**[データの種類]** を選択してグループ化します。


#### データはどれだけの期間保持されますか。 

これは、[料金プラン](http://azure.microsoft.com/pricing/details/application-insights/)によって異なります。

生データ ポイント (つまり、診断検索で調べることができる項目) は、7 ～ 30 日です。

集計されたデータ (つまり、メトリックス エクスプローラーに表示されるカウント、平均、その他の統計データ) は、1 分の詳細度であれば 30 日、(種類に応じて) 1 時間または 1 日の詳細度であれば少なくとも 13 か月の期間にわたって保持されます。

#### 各種のデータに対してどのような制限がありますか。

1.	アプリケーションに対して最大 200 の一意のメトリックの名前と 200 の一意のプロパティの名前。メトリックには、TrackMetric を通じて送信されるデータと、イベントなどの他のデータ型の測定値が含まれます。[メトリックとプロパティの名前][api]は、データ型にスコープが制限されず、インストルメンテーション キーごとにグローバルです。
2.	各プロパティに対する一意の値は 100 未満であり、[プロパティ][apiproperties]は、フィルタリングとグループ化のみに使用できます。一意の値が 100 を超えた後も、プロパティは検索とフィルタリングに使用できますが、フィルター処理には使用できなくなります。
3.	要求名やページの URL などの標準プロパティは、1 週間あたり 1,000 個の一意な値に制限されます。一意の値が 1,000 個を超えると、追加の値は "その他の値" としてマークされます。元の値は、全文テキスト検索とフィルタリングに引き続き使用できます。


## アクセス

#### データを見ることができるのはだれですか。

お客様と、組織アカウントを持っている場合はチーム メンバーが、データを見ることができます。

お客様とチーム メンバーはデータをエクスポートできます。また、他の場所にデータをコピーしたり、第三者にデータを渡したりすることもできます。

#### アプリから Application Insights に送信された情報を Microsoft はどのように利用しますか。

Microsoft は、お客様にサービスを提供する目的でのみデータを使用します。


## 場所

#### データが保持されている場所はどこですか。 

* アメリカ合衆国内です。 

#### データを他の場所 (たとえば、ヨーロッパ) に保存することはできますか。 

* まだありません。 

## セキュリティ 

#### データのセキュリティは保たれますか。 

データは、Microsoft Azure サーバーに保管されます。Azure プレビュー ポータルのアカウントの場合、アカウントの制限は [Azure のセキュリティ、プライバシー、コンプライアンスの文書](http://go.microsoft.com/fwlink/?linkid=392408)に記載されています。Visual Studio Online ポータルのアカウントの場合は、[Visual Studio Online のデータ保護のドキュメント](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf)が適用されます。

Microsoft のスタッフによるデータへのアクセスは制限されます。Microsoft は、Application Insights の使用をサポートするために必要であれば、ユーザーからアクセス許可を得た上でデータにアクセスします。

このほか、Application Insights の機能の向上に役立てるために、すべてのお客様のアプリケーションのデータ (データ レート、トレースの平均サイズなど) を集計のうえ使用します。

#### だれかのテレメトリが Application Insights データに干渉する可能性はありますか。

Web ページのコード内にインストルメンテーション キーがある場合には、だれかがそれを使用してお客様のアカウントに追加のテレメトリを送信することができます。追加データの量が多いと、アプリのパフォーマンスと使用状況がメトリックに適切に示されなくなる可能性があります。

コードを他のプロジェクトと共有する場合は、インストルメンテーション キーを必ず削除してください。

## 暗号化

#### Application Insights サーバーでデータは暗号化されますか。 

現時点で、サーバー内では暗号化されません。

データをデータ センター間で移動するときは、すべてのデータが暗号化されます。

#### アプリケーションから Application Insights サーバーに送信されるときにデータは暗号化されますか。

はい。ポータルからほぼすべての SDK (Web サーバー、デバイス、HTTPS Web ページを含みます) への送信に https が使用されます。唯一の例外は、プレーンな HTTP Web ページから送信されるデータです。

## 個人を特定できる情報

#### 個人を特定できる情報 (PII) は Application Insights に送信されますか。 

はい。

一般的なガイダンス:

* ほとんどの標準テレメトリ (つまり、コードを書かなくても送信されるテレメトリ) には、明示的な PII は含まれません。ただし、イベントのコレクションから推論することによって、個人を特定できる場合があります。
* 例外とトレースのメッセージには PII が含まれている可能性があります。
* カスタム テレメトリ (つまり、API またはログ トレースを使用してコードに記述する TrackEvent などの呼び出し) には、自分で選んだすべてのデータを含めることができます。


収集されるデータに関する詳細な説明については、このドキュメントの末尾の表をご覧ください。



#### 私は PII に関する法令を遵守する責任を負いますか。

はい。データの収集と使用に関して法令および Microsoft Online Services の条項に従っていることを確認するのはお客様の責任です。

お客様は、アプリケーションが収集するデータと、データの使用方法について、顧客に適切に通知する必要があります。

#### ユーザーは Application Insights を無効にできますか。

直接無効にすることはできません。ユーザーが Application Insights を無効にするために操作できるスイッチはありません。

ただし、アプリケーションでそのような機能を実装することはできます。すべての SDK には、テレメトリの収集を無効にする API 設定が含まれています。

#### アプリケーションが意図せずに機密情報を収集しています。このデータの収集を取り消して Application Insights にデータが保持されないようにすることはできますか。

Application Insights がデータをフィルター処理したり、削除したりすることはありません。データを適切に管理して、そのようなデータが Application Insights に送信されないように注意してください。



## Application Insights によって送信されるデータ

SDK はプラットフォームごとに異なり、インストールできるコンポーネントも複数あります ([Application Insights の概要][start]に関するページを参照してください)。 各コンポーネントは、それぞれ異なるデータを送信します。

#### さまざまなシナリオで送信されるデータのクラス

操作 | 収集されるデータのクラス (次の表を参照)
---|---
[Application Insights SDK を .NET Web プロジェクトに追加する][greenbrown] | ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Exceptions**<br/>Session<br/>ユーザー
[Status Monitor を IIS にインストールする][redfield]<br/>[AI 拡張機能を Azure VM または Web アプリに追加する][azure]|Dependencies<br/>ServerContext<br/>Inferred<br/>Perf counters
[Application Insights SDK を Java Web アプリに追加する][java]|ServerContext<br/>Inferred<br/>Request<br/>Session<br/>ユーザー
[JavaScript SDK を Web ページに追加する][client]|ClientContext <br/>Inferred<br/>Page<br/>ClientPerf
[SDK を Windows ストア アプリに追加する][windows]|DeviceContext<br/>ユーザー<br/>Crash data
[既定のプロパティを定義する][apiproperties]|**Properties** (すべての標準イベントおよびカスタム イベント)
[Call TrackMetric][api]|数値<br/>**Properties**
[Call Track*][api]||イベント名<br/>**Properties**
[Call TrackException][api]|**Exceptions**<br/>スタック ダンプ<br/>**Properties**
SDK はデータを収集できません。例: <br/> - パフォーマンス カウンターにアクセスできない<br/> - テレメトリ初期化子での例外 | SDK diagnostics
 

[他のプラットフォームの SDK][platforms] については、該当するドキュメントを参照してください。



#### 収集されるデータのクラス

収集されるデータのクラス | 含まれるデータ (網羅的なリストではありません) 
---|---
**プロパティ**|**コードによって決まる任意のデータ**
DeviceContext |Id、IP、ロケール、デバイス モデル、ネットワーク、ネットワークの種類、OEM の名前、画面解像度、ロール インスタンス、ロール名、デバイスの種類
ClientContext |OS、ロケール、言語、ネットワーク、ウィンドウの解像度
Session | セッション ID
ServerContext |コンピューター名、ロケール、OS、デバイス、ユーザー セッション、ユーザー コンテキスト、操作 
Inferred |IP アドレス、タイムスタンプ、OS、ブラウザーからの地理的場所
メトリック | メトリックの名前と値
イベント | イベントの名前と値
PageViews | URL とページ名または画面名
Client perf | URL/ページ名、ブラウザーの読み込み時間
Requests |URL、期間、応答コード
依存関係|種類 (SQL、HTTP、...)、接続文字列または URI、同期または非同期、期間、成功、SQL ステートメント (Status Monitor による)
**Exceptions** | 種類、**メッセージ**、呼び出し履歴、ソース ファイルと行の番号、スレッド ID
Crashes | プロセス ID、親プロセスの ID、クラッシュ スレッドの ID。アプリケーションの修正プログラム、ID、ビルド。例外の種類、アドレス、理由。難読化されたシンボルとレジスタ、バイナリの開始アドレスと終了アドレス、バイナリ名とパス、CPU の種類
Trace | **メッセージ**と重大度レベル
Perf counters | プロセッサ時間、使用可能なメモリ、要求レート、例外レート、プロセスのプライベート バイト、IO レート、要求の期間、要求のキューの長さ
可用性 | Web テストの応答コード、各テスト ステップの期間
SDK diagnostics | トレース メッセージまたは例外 

[ApplicationInsights.config を編集して、データの一部を無効にできます][config]


## クレジット

この製品には、MaxMind によって作成された GeoLite2 データが含まれています。MaxMind は [http://www.maxmind.com](http://www.maxmind.com) から入手できます。

## <a name="video"></a>ビデオ

#### はじめに

> [AZURE.VIDEO application-insights-introduction]

#### 作業開始

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=July15_HO3-->