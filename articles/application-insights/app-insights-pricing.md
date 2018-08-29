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
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 08/11/2018
ms.author: mbullwin
ms.openlocfilehash: 221d0d8a78a46cad9f1994e9313b21e2867d9563
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42141373"
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Application Insights での価格とデータ ボリュームの管理

> [!NOTE]
> この記事では、Application Insights でデータの使用状況を分析する方法について説明します。  関連する情報については、次の記事を参照してください。
> - 「[使用量と推定コストの監視](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md)」では、Azure の異なる価格モデルの複数の監視機能全体の使用量と推定コストを表示する方法について説明します。 価格モデルを変更する方法についても説明します。

[Azure Application Insights][start] の価格は、アプリケーションごとのデータ量に基づきます。 Application Insights の各リソースは個々のサービスとして課金され、Azure サブスクリプションの課金内容に加えられます。

Application Insights には、Basic と Enterprise の 2 つの価格プランがあります。 Basic 価格プランは既定のプランです。 これには、Enterprise プランのすべての機能が追加コストなしで含まれます。 Basic プランでは基本的に、取り込まれるデータの量に基づいて請求されます。 

Enterprise プランはノードあたりの料金で、各ノードに 1 日あたりの無料データ利用分があります。 Enterprise 価格プランでは、含まれる無料利用分を超えて取り込まれたデータに対して課金されます。 Operations Management Suite を使用する場合は、Enterprise プランを選択する必要があります。 

Application Insights の課金のしくみについてご質問がある場合は、[フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)に投稿してください。

## <a name="pricing-plans"></a>価格プラン

お客様の通貨およびリージョンでの現在の価格については、「[Application Insights の価格][pricing]」をご覧ください。

> [!NOTE]
> 2018 年 4 月に、Azure Monitoring 用の新しい価格モデルを[導入](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)しました。 このモデルでは、監視サービスのポートフォリオ全体で単純な "従量課金制" モデルを採用しています。 [新しい価格モデル](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)の詳細、使用パターンに基づいて[このモデルへの移行の影響を評価](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model)する方法、[新しいモデルを有効にする方法](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)をご確認ください。

### <a name="basic-plan"></a>Basic プラン

Basic プランは、新しい Application Insights リソースを作成する場合の既定の価格プランです。 Basic プランは、Operations Management Suite サブスクリプションをご利用のお客様を除くすべてのお客様に最適です。

* Basic プランでは、データ ボリュームによって課金されます。 データ ボリュームとは、Application Insights で受信したテレメトリのバイト数です。 データ ボリュームは、Application Insights がアプリケーションから受信した圧縮されていない JSON データ パッケージのサイズとして測定されます。 [Analytics にインポートされた表形式データ](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)では、データ ボリュームは、Application Insights に送信されたファイルの非圧縮サイズとして測定されます。
* アプリケーションのデータ ボリューム料金は、2018 年 4 月から**データ インジェスト**という新しい課金メーターで報告されるようになりました。 この新しいメーターは Applications Insights や Log Analytics などの監視テクノロジで共有され、現在は **App Services** というサービス名の下に表示されます (間もなく **Log Analytics** に変更されます)。 
* [ライブ メトリック ストリーム](app-insights-live-stream.md) データは、課金対象としてカウントされません。
* 2018 年 4 月の時点で、Basic プランでは、[連続エクスポート](app-insights-export-telemetry.md)と [Azure Log Analytics コネクタ](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409)を追加料金なしでご利用いただけます。

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Enterprise プランと Operations Management Suite のサブスクリプションの権利

[以前の発表](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)のとおり、Operations Management Suite E1 および E2 を購入したお客様は、追加コストなしで追加コンポーネントとして Application Insights Enterprise を取得できます。 具体的には、Operations Management Suite E1 および E2 の各ユニットには、Application Insights Enterprise プラン 1 ノード分の権利が含まれています。 Application Insights の各ノードは、追加コストなしで、1 日あたり最大 200 MB のデータを取り込み (Log Analytics のデータ インジェストを除く)、90 日間保持できます。 プランについては、この記事で後ほど詳しく説明します。 

このプランは Operations Management Suite サブスクリプションをお持ちのお客様だけに適用されるので、Operations Management Suite サブスクリプションをお持ちでないお客様にはこのプランを選ぶオプションは表示されません。

> [!NOTE]
> この権利を取得するには、Application Insights リソースが Enterprise 価格プランである必要があります。 この権利は、ノードとしてのみ適用されます。 Basic プランの Application Insights リソースでは、メリットは実現されません。 この権利は、**[使用量と推定コスト]** ウィンドウに表示される見積もりコストには表示されません。 また、サブスクリプションを 2018年 4 月の新しい Azure Monitoring 価格モデルに移行した場合、使用可能なプランは Basic プランだけです。 Operations Management Suite サブスクリプションをお持ちの場合、新しい Azure Monitoring 価格モデルへのサブスクリプションの移行はお勧めしません。

Enterprise プランについて詳しくは、「[Enterprise プランの詳細](app-insights-pricing-enterprise-details.md)」をご覧ください。

### <a name="multi-step-web-tests"></a>複数手順の Web テスト

[複数ステップ Web テスト](app-insights-monitor-web-app-availability.md#multi-step-web-tests)に対しては、追加料金が発生します。 複数ステップ Web テストは、一連のアクションを実行する Web テストです。

単一ページの "*ping テスト*" については、個別の料金はかかりません。 Ping テストと複数ステップ テストからのテレメトリについては、アプリの他のテレメトリと同じ料金が請求されます。

## <a name="review-pricing-plans-and-estimate-costs"></a>価格プランを確認してコストを見積もる

Application Insights では、利用可能な価格プランと最近の使用パターンに基づくコストを簡単に理解できるようになっています。 作業を開始するには、Azure Portal で Application Insights リソースの **[使用量と推定コスト]** ウィンドウに移動します。

![価格の選択](./media/app-insights-pricing/pricing-001.png)

A. 該当の月のデータ量を確認します。 これには、サーバーおよびクライアント アプリから、また可用性テストから ([サンプリング](app-insights-sampling.md)の後に) 受信され保持されたすべてのデータが含まれます。  
B. [複数ステップ Web テスト](app-insights-monitor-web-app-availability.md#multi-step-web-tests)で別料金が発生しています。 (これには、データ ボリューム料金に含まれるシンプルな可用性テストは含まれません。)  
C. 過去 1 か月のデータ ボリュームの傾向を表示します。  
D. データ インジェストの[サンプリング](app-insights-sampling.md)を有効にします。   
E. 1 日のデータ ボリュームの上限を設定します。  

Application Insights の課金は Azure の課金内容に加えられます。 Azure Portal の **[課金]** セクションか [Azure Billing Portal](https://account.windowsazure.com/Subscriptions) で、Azure の課金内容の詳細を確認できます。 

![左側のメニューで [課金] を選択します](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>データ速度
送信するデータの量は次の 3 つの方法で制限されます。

* **サンプリング:** サンプリングを使用すると、メトリックのひずみを最小に抑えて、サーバーおよびクライアント アプリから送信されるテレメトリの量を減らすことができます。 サンプリングは、送信するデータの量を調整するために使用できる主要なツールです。 [サンプリング機能の詳細については、こちらを参照してください](app-insights-sampling.md)。 
* **日次上限**: Azure Portal で Application Insights リソースを作成する場合、日次上限は 100 GB/日に設定されます。 Visual Studio から Application Insights リソースを作成する場合の既定値は小 (32.3 MB/日) です。 日次上限の既定値は、テストを容易にするために設定されます。 アプリを実稼働環境にデプロイする前に、ユーザーが日次上限を上げることになります。 

    高トラフィック アプリケーション用に最大値の引き上げを要求する場合を除き、最大の上限は 1,000 GB/日です。 

    日次上限を設定する場合はご注意ください。 意図は、"*日次上限に達しない*" ようにすることです。 日次上限に達した場合、その日の残りの時間についてデータが失われ、アプリケーションを監視することはできません。 日次上限を変更するには、**[日次ボリューム上限]** オプションを使用します。 このオプションには、**[使用量と推定コスト]** ウィンドウでアクセスできます (これについてはこの記事で後ほど詳しく説明します)。
    Application Insights には使用できなかったクレジットがある一部のサブスクリプションの種類の制限を除去しました。 これまでは、サブスクリプションに使用制限がある場合は、[日次上限] ダイアログに、使用制限を解除して日次上限を 32.3 MB/日から引き上げる方法が表示されました。
* **スロットル:** スロットルにより、データ速度は、インストルメンテーション キーごとに 1 分間で平均して 1 秒あたり 32,000 イベントに制限されます。

*アプリがスロットル レートを超えるとどうなりますか。*

* アプリから送信されるデータ量は分単位で評価されます。 1 分間で平均して 1 秒あたりのレートを超える場合、一部の要求がサーバーから拒否されます。 SDK はデータをバッファー処理し、その再送信を試みます。 急激な増加を数分間に分散させます。 アプリが常にスロットル レートを超えてデータを送信した場合、一部のデータが破棄されます  (ASP.NET、Java、JavaScript SDK はこの方法でデータの再送信を試みますが、その他の SDK は調整されたデータを単に破棄します)。スロットルが発生した場合、この状況が発生したことを通知する警告が表示されます。

*アプリがどれだけのデータを送信しているかを知る方法はありますか。*

次のオプションのいずれかを使用して、アプリで送信されるデータの量を表示できます。

* **[使用量と推定コスト]** ウィンドウに移動し、毎日のデータ ボリュームのグラフを確認します。 
* メトリックス エクスプローラーで、新しいグラフを追加します。 グラフ メトリックについては、**[データ ポイントの量]** を選択します。 **[グループ化]** を有効にし、**[データの種類]** を選択してグループ化します。

## <a name="reduce-your-data-rate"></a>データ速度を低下させる
データ ボリュームを減らすために、以下のことを実行できます。

* [サンプリング](app-insights-sampling.md)の使用。 このテクノロジは、メトリックがゆがめずにデータ速度を低下させます。 Search では、関連項目間を移動する機能は失われません。 サーバー アプリケーションでは、サンプリングは自動的に動作します。
* [報告できる AJAX 呼び出しの数を制限する](app-insights-javascript.md#detailed-configuration) か、AJAX レポートを無効にします。
* [ApplicationInsights.config を編集](app-insights-configuration-with-applicationinsights-config.md)し、不要なコレクション モジュールを無効にします。 たとえば、パフォーマンス カウンターや依存関係のデータが重要ではないと判断した場合などに検討します。
* 異なるインストルメンテーション キー間でテレメトリを分割します。 
* 事前集計メトリック。 TrackMetric への呼び出しをアプリに配置した場合、平均計算と測定のバッチの標準偏差を受け入れるオーバーロードを使用して、トラフィックを減らすことができます。 または、[事前集計パッケージ](https://www.myget.org/gallery/applicationinsights-sdk-labs)を使用することもできます。

## <a name="manage-the-maximum-daily-data-volume"></a>最大日次データ ボリュームを管理する

日次ボリューム上限を使用すると、収集されるデータを制限できます。 ただし、上限に達した場合は、その日の残りの時間についてアプリケーションから送信されたすべてのテレメトリが失われます。 アプリケーションが日次上限に達することは "*望ましくありません*"。 日次上限に達した後は、アプリケーションの正常性とパフォーマンスを追跡できません。

日次ボリューム上限を使用する代わりに、[サンプリング](app-insights-sampling.md)を使用して、データ ボリュームを目的のレベルに調整してください。 その後、アプリケーションが予期せず大量のテレメトリの送信を開始した場合に、"最後の手段" としてのみ日次上限を使用します。

日次上限を変更するには、Application Insights リソースの **[構成]** セクションで、**[使用量と推定コスト]** ウィンドウから **[日次上限]** を選択します。

![テレメトリの日次ボリューム上限の調整](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>サンプリング
[サンプリング](app-insights-sampling.md)は、テレメトリがアプリに送信される速度を低下させる一方で、診断検索中に関連イベントを見つける機能を保持する方法です。 適切なイベント カウントも保持されます。

サンプリングは、料金を削減し、月間クォータ内で維持する効果的な方法です。 サンプリング アルゴリズムはテレメトリの関連項目を保持するので、たとえば、Search を使用する場合は、特定の例外に関連する要求を検出できます。 アルゴリズムは、要求レート、例外レート、およびその他のカウントについてメトリックス エクスプローラーに正しい値が表示されるように正しいカウントも保持します。

サンプリングの形式にはいくつかあります。

* [アダプティブ サンプリング](app-insights-sampling.md)は、ASP.NET SDK の既定値です。 アダプティブ サンプリング は、アプリが送信するテレメトリの量を自動的に調整します。 Web アプリの SDK で自動的に動作して、ネットワーク上のテレメトリのトラフィックを削減します。 
* *インジェスト サンプリング* " は、アプリからのテレメトリが Application Insights サービスに入る時点で動作します。 インジェスト サンプリングは、アプリから送信されるテレメトリの量には影響しませんが、サービスによって保持される量が削減されます。 インジェスト サンプリングを使用すると、ブラウザーや他の SDK からのテレメトリによって使用されるクォータを削減できます。

インジェスト サンプリングを設定するには、**[価格]** ウィンドウに移動します。

![クォータと価格のウィンドウで、[サンプル] タイルを選択して、サンプリングの割合を選択する](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> **[データのサンプリング]** ウィンドウでは、インジェスト サンプリングの値だけを制御します。 アプリで Application Insights SDK によって適用されているサンプリング レートは反映されません。 受信テレメトリが既に SDK でサンプリングされている場合、インジェスト サンプリングは適用されません。
>

適用されている場所に関係なく、実際のサンプリング レートを検出するには、[Analytics クエリ](app-insights-analytics.md) を使用します。 クエリは次のようになります。

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

保持されている各レコードで、`itemCount` は、それが表す元のレコードの数を示します。 これは、1 + 以前に破棄されたレコードの数と同じです。 

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