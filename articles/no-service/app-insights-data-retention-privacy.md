
﻿<properties 
	pageTitle="Data retention and storage in Application Insights" 
	description="Retention and privacy policy statement" 
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
	ms.date="11/14/2014" 
	ms.author="awills"/>
	
=======
<properties title="Data retention and storage in Application Insights" pageTitle="Application Insights でのデータ保持と保存" description="保持およびプライバシー ポリシー ステートメント" metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-14" ms.author="awills" />

>>>>>>> origin/master:articles/no-service/app-insights-data-retention-privacy.md
# Application Insights でのデータ保持と保存 

*Application Insights はプレビュー段階です。*


この記事には、アプリケーションが Application Insights に送信する利用統計情報を格納する方法についての質問に対する回答が記載されています。

Application Insights は、アメリカ合衆国内の Microsoft Azure サーバーにデータを格納します。Microsoft Azure のポリシーの詳細については、[セキュリティ、プライバシー、コンプライアンスのホワイト ペーパー](http://go.microsoft.com/fwlink/?LinkId=392408)をダウンロードしてください。Application Insights について具体的な回答を以下に記載します。 

#### 格納されるデータ量はどれくらいですか。 

Application Insights は、無料のプレビュー段階で、アカウントごとに以下を格納します。 

* 1 秒あたり最大 500 (または 1 分あたり 30,000) の利用統計情報メッセージ  

* 1 か月あたり最大 10,000,000 ページ ビューまたはイベント 

*上限を超えるとどうなりますか。* 

* 短い制限を超えると、いくつかのメッセージがカウント後に削除されます。それらは、[診断検索] には表示されません。イベントのカウントは、正しくなります。メトリックの平均は、保持するイベントに基づいていて、有効である必要があります。 

* 毎月の制限を超えると、イベントの記録が停止されるため、メトリックには日付の後に 0 が表示されます。 

####利用統計情報の保持期間はどれくらいですか。 

* インスタンス データは、[診断検索] で 7 日間表示されます。個々のページ ビュー、イベント、ログ メッセージ、トレース、例外。 

* 集計データは、メトリック エクスプローラーで 13 か月間表示されます。メトリック、イベント、例外 (種類のカウント、失敗した関数など) に関する統計が、1 分間 (またはそれ未満) 単位では 30 日間、1 時間単位では 13 か月間保持されます。 

####データが保持されている場所はどこですか。 

* アメリカ合衆国内です。 

ヨーロッパまたは他の場所に保存するオプションはありますか。 

* まだありません。 

####利用統計情報データの安全性はどの程度ですか。 

データは Microsoft Azure サーバーに保管されます。Azure プレビュー ポータルのアカウントの場合、アカウントの制限は Azure セキュリティ、プライバシー、コンプライアンス文書に記載されています。Visual Studio オンライン ポータルのアカウントの場合は、VS オンライン データ保護のドキュメントが適用されます。 

Microsoft のスタッフによるデータへのアクセスは制限されます。Microsoft は、Application Insights の使用をサポートするために必要であれば、ユーザーからアクセス許可を得た上でデータにアクセスします。 

####Application Insights サーバーでデータは暗号化されますか。 

現時点では暗号化されません。 

####個人を特定できる情報 (PII) は Application Insights に送信されますか。 

はい。カスタムの利用統計情報では、コードによって両方の PII が Application Insights に送信され、PII は標準利用統計情報に含まれます。Application Insights には Azure のプライバシーに関する声明が適用されます。 

データがポータルに送信される可能性があります。また、[診断検索] に表示される可能性があります。組織のメンバーである場合は、データをエクスポートおよびダウンロードできます。 

* 状態モニターをインストールするか、Application Insights をプロジェクトに追加すると、例外またはパフォーマンスの警告が発生したときにスタック トレースがキャプチャされます。これには、SQL データなどの実際のパラメーター データが含まれます。 

* コードに、TrackEvent などの利用統計情報の呼び出しを挿入する場合、またはログ記録フレームワーク メッセージをキャプチャする場合は、プロパティに PII を含めることができます。Microsoft オンライン サービスの使用条件も適用されます。特に、Application Insights の場合は、法律およびプライバシーとデータの収集に適用される規則に準拠する必要があります。情報を収集する前に、通知やその他の同意が必要かどうかを確認してください。 


####Application Insights を使用すると、どのような利用統計情報が送信されますか。 

インストールできるコンポーネントがいくつかあります。(「[Application Insights - 概要][start]」を参照してください)。 

<table>
<tr><th>設定するもの</th><th>ポータルに送信される利用統計情報</th><th>機密性の高いデータが含まれている可能性はありますか。</th></tr>
<tr><td><a href="../app-insights-start-monitoring-app-health-usage/">Web プロジェクト に Application Insights を追加する</a></td>
  <td>メトリック: サーバー要求の数、サーバー応答時間</td>
  <td>いいえ</td></tr>
<tr><td></td>
  <td>サーバーからの例外レポート</td><td>スタック ダンプにパラメーター値を含めることができる</td></tr>
<tr><td><a href="../app-insights-web-track-usage-custom-events-metrics/">カスタム イベントとメトリックを追跡する</a></td>
  <td>コードでアタッチされたプロパティを含むイベント</td>
  <td>はい (コードがプロパティまたはタイトルで PII を送信する場合)</td></tr>
<tr><td><a href="../app-insights-search-diagnostic-logs/#trace">利用統計情報のログおよびトレース</a></td><td>メッセージのログおよびトレース</td><td>ログ メッセージには PII が含まれる</td></tr>
<tr><td><a href="../app-insights-web-track-usage/">Web ページに AI スクリプトを挿入する</a></td>
  <td>匿名のユーザーおよびアカウント ID</td><td>いいえ</td></tr>
<tr><td></td><td>匿名ユーザー セッションの開始と終了</td><td>いいえ</td></tr>
<tr><td></td><td>ページ URI、読み込み時間、セッションのタイミング</td><td>いいえ</td></tr>
<tr><td><a href="../app-insights-monitor-performance-live-website-now/">サーバーに状態モニターをインストールする</a></td>
  <td>依存関係の呼び出しと経過時間</td>
  <td>呼び出しデータにはパラメーター (SQL フィールドなど) を含めることができる</td></tr>
<tr><td></td><td>CPU、メモリ、ネットワーク、その他のリソース カウンター</td><td>いいえ</td></tr>
<tr><td><a href="../app-insights-monitor-web-app-availability/">Web テスト</a></td><td>Web から見た可用性と応答時間</td><td>いいえ</td></tr>
</table>

## <a name="video"></a>ビデオ

#### はじめに

> [AZURE.VIDEO application-insights-introduction]

#### はじめに

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=35.2-->
 
