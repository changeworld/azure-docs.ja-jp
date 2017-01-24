---
title: "Application Insights の価格とデータ ボリュームの管理 | Microsoft Docs"
description: "Application Insights でテレメトリの量を管理し、コストを監視します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 95a8f923b5afa086aa2705bfb80e5dada7979e2e
ms.openlocfilehash: 82a8dd3a68603336f6d0f9857ce9516e48ce5ade


---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Application Insights での価格とデータ ボリュームの管理


[Azure Application Insights][start] の価格は、アプリケーションごとのデータ量に基づきます。 デプロイメント時または小規模なアプリで使用頻度が低い場合は、無料になる可能性があります。これは、毎月無料のテレメトリ データ許容量が設けられているためです。

Application Insights の各リソースは個々のサービスとして課金され、Azure のサブスクリプションの課金内容に加えられます。

2 つの価格プランがあります。 既定のプランは、Basic と呼ばれます。 Enterprise プランを選択できます。ここでは日単位で料金が発生しますが、[連続エクスポート](app-insights-export-telemetry.md)などの特定の追加の機能を有効にすることができます。


## <a name="the-pricing-plans"></a>価格プラン


現在の料金については、[価格プランを参照してください][pricing]。

### <a name="basic-plan"></a>Basic プラン

既定のプランは **Basic** です。 

* このプランは、[連続エクスポート](app-insights-export-telemetry.md)や[Log Analytics コネクタ](https://go.microsoft.com/fwlink/?LinkId=833039&clcid=0x409)などの機能が必要ない場合にのみ使用してください。 これらは、Basic プランでは利用できません。
* Basic プランでは、Application Insights ポータルで受信したテレメトリのギガバイト数に応じて料金が課金されます。
* いずれのアプリについても、最初の 1 GB 無料です。そのため、試験や開発の段階で換金されることはあまりありません。

### <a name="enterprise-plan"></a>Enterprise プラン

* Enterprise プランでは、Application Insights のすべての機能をアプリで使用できます。 
* Enterprise プランでは、アプリに対してテレメトリを送信しているノードごとに料金が課金されます。 
 * *ノード*とは、アプリをホストする物理または仮想サーバー マシン (または Platform-as-a-Service (PaaS) ロール インスタンス) のことです。
 * 開発マシンと、クライアントのブラウザーやデバイスはこのカウントには含められません。 
 * テレメトリを送信するコンポーネント (Web サービスやバックエンド ワーカーなど) がアプリに複数ある場合、それらは個別にカウントされます。
* サブスクリプション全体を通じて、料金はノード単位で計算されます (アプリ単位ではありません)。 12 のアプリに対して 5 つのノードがテレメトリを送信している場合、料金は 5 ノード分になります。
* 料金の見積りは月単位で計算されますが、実際の料金は、ノードがアプリからテレメトリを送信した時間分しか課金されません。 1 時間あたりの料金は、1 か月あたりの見積り額 / 744 です。 アプリが一度に複数のサーバーを使用するようスケールされた場合、 
* お客様には、(1 つ以上のエンタープライズ アプリから) テレメトリを送信するノードごとに、テレメトリのクォータが割り当てられます。 これは、Azure サブスクリプション内のすべてのエンタープライズ アプリを対象に、終日累積されます。 毎日、1 日の終わり (午前 0 時 (UTC)) に、お客様のエンタープライズ アプリが累積クォータを超えて送信したテレメトリに対し、料金が課金されます。 
* クォータが日をまたいで繰り越されることはありません。


### <a name="multi-step-web-tests"></a>複数手順の Web テスト

[**複数ステップ Web テスト**](app-insights-monitor-web-app-availability.md#multi-step-web-tests)に対しては、追加料金が発生します。 これは、一連のアクションを実行する Web テストのことです。 

単一ページの ping テストについては、個別の料金はかかりません。 Ping テストと複数ステップ テストの両方からのテレメトリについては、アプリの他のテレメトリと一緒に課金されます。

## <a name="review-pricing-plan-for-your-application-insights-resource"></a>Application Insights リソースの価格プランの確認
お使いのアプリケーションの Application Insights リソースの [Features + Pricing (機能と価格設定)] ブレードを開きます。

![[Pricing (価格設定)] を選択します。](./media/app-insights-pricing/01-pricing.png)

1. 該当の月のデータ量を確認します。 これには、(サーバーおよびクライアント アプリからの、また可用性テストからの[サンプリング](app-insights-sampling.md)の後) 受信され保持されたすべてのデータが含まれます。
2. [複数ステップ Web テスト](app-insights-monitor-web-app-availability.md#multi-step-web-tests)で別料金が発生しています。 (これには、データ ボリューム料金に含まれるシンプルな可用性テストは含まれません。)
3. Enterprise プランで提供される追加の機能を有効にします。 このプランには、無料のデータ許容量はありません。
4. データ管理オプションをクリックして 1 日あたりの上限を設定するか、インジェスト サンプリングを設定します。

Application Insights の課金は Azure の課金内容に加えられます。 Azure ポータルの [課金] か [Azure Billing Portal](https://account.windowsazure.com/Subscriptions)で、Azure の課金内容の詳細を確認できます。 

![サイド メニューで、[課金] を選択します。](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>データ速度
送信するデータ ボリュームを制限するには、3 つの方法があります。

* 1 日あたりの上限。 既定では、これは 100 GB/日に設定されています。 アプリが上限に達すると、電子メールが届き、その日の終わりまでデータが破棄されます。 これを変更するには、[Data Volume Management (データ ボリューム管理)] ブレードを使用します。
* [サンプリング](app-insights-sampling.md)。 このメカニズムでは、メトリックのひずみを最小に抑えて、サーバーおよびクライアント アプリから送信されるテレメトリの量を減らすことができます。
* スロットルにより、分あたりのデータ速度が制限されます。 上限は、Basic 価格プランでは、5 分間の平均が 1 秒あたり 200 データ ポイント、有料レベルでは、Enterprise プランでは、1 分間の平均が 1 秒あたり 500 データ ポイントになります。 

スロットルでは、次の 3 つのバケットが別々にカウントされます。

* [TrackTrace 呼び出し](app-insights-api-custom-events-metrics.md#track-trace)と[キャプチャしたログ](app-insights-asp-net-trace-logs.md)
* [例外](app-insights-api-custom-events-metrics.md#track-exception)(1 秒あたり 50 ポイントに制限されます)
* その他すべてのテレメトリ (ページ ビュー、セッション、要求、依存関係、メトリック、カスタム イベント、Web テストの結果)

*アプリが 1 秒あたりのレートを超えるとどうなりますか。*

* アプリから送信されるデータ量は分単位で評価されます。 1 分間で平均して 1 秒あたりのレートを超える場合、一部の要求がサーバーから拒否されます。 SDK はデータをバッファー処理し、再送信を試みるので、数分間にわたってデータ量が増加します。 アプリが常にスロットル レートを超えてデータを送信した場合、一部のデータが破棄されます  (ASP.NET、Java、JavaScript SDK はこの方法で再送信を試みますが、その他の SDK は調整されたデータを単に破棄します)。

スロットルが発生した場合、この状況が発生したことを通知する警告が表示されます。

*アプリがどれだけのデータ ポイントを送信しているかを知る方法はありますか。*

* [価格] ブレードを開いて、[データ ボリューム] グラフを表示します。
* または、メトリックス エクスプローラーで、新しいグラフを追加し、 **[データ ポイントの量]** メトリックを選択します。 [グループ化] を有効にし、 **[データの種類]**を選択してグループ化します。

## <a name="to-reduce-your-data-rate"></a>データ レートを削減するには
データ ボリュームを減らすために、以下のことを実行できます。

* 毎日のボリューム上限を減らします。 既定値は 100 GB/日です。
* [サンプリング](app-insights-sampling.md)の使用。 このテクノロジは、メトリックや、検索で関連するアイテム間を移動する機能を損なうことなく、データ レートを削減します。 サーバー アプリケーションで自動的に操作されます。
* [報告できる AJAX 呼び出しの数を制限する](app-insights-javascript.md#detailed-configuration) か、AJAX レポートを無効にします。
* [ApplicationInsights.config を編集](app-insights-configuration-with-applicationinsights-config.md)し、不要なコレクション モジュールを無効にします。 たとえば、パフォーマンス カウンターや依存関係のデータが重要ではないと判断した場合などに検討します。
* テレメトリを分割して、インストルメンテーション キーを分割します。 
* 事前集計メトリック。 TrackMetric への呼び出しをアプリに配置した場合、平均計算と測定のバッチの標準偏差を受け入れるオーバーロードを使用して、トラフィックを減らすことができます。 また、 [事前集計パッケージ](https://www.myget.org/gallery/applicationinsights-sdk-labs)を使用することもできます。 

## <a name="sampling"></a>サンプリング
[サンプリング](app-insights-sampling.md) は、テレメトリがアプリに送信される速度を低下させる一方で、診断検索中に関連イベントを見つける機能を保持すると共に、正しいイベント数を保持する方法です。 

サンプリングは、料金を削減し、月間クォータ内で維持する効果的な方法です。 サンプリング アルゴリズムは、たとえば、検索を使用する場合は、特定の例外に関連する要求を検出できるようにする、テレメトリの関連項目を保持します。 アルゴリズムは、要求レート、例外レート、およびその他のカウントについてメトリックス エクスプ ローラーに正しい値が表示されるように正しいカウントも保持します。

サンプリングの形式にはいくつかあります。

* [アダプティブ サンプリング](app-insights-sampling.md) は ASP.NET SDK の既定値で、アプリが送信するテレメトリの量を自動的に調整します。 Web アプリの SDK で自動的に動作して、ネットワーク上のテレメトリのトラフィックを削減します。 
* *インジェスト サンプリング* " は、アプリからのテレメトリが Application Insights サービスに入る時点で動作します。 アプリから送信されるテレメトリの量には影響しませんが、サービスによって保持される量が削減されます。 これを使用すると、ブラウザーや他の SDK からのテレメトリによって使用されるクォータを削減できます。

インジェスト サンプリングを設定するには、[価格] ブレードでコントロールを設定します。

![クォータと価格のブレードから、[サンプル] タイルをクリックして、サンプリングの割合を選択します。](./media/app-insights-pricing/04.png)

> [!WARNING]
> [サンプル保持の割合] タイルに表示される値は、インジェスト サンプリングに設定した値のみを示します。 アプリの SDK で動作するサンプリング レートは反映されません。 
> 
> 受信テレメトリが既に SDK でサンプリングされている場合、インジェスト サンプリングは適用されません。
> 
> 

適用されている場所に関係なく、実際のサンプリング レートを検出するには、次のように [Analytics クエリ](app-insights-analytics.md) を使用します。

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

保持されている各レコードで、`itemCount` は、それが表す元のレコードの数 (1 + 以前に破棄されたレコードの数と同じ) を示します。 


## <a name="transition-from-the-old-pricing-tiers"></a>旧価格レベルからの切り替え

既存のアプリケーションについては、2017 年 2 月まで、旧価格レベルを引き続き使用できます。 この期限までに、多くのアプリケーションは自動的に Basic プランへと移行されます。 連続エクスポートや OMS Log Analytics のコネクタを使用しているアプリケーションは、Enterprise プランに移行されます。


## <a name="limits-summary"></a>制限の概要
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>次のステップ

* [サンプリング](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/




<!--HONumber=Dec16_HO3-->


