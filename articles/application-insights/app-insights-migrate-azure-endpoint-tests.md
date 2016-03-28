<properties 
	pageTitle="Azure エンドポイント テストから Application Insights 可用性テストへの移行" 
	description="従来の Azure エンドポイント監視テストを、Application Insights 可用性テストに移行しました。2016 年 4 月 4 日に新旧テストを切り替える予定です。"
	services="application-insights" 
    documentationCenter=""
	authors="soubhagyadash" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2016" 
	ms.author="awills"/>
 
# Azure エンドポイント監視から Application Insights 可用性テストへの移行

Azure Web アプリに対する[エンドポイント監視](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/)をご利用の皆様にお知らせです。 2016 年 4 月 4 日より、エンドポイント監視に代わって、より強力な新機能である[可用性テスト](app-insights-monitor-web-app-availability.md)を導入します。この新しいテストは既に作成されていますが、4 月 4 日までは無効になっています。

新しいテストはユーザーによる編集が可能で、必要に応じて切り替えることができます。[Azure ポータル](https://portal.azure.com)の Default-ApplicationInsights-CentralUS リソース グループに配置されています。


## 可用性テストとは

可用性テストは、最大で世界中の 16 の場所から HTTP 要求 (単一の ping テストまたは Visual Studio Web テスト) を送信し、任意の Web サイトまたはサービスが稼動しているかどうかを継続的にチェックする Azure の機能です。

[従来の Azure ポータル](https://manage.windowsazure.com)では、これらのテストがエンドポイント監視と呼ばれていました。しかし、そのスコープは限定的でした。新しい可用性テストは、次のように性能が大幅に向上しています。

* Application Insights リソースごとに最大 10 個の Visual Studio Web テストまたは ping テスト。 
* 世界中で最大 16 の場所から、対象の Web アプリに対してテスト要求を送信。テスト成功基準の制御機能が向上。 
* Azure Web アプリだけでなく、あらゆる Web サイトまたはサービスのテストを実行可能。
* テスト再試行: 一過性のネットワーク障害による誤検知のアラートを低減。 
* Webhook でアラート用 HTTP POST 通知を受信可能。

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

[可用性テストについては、ここに](app-insights-monitor-web-app-availability.md)詳しい説明があります。

可用性テストは、Web アプリケーションに対する拡張可能な解析サービスである [Visual Studio Application Insights](app-insights-overview.md) の一部です。



## これまでのエンドポイント テストに対する影響

* お客様が今までに作成されたエンドポイント監視テストは、新しい Application Insights 可用性テストに複製しました。この複製は 2016 年 3 月 4 日に実施しましたので、それ以降にお客様が作成されたエンドポイント テストは複製していません。
* 新しい可用性テストはまだ有効になっておらず、現在も古いエンドポイント テストが実行されています。
* アラート生成規則は移行して*いません*。新しいテストには最初から、次のような既定の規則が設定されています。
 * 5 分以内に複数の場所でレポートのエラーが発生した場合にトリガーする。
 * サブスクリプション管理者に電子メールを送信する。

[Azure ポータル](https://portal.azure.com)の "Default-ApplicationInsights-CentralUS" リソース グループ内に、移行済みテストがあります。これらのテスト名は、"Migrated-" (移行済み) で始まります。

## 何をする必要がありますか?

* 2016 年 3 月 4 日以降に従来形式のテストを作成した場合 (または何らかの理由でお客様のテストが移行されていない場合) は、新しい可用性テストを[簡単に設定](app-insights-monitor-web-app-availability.md)できます。

### オプション A: 何もしない。Microsoft に任せる。

**4 月 4 日に** Microsoft は以下を実施します。

* 古いエンドポイント テストを無効にする。
* 移行済みの可用性テストを有効にする。

### オプション B: お客様が新しいテストの管理や有効化を行う。

* 新しい [Azure ポータル](https://portal.azure.com)で、新機能の可用性テストを確認し、編集します。 
 * トリガー条件を確認
 * 電子メール受信者を確認
* 新しいテストを有効にします。
* [クラシック ポータルで](https://manage.windowsazure.com)古いエンドポイント テストを削除します。アラートの重複を避け、Web サイトに対するテスト トラフィックの負荷を軽くするために、この手順の実行をお勧めします。お客様が実行しない場合は、2016 年 4 月 4 日に Microsoft が削除します。


### オプション C: 利用の停止

可用性テストの利用を希望しない場合は、[Azure ポータル](https://portal.azure.com)で削除できます。通知メールの下部にも登録解除リンクがあります。

いずれにせよ、従来のエンドポイント テストは 4 月 4 日に削除されます。

## 新しいテストの編集方法

[Azure ポータル](https://portal.azure.com)にサインインして、名前が "Migrated-" (移行済み) で始まる Web テストを探します。

![Choose Resource Groups, Default-ApplicationInsights-CentralUS, and open the 'migrated' tests.](./media/app-insights-migrate-azure-endpoint-tests/20.png)

テストの編集や有効化を行います。

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)


## パフォーマンスが低下しているのはなぜですか。

サービスを向上させるためです。従来のエンドポイント サービスは対象範囲がごく限られていました。Azure VM または Web アプリで、2 つの URL に対して 3 地点から簡単な ping テストを実施できるだけでした。新しいテストでは、最大 16 の場所から複数手順の Web テストを実行でき、1 つのアプリケーションに 10 個までのテストを指定できます。任意の URL をテストすることができ、Azure サイトである必要はありません。

新しいテストは、現在テストを実施している Web アプリまたは VM とは別に構成されます。

引き続きお客様が新しいポータルからテストを制御できるよう、テストの移行を実施します。

## Application Insights とは何か?

新しい可用性テストは、[Visual Studio Application Insights](app-insights-overview.md) の一部です。[2 分間のビデオ](http://go.microsoft.com/fwlink/?LinkID=733921)をご覧ください。

## 新しいテストにかかる料金

移行されたテストは、既定の Free プランで Application Insights リソースに設定されます。このプランでは、最大 500 万データ ポイントの収集が可能です。新しいテストで使用するデータ量に問題なく対応できます。

もちろん、Application Insights がお気に召して、より多くの可用性テストを作成したり、利用するパフォーマンス監視と診断の機能を増やしたりすると、生成されるデータ ポイントも増加します。しかし、それで Free プランの割当分を使い果たしても、何も問題はありません。Standard プランまたは Premium プランに登録するまで、料金は請求されません。

[Application Insights の価格設定とクォータ監視について、ご説明します](app-insights-pricing.md)。

## 移行対象と移行対象外の要素

以下は古いエンドポイント テストから保持されます。

* テストされるエンドポイント URL。
* 要求の送信元となる地域。
* テスト間隔は 5 分のまま。
* テストのタイムアウトは 30 秒のまま。 

移行対象外の要素を以下に示します。

* アラートのトリガー規則。1 つの場所でレポートが 5 分間停止するとトリガーする規則を設定しました。
* アラートの受信者。通知メールは、サブスクリプションの所有者と共同所有者に送信されます。 

## 新しいテストを見つける方法

必要なときに、新しいテストを編集することができます。[Azure ポータル](https://portal.azure.com)にサインインして、**[リソース グループ]** を開き、**[Default-ApplicationInsights-CentralUS]** を選択します。このグループで、新しい Web テストを探します。[新しい可用性テストについてご説明します](app-insights-monitor-web-app-availability.md)。

新しい電子メール アラートは次のアドレスから送信されますので、注意してください: App Insights アラート (ai-noreply@microsoft.com)

## 何も対応しなかった場合に行われる処理

オプション A が適用されます。移行されたテストが有効になり、上記の既定のアラート規則が設定されます。前述したように、必要なカスタム アラート規則を追加し、受信者を設定してください。従来のエンドポイント監視テストは無効になります。

## この件に関するフィードバックの宛先 

お客様からのご意見をお待ちしております。[こちらまでメールを](mailto:vsai@microsoft.com)お寄せください。

<!---HONumber=AcomDC_0316_2016-->