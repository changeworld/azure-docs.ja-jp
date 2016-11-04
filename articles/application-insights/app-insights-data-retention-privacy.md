---
title: Application Insights でのデータ保持と保存
description: データ保持およびプライバシー ポリシー ステートメント
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2016
ms.author: awills

---
# Application Insights でのデータの収集、保持、保存
*Application Insights はプレビュー段階です。*

アプリに [Visual Studio Application Insights][start] SDK をインストールすると、アプリに関する製品利用統計情報がクラウドに送信されます。当然ながら、担当開発者は、送信されるデータ、データに対して発生すること、それを制御する方法について知りたいと考えます。特に、機密データの送信、その保存先、安全性が重要です。

まず、手短に言えば次のようになります。

* 「難しい設定なしで」動く標準の製品利用統計情報モジュールは、多くの場合、機密データをサービスに送信しません。製品利用統計情報は、負荷指標、パフォーマンス指標、利用率指標、例外レポート、その他の診断データに関連します。診断レポートに表示される主なユーザー データは URL ですが、いかなる場合も、アプリは URL にプレーンテキストで機密データを入力するべきではありません。
* 診断と監視利用に役立つ追加のカスタム製品利用統計情報を送信するコードを記述できます。(この拡張機能は Application Insights の優れた機能です。) 手違いにより、このコードを記述し、個人データやその他の機密データが含まれてしまうことはあります。アプリケーションがそのようなデータを利用する場合、記述するあらゆるコードに強力なレビュー プロセスを適用してください。
* アプリを開発し、テストするとき、SDK により送信される内容は簡単に調査できます。データは IDE とブラウザーのデバッグ出力ウィンドウに表示されます。
* データは米国の [Microsoft Azure](http://azure.com) サーバーに保管されます。(ただし、アプリは、場所を問わず実行できます)。 Azure には[強力なセキュリティ プロセスがあり、広範囲のコンプライアンス標準を満たします](https://azure.microsoft.com/support/trust-center/)。あなたとあなたが指名したチームだけがあなたのデータにアクセスできます。Microsoft のスタッフには、特定の状況下でのみ、あなたに通知した上で限られたアクセスが与えられます。転送中は暗号化されます。サーバーに保管されているときは暗号化されません。

この記事の残りの部分では、以上の答えについてもっと詳しく説明します。設計は自己完結型であり、直近のチームに入っていない同僚にも見せることができます。

## Application Insights とは何か?
[Visual Studio Application Insights][start] はライブ アプリケーションのパフォーマンスと使いやすさを改善する Microsoft 提供サービスです。テスト中と公開後またはデプロイ後の両方で、実行中のアプリケーションを常時監視します。Application Insights が作成するグラフや表を見ると、たとえば、1 日の中でユーザー数が最も多い時間帯、アプリの反応性、アプリが依存している外部サービスのサービス性能などがわかります。クラッシュ、エラー、パフォーマンス問題が発生した場合、製品利用統計情報データを詳しく調査し、原因を診断できます。アプリの可用性やパフォーマンスに変化があった場合、サービスからメールが届きます。

この機能を入手するには、アプリケーションに Application Insights SDK をインストールします。インストールすることでそのコードの一部になります。アプリの実行中、SDK はその操作を監視し、製品利用統計情報を Application Insights サービスに送信します。これは [Microsoft Azure](http://azure.com) がホストするクラウド サービスです。(ただし、Application Insights は、Azure にホストされているアプリケーションだけでなく、あらゆるアプリケーションで動作します。)

![アプリの SDK は製品利用統計情報を Application Insights サービスに送信します。](./media/app-insights-data-retention-privacy/01-scheme.png)

Application Insights サービスは製品利用統計情報を保存し、分析します。分析を確認し、保存されている製品利用統計情報を検索するには、Azure アカウントにサインインし、アプリケーションの Application Insights リソースを開きます。データへのアクセスをチームの他のメンバーや指定した Azure サブスクライバーと共有することもできます。

Application Insights サービスから、たとえば、データベースや外部ツールにデータをエクスポートできます。サービスから取得した特別なキーを各ツールに提供します。キーは必要に応じて取り消すことができます。

Application Insights SDK はさまざまなアプリケーション タイプに利用できます。独自の J2EE サーバー、ASP.NET サーバー、Azure にホストされている Web サービス、Web クライアント (Web ページで実行されるコード)、デスクトップのアプリとサービス、デバイス アプリ (Windows Phone、iOS、Android) などです。これらはすべて同じサービスに製品利用統計情報を送信します。

## どのようなデータが収集されますか。
### データはどのような方法で収集されますか。
データ ソースは 3 つあります。

* SDK は[デプロイ時](app-insights-asp-net.md)または[実行時](app-insights-monitor-performance-live-website-now.md)にアプリと統合します。アプリケーションの種類が違えば SDK も違います。[Web ページ用の SDK](app-insights-javascript.md) もあります。ページと共にエンドユーザーのブラウザーに読み込まれます。
  
  * それぞれの SDK にはさまざまな[モジュール](app-insights-configuration-with-applicationinsights-config.md)があり、さまざまな手法でさまざまな種類の製品利用統計情報を収集します。
  * デプロイ時に SDK をインストールする場合、標準のモジュールに加え、その API を利用し、独自の製品利用統計情報を送信できます。このカスタム製品利用統計情報にあらゆるデータを含め、送信できます。
* 一部の Web サーバーには、アプリと共に実行され、CPU、メモリ、ネットワーク占有率に関する製品利用統計情報を送信するエージェントもあります。たとえば、Azure VM、Docker ホスト、[J2EE サーバー](app-insights-java-agent.md)にそのようなエージェントがあります。
* [可用性テスト](app-insights-monitor-web-app-availability.md)は Microsoft が実行するプロセスであり、定期的な間隔で要求を Web アプリに送信します。結果は Application Insights サービスに送信されます。

### どのような種類のデータが収集されますか。
主なカテゴリは次のとおりです。

* [Web サーバー製品利用統計情報](app-insights-asp-net.md) -HTTP 要求。URI、要求の処理にかかる時間、応答コード、クライアント IP アドレス。セッション ID。
* [Web ページ](app-insights-javascript.md) - ページ、ユーザーとセッションの数。ページの読み込み時間。例外。AJAX 呼び出し。
* パフォーマンス カウンター - メモリ、CPU、IO、ネットワーク占有率。
* クライアントとサーバーのコンテキスト - OS、ロケール、デバイスの種類、ブラウザー、画面の解像度。
* [例外](app-insights-asp-net-exceptions.md)とクラッシュ - **スタック ダンプ**、ビルド ID、CPU タイプ。
* [依存関係](app-insights-asp-net-dependencies.md) - REST、SQL、AJAX など、外部サービスの呼び出し。URI または接続文字列、期間、成功、コマンド。
* [可用性テスト](app-insights-monitor-web-app-availability.md) - テストとステップの期間、応答。
* [トレース ログ](app-insights-search-diagnostic-logs.md)と[カスタム製品利用統計情報](app-insights-api-custom-events-metrics.md) - **コード化してログまたは製品利用統計情報に入れるすべて**。

[詳細](#data-sent-by-application-insights)。

## 収集された内容はどのような方法で検証しますか。
Visual Studio でアプリを開発している場合、デバッグ モードでアプリを実行してください (F5)。製品利用統計情報は出力ウィンドウに表示されます。そこから、それをコピーし、JSON として書式設定すれば、調査が簡単になります。

![](./media/app-insights-data-retention-privacy/06-vs.png)

[診断] ウィンドウには、さらに読みやすいビューもあります。

Web ページの場合、ブラウザーのデバッグ ウィンドウを開きます。

![F12 を押し、[ネットワーク] タブを開きます。](./media/app-insights-data-retention-privacy/08-browser.png)

### 送信前に製品利用統計情報を絞り込むコードを記述できますか。
[製品利用統計情報プロセッサ プラグイン](app-insights-api-filtering-sampling.md)を記述することで可能です。

## データはどれだけの期間保持されますか。
生データ ポイント (つまり、診断検索で調べることができる項目) は、7 日間保持されます。それ以上長くデータを保持する必要がある場合は、[連続エクスポート](app-insights-export-telemetry.md)を使用してストレージ アカウントにコピーすることができます。

集計されたデータ (つまり、メトリックス エクスプローラーに表示されるカウント、平均、その他の統計データ) は、1 分の詳細度であれば 30 日、(種類に応じて) 1 時間または 1 日の詳細度であれば少なくとも 90 日の期間にわたって保持されます。

## 誰がデータにアクセスできますか。
お客様と、組織アカウントを持っている場合はチーム メンバーが、データを見ることができます。

お客様とチーム メンバーはデータをエクスポートできます。また、他の場所にデータをコピーしたり、第三者にデータを渡したりすることもできます。

#### アプリから Application Insights に送信された情報を Microsoft はどのように利用しますか。
Microsoft は、お客様にサービスを提供する目的でのみデータを使用します。

## データが保持されている場所はどこですか。
* アメリカ合衆国内です。

#### データを他の場所 (たとえば、ヨーロッパ) に保存することはできますか。
* 現時点ではすべてではありません。

#### それはアプリを米国内でホストする必要があるという意味ですか。
* いいえ。アプリは、独自のオンプレミスのホストでもクラウドでも、場所を問わず実行できます。

## データのセキュリティは保たれますか。
Application Insights は、プレビュー段階の Azure サービスです。プレビュー段階の間、Microsoft は、[Azure のセキュリティ、プライバシー、コンプライアンスに関するホワイト ペーパー](http://go.microsoft.com/fwlink/?linkid=392408)に記載されているポリシーに従ってお客様のデータを保護するよう務めます。

データは、Microsoft Azure サーバーに保管されます。Azure ポータルのアカウントの場合、アカウントの制限は [Azure のセキュリティ、プライバシー、コンプライアンスの文書](http://go.microsoft.com/fwlink/?linkid=392408)に記載されています。Visual Studio Team Services ポータルのアカウントの場合は、[Visual Studio Team Services のデータ保護のドキュメント](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf)が適用されます。

Microsoft のスタッフによるデータへのアクセスは制限されます。Microsoft は、Application Insights の使用をサポートするために必要であれば、ユーザーからアクセス許可を得た上でデータにアクセスします。

このほか、Application Insights の機能の向上に役立てるために、すべてのお客様のアプリケーションのデータ (データ レート、トレースの平均サイズなど) を集計のうえ使用します。

#### だれかのテレメトリが Application Insights データに干渉する可能性はありますか。
Web ページのコード内にインストルメンテーション キーがある場合には、だれかがそれを使用してお客様のアカウントに追加のテレメトリを送信することができます。追加データの量が多いと、アプリのパフォーマンスと使用状況がメトリックに適切に示されなくなる可能性があります。

コードを他のプロジェクトと共有する場合は、インストルメンテーション キーを必ず削除してください。

## データは暗号化されますか。
現時点で、サーバー内では暗号化されません。

データをデータ センター間で移動するときは、すべてのデータが暗号化されます。

#### アプリケーションから Application Insights サーバーに送信されるときにデータは暗号化されますか。
はい。ポータルからほぼすべての SDK (Web サーバー、デバイス、HTTPS Web ページを含みます) への送信に https が使用されます。唯一の例外は、プレーンな HTTP Web ページから送信されるデータです。

## 個人を特定できる情報
#### 個人を特定できる情報 (PII) は Application Insights に送信されますか。
はい、できます。

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
| 操作 | 収集されるデータのクラス (次の表を参照) |
| --- | --- |
| [Application Insights SDK を .NET Web プロジェクトに追加する][greenbrown] |ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Exceptions**<br/>Session<br/>ユーザー |
| [Status Monitor を IIS にインストールする][redfield] |Dependencies<br/>ServerContext<br/>Inferred<br/>Perf counters |
| [Application Insights SDK を Java Web アプリに追加する][java] |ServerContext<br/>Inferred<br/>Request<br/>Session<br/>ユーザー |
| [JavaScript SDK を Web ページに追加する][client] |ClientContext <br/>Inferred<br/>Page<br/>ClientPerf<br/>Ajax |
| [既定のプロパティを定義する][apiproperties] |**Properties** (すべての標準イベントおよびカスタム イベント) |
| [Call TrackMetric][api] |数値<br/>**Properties** |
| [Call Track*][api] |イベント名<br/>**Properties** |
| [Call TrackException][api] |**Exceptions**<br/>スタック ダンプ<br/>**Properties** |
| SDK はデータを収集できません。例: <br/> - パフォーマンス カウンターにアクセスできない<br/> - テレメトリ初期化子での例外 |SDK diagnostics |

[他のプラットフォームの SDK][platforms] については、該当するドキュメントを参照してください。

#### 収集されるデータのクラス
| 収集されるデータのクラス | 含まれるデータ (網羅的なリストではありません) |
| --- | --- |
| **プロパティ** |**コードによって決まる任意のデータ** |
| DeviceContext |Id、IP、ロケール、デバイス モデル、ネットワーク、ネットワークの種類、OEM の名前、画面解像度、ロール インスタンス、ロール名、デバイスの種類 |
| ClientContext |OS、ロケール、言語、ネットワーク、ウィンドウの解像度 |
| Session |セッション ID |
| ServerContext |コンピューター名、ロケール、OS、デバイス、ユーザー セッション、ユーザー コンテキスト、操作 |
| Inferred |IP アドレス、タイムスタンプ、OS、ブラウザーからの geo ロケーション |
| メトリック |メトリックの名前と値 |
| イベント |イベントの名前と値 |
| PageViews |URL とページ名または画面名 |
| Client perf |URL/ページ名、ブラウザーの読み込み時間 |
| Ajax |Web ページからサーバーへの HTTP 呼び出し |
| 要求数 |URL、期間、応答コード |
| 依存関係 |種類 (SQL、HTTP、...)、接続文字列または URI、同期または非同期、期間、成功、SQL ステートメント (Status Monitor による) |
| **Exceptions** |種類、**メッセージ**、呼び出し履歴、ソース ファイルと行の番号、スレッド ID |
| Crashes |プロセス ID、親プロセスの ID、クラッシュ スレッドの ID。アプリケーションの修正プログラム、ID、ビルド。例外の種類、アドレス、理由。難読化されたシンボルとレジスタ、バイナリの開始アドレスと終了アドレス、バイナリ名とパス、CPU の種類 |
| Trace |**メッセージ**と重大度レベル |
| Perf counters |プロセッサ時間、使用可能なメモリ、要求レート、例外レート、プロセスのプライベート バイト、IO レート、要求の期間、要求のキューの長さ |
| 可用性 |Web テストの応答コード、各テスト ステップの期間、テスト名、タイムスタンプ、成功、応答時間、テストの場所 |
| SDK diagnostics |トレース メッセージまたは例外 |

[ApplicationInsights.config を編集して、データの一部を無効にできます][config]

## 謝辞
この製品には、MaxMind によって作成された GeoLite2 データが含まれています。MaxMind は [http://www.maxmind.com](http://www.maxmind.com) から入手できます。

## <a name="video"></a>ビデオ
#### はじめに
> [!VIDEO https://channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Application-Insights-Introduction/player]
> 
> 

#### 作業開始
> [!VIDEO https://channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Getting-Started-with-Application-Insights/player]
> 
> 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md



<!---HONumber=AcomDC_0907_2016-->