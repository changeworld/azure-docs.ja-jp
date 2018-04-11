---
title: Azure Application Insights の価格とデータ ボリュームの管理 | Microsoft Docs
description: Application Insights でテレメトリの量を管理し、コストを監視します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Application Insights での価格とデータ ボリュームの管理

[Azure Application Insights][start] の価格は、アプリケーションごとのデータ量に基づきます。 Application Insights の各リソースは個々のサービスとして課金され、Azure のサブスクリプションの課金内容に加えられます。

2 つの価格プランがあります。 既定のプランは Basic であり、取り込んだデータのボリュームでは、基本的には追加のコストや請求なしで Enterprise プラン プランに相当する機能をすべて含みます。 Operations Management Suite を使用している場合は、日次でのデータ許容量に沿ってノード単位で課金され、含まれる許容量を超える取り込みデータに対してさらに課金される Enterprise プランを選択できます。

Application Insights の課金の仕組みについてご質問があれば、[フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)に気軽に投稿してください。

## <a name="the-price-plans"></a>価格プラン

お客様の通貨および地域での現在の料金については、[Application Insights の価格ページ][pricing]をご覧ください。

### <a name="basic-plan"></a>Basic プラン

Basic プランは、新しい Application Insights リソースが作成されるときの既定のプランであり、Operations Management Suite サブスクリプションをお持ちのお客様を除く、すべてのお客様に最適なプランです。

* Basic プランでは、データ量、つまり Application Insights で受信したテレメトリのバイト数に応じて課金されます。 データ量は、Application Insights がアプリケーションから受信した圧縮されていない JSON データ パッケージのサイズとして測定されます。
[Analytics にインポートされた表形式データ](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)では、データ ボリュームは、Application Insights に送信されたファイルの非圧縮サイズとして測定されます。
* [ライブ メトリック ストリーム](app-insights-live-stream.md) データは、課金対象としてカウントされません。
* 2018 年 4 月の時点で、Basic プランでは、[連続エクスポート](app-insights-export-telemetry.md)と [Log Analytics コネクタ](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409)を追加料金なしでご利用いただけます。

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Enterprise プランと Operations Management Suite のサブスクリプションの権利

[以前の発表](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)のとおり、Microsoft Operations Management Suite E1 および E2 を購入したお客様は、追加コストなしで追加コンポーネントとして Application Insights Enterprise を取得できます。 具体的には、Operations Management Suite E1 および E2 の各ユニットには、Application Insights の Enterprise プラン 1 ノード分の権利が含まれています。 後で詳しく説明するように、Application Insights の各ノードは、追加コストなしで、1 日あたり最大 200 MB のデータを取り込み (Log Analytics のデータ インジェストを除く)、90 日間保持できます。 これは Operations Management Suite サブスクリプションをお持ちのお客様だけに適用されるので、サブスクリプションをお持ちでないお客様にはこのプランを選ぶオプションは表示されません。

> [!NOTE]
> この権利を取得するには、Enterprise 価格プランで Application Insights リソースを取得する必要があります。 この権利は、ノードとしてのみ適用されるため、Basic プランの Application Insights リソースはこの利点を受けられません。 この権利は、*[使用量と推定コスト]* ページに表示される見積もりコストには表示されません。 また、2018年 4 月の新しい Azure 監視価格モデルにサブスクリプションを移行する場合、使用可能なプランは Basic プランだけなので、Operations Management Suite サブスクリプションをお持ちの場合はお勧めしません。

Enterprise プランについて詳しくは、[Enterprise プランの価格の詳細に関するページ](app-insights-pricing-enterprise-details.md)をご覧ください。

### <a name="multi-step-web-tests"></a>複数手順の Web テスト

[複数ステップ Web テスト](app-insights-monitor-web-app-availability.md#multi-step-web-tests)に対しては、追加料金が発生します。 これは、一連のアクションを実行する Web テストのことです。

単一ページの ping テストについては、個別の料金はかかりません。 Ping テストと複数ステップ テストの両方からのテレメトリについては、アプリの他のテレメトリと一緒に課金されます。

## <a name="review-pricing-plans-and-estimate-costs"></a>価格プランを確認してコストを見積もる

Application Insights では、利用可能な価格プランと最近の使用パターンに基づくコストを簡単に理解できるようになっています。 Azure Portal で Application Insights リソースの **[使用量と推定コスト]** ページを開きます。

![[Pricing (価格設定)] を選択します。](./media/app-insights-pricing/pricing-001.png)

**a.** 該当の月のデータ量を確認します。 これには、(サーバーおよびクライアント アプリからの、また可用性テストからの[サンプリング](app-insights-sampling.md)の後) 受信され保持されたすべてのデータが含まれます。

**b.** [複数ステップ Web テスト](app-insights-monitor-web-app-availability.md#multi-step-web-tests)で別料金が発生しています。 (これには、データ ボリューム料金に含まれるシンプルな可用性テストは含まれません。)

**c.** 過去 1 か月のデータ ボリュームの傾向を表示します。

**d.** データ インジェストの[サンプリング](app-insights-sampling.md)を有効にします。 

**e.** 1 日のデータ ボリュームの上限を構成します。

Application Insights の課金は Azure の課金内容に加えられます。 Azure ポータルの [課金] か [Azure Billing Portal](https://account.windowsazure.com/Subscriptions)で、Azure の課金内容の詳細を確認できます。 

![サイド メニューで、[課金] を選択します。](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>データ速度
送信するデータ ボリュームを制限するには、3 つの方法があります。

* **サンプリング:** このメカニズムを使用すると、メトリックのひずみを最小に抑えて、サーバーおよびクライアント アプリから送信されるテレメトリの量を減らすことができます。 これは、データの量を調整するための主要なツールです。 [サンプリング機能の詳細については、こちらを参照してください](app-insights-sampling.md)。 
* **1 日あたりの上限:** Azure Portal から Application Insights リソースを作成する場合は、100 GB/日に設定されます。 Visual Studio から Application Insights リソースを作成する場合の既定値は小 (32.3 MB/日) です。これはテストを行うことだけを想定したものです。 このケースでは、アプリを実稼働環境にデプロイする前に、ユーザーが日次上限を上げることになります。 高トラフィック アプリケーション用に最大値の引き上げを要求した場合を除き、最大の上限は 1000 GB/日です。 日次上限を設定するときには、**決して日次上限に到達することがないように**注意してください。到達すると、その日のそれ以降のデータが失われてしまい、アプリケーションを監視できなくなるためです。 これを変更するには、[使用量と推定コスト] ページからリンクされている [日次ボリューム上限] オプションを使います (後述参照)。 Application Insights には使われないクレジットがある一部のサブスクリプションの種類の制限を削除しました。 これまでは、サブスクリプションに使用制限がある場合は、[日次上限] ダイアログに、使用制限を解除して 32.3 MB/日から引き上げる方法が表示されます。
* **スロットル:** これにより、データ速度は 1 分間で平均して 1 秒あたり 32,000 イベントに制限されます。

*アプリがスロットル レートを超えるとどうなりますか。*

* アプリから送信されるデータ量は分単位で評価されます。 1 分間で平均して 1 秒あたりのレートを超える場合、一部の要求がサーバーから拒否されます。 SDK はデータをバッファー処理し、再送信を試みるので、数分間にわたってデータ量が増加します。 アプリが常にスロットル レートを超えてデータを送信した場合、一部のデータが破棄されます  (ASP.NET、Java、JavaScript SDK はこの方法で再送信を試みますが、その他の SDK は調整されたデータを単に破棄します)。スロットルが発生した場合、この状況が発生したことを通知する警告が表示されます。

*アプリがどれだけのデータを送信しているかを知る方法はありますか。*

* **[使用量と推定コスト]** ページを開き、毎日のデータ ボリュームのグラフを確認します。 
* または、メトリックス エクスプローラーで、新しいグラフを追加し、 **[データ ポイントの量]** メトリックを選択します。 [グループ化] を有効にし、 **[データの種類]**を選択してグループ化します。

## <a name="to-reduce-your-data-rate"></a>データ レートを削減するには
データ ボリュームを減らすために、以下のことを実行できます。

* [サンプリング](app-insights-sampling.md)の使用。 このテクノロジは、メトリックや、検索で関連するアイテム間を移動する機能を損なうことなく、データ レートを削減します。 サーバー アプリケーションで自動的に操作されます。
* [報告できる AJAX 呼び出しの数を制限する](app-insights-javascript.md#detailed-configuration) か、AJAX レポートを無効にします。
* [ApplicationInsights.config を編集](app-insights-configuration-with-applicationinsights-config.md)し、不要なコレクション モジュールを無効にします。たとえば、パフォーマンス カウンターや依存関係のデータが重要ではないと判断した場合などに検討します。
* テレメトリを分割して、インストルメンテーション キーを分割します。 
* 事前集計メトリック。 TrackMetric への呼び出しをアプリに配置した場合、平均計算と測定のバッチの標準偏差を受け入れるオーバーロードを使用して、トラフィックを減らすことができます。 また、 [事前集計パッケージ](https://www.myget.org/gallery/applicationinsights-sdk-labs)を使用することもできます。

## <a name="managing-the-maximum-daily-data-volume"></a>最大日次データ ボリュームの管理

日次ボリューム上限を使用して、収集されるデータを制限できますが、上限に達した場合、アプリケーションから送信されるその日の残りのすべてのテレメトリが失われます。 アプリケーションが日次上限に達すると、その後のアプリケーションの正常性とパフォーマンスを追跡できなくなるため、上限に達しないようにすることを**お勧めします**。

代わりに、[サンプリング](app-insights-sampling.md) を使用してデータ ボリュームを調整し、日次上限は、アプリケーションが予想以上に大量のテレメトリを送信し始めた場合の "最後の手段" としてのみ使用するようにします。

日次上限を変更するには、Application Insights リソースの [構成] セクションで、**[使用量と推定コスト]** ページから **[日次上限]** をクリックします。

![テレメトリの日次ボリューム上限の調整](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>サンプリング
[サンプリング](app-insights-sampling.md) は、テレメトリがアプリに送信される速度を低下させる一方で、診断検索中に関連イベントを見つける機能を保持すると共に、正しいイベント数を保持する方法です。

サンプリングは、料金を削減し、月間クォータ内で維持する効果的な方法です。 サンプリング アルゴリズムは、たとえば、検索を使用する場合は、特定の例外に関連する要求を検出できるようにする、テレメトリの関連項目を保持します。 アルゴリズムは、要求レート、例外レート、およびその他のカウントについてメトリックス エクスプ ローラーに正しい値が表示されるように正しいカウントも保持します。

サンプリングの形式にはいくつかあります。

* [アダプティブ サンプリング](app-insights-sampling.md) は ASP.NET SDK の既定値で、アプリが送信するテレメトリの量を自動的に調整します。 Web アプリの SDK で自動的に動作して、ネットワーク上のテレメトリのトラフィックを削減します。 
* *インジェスト サンプリング* " は、アプリからのテレメトリが Application Insights サービスに入る時点で動作します。 アプリから送信されるテレメトリの量には影響しませんが、サービスによって保持される量が削減されます。 これを使用すると、ブラウザーや他の SDK からのテレメトリによって使用されるクォータを削減できます。

インジェスト サンプリングを設定するには、[価格] ダイアログでコントロールを設定します。

![クォータと価格のダイアログから、[サンプル] タイルをクリックして、サンプリングの割合を選択します。](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> [データのサンプリング] ダイアログでは、インジェストのサンプリングの値を制御するだけです。 アプリで Application Insights SDK によって適用されているサンプリング レートは反映されません。 受信テレメトリが既に SDK でサンプリングされている場合、インジェスト サンプリングは適用されません。
>

適用されている場所に関係なく、実際のサンプリング レートを検出するには、次のように [Analytics クエリ](app-insights-analytics.md) を使用します。

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

保持されている各レコードで、 `itemCount` は、それが表す元のレコードの数 (1 + 以前に破棄されたレコードの数と同じ) を示します。 

## <a name="automation"></a>Automation

Azure Resource 管理を使用して、価格プランを設定するスクリプトを記述することができます。 方法については、[こちら](app-insights-powershell.md#price)をご覧ください。

## <a name="limits-summary"></a>制限の概要

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>次の手順

* [サンプリング](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
