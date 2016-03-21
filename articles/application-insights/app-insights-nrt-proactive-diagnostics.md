<properties 
	pageTitle="Application Insights のほぼリアルタイムのプロアクティブ診断" 
	description="NRT のプロアクティブ診断では、サーバーの応答時間が異常な動作を示している場合に自動的にユーザーに通知します。構成は必要ありません。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="awills"/>
 
# ほぼリアルタイムのプロアクティブ診断

*この機能は初期試用版です。*

[ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com) *にフィードバックをお寄せください*

[Visual Studio Application Insights](app-insights-overview.md) では、Web アプリの要求失敗率が大幅に増加すると、ほぼリアルタイムで自動的にユーザーに通知します。通知には、問題のトリアージと診断に役立つよう、失敗した要求の特性および関連するテレメトリの分析結果が記載されています。また、より詳しい診断を行うために、Application Insights ポータルへのリンクも含まれています。この機能は、機械学習アルゴリズムを使用してベースラインとなる通常の失敗率を予測するため、セットアップや構成は不要です。これを機能させるには、最小量のトラフィックがある程度必要です。

アラートの例を次に示します。

![Sample Intelligent Alert showing cluster analysis around failure](./media/app-insights-nrt-proactive-diagnostics/010.png)

失敗のアラートを取得するには、[ASP.NET 向け](app-insights-asp-net.md)または [Java Web アプリ向け](app-insights-java-get-started.md)に Application Insights を設定する必要があります。

## 動作のしくみ

ほぼリアルタイムのプロアクティブ診断では、アプリから受け取ったテレメトリ (特に要求失敗率) を監視します。このメトリックは、通常、400 番以上の応答コードを返した HTTP 要求の数を示します (ただし、[フィルター処理](app-insights-api-filtering-sampling.md#filtering)するか、独自の [TrackRequest](app-insights-api-custom-events-metrics.md#track-request) 呼び出しを生成するカスタム コードを記述した場合を除きます)。

サービスは、このメトリックの以前の動作に基づいて、予想される値の範囲を予測します。実際の値が大幅に高い場合は、アラートを生成します。

アラートが発生すると、要求の複数のディメンションに対してクラスター分析を実行して、失敗の特徴的な値のパターンの検出を試みます。上記の例では、ほとんどが特定の要求名に関する失敗ですが、ホスト インスタンスおよびサーバー インスタンスとは関係ないことが分析によって検出されています。

また、特定されたクラスター内の要求に関連する例外と依存関係のエラーに加え、それらの要求に関連するすべてのトレース ログの例も検出されています。

分析結果は、(送信しないように構成している場合を除き) 電子メールで送信されます。

[手動で設定したアラート](app-insights-alerts.md)と同じように、Application Insights リソースの [アラート] ブレードでアラートの状態を検査し、構成できます。ただし、その他のアラートとは異なり、アダプティブ失敗アラートを設定したり、構成したりする必要はありません。必要に応じて、無効にすることや、送信先の電子メール アドレスを変更することもできます。

## アラートを受け取った場合

アラートは、要求失敗率の異常が検出されたことを示します。アプリやその環境に何らかの問題があることが考えられます。アップロードした新しいバージョンが正常に動作していない可能性や、データベースや外部リソースなどの依存関係に異常がある可能性があります。

要求の割合と影響を受けるユーザーの数から、問題の緊急度を判断できます。

多くの場合は、要求名、例外、依存関係などの提供されたデータからすばやく問題を診断することができます。

さらに詳しく調査する必要がある場合は、関連する要求、例外、依存関係、またはトレースでフィルター処理された[検索ページ](app-insights-diagnostic-search.md)に各セクションのリンクから直接移動できます。また、[Azure ポータル](https://portal.azure.com)を開き、アプリの Application Insights リソースに移動して、[失敗] ブレードを開くという方法もあります。

## 最新のアラートを確認する

ポータルでアラートを確認するには、**[設定] の [操作イベント]** を開きます。

![Alerts summary](./media/app-insights-nrt-proactive-diagnostics/040.png)

任意のアラートをクリックすると、その詳細情報がすべて表示されます。


## アラートを構成する 

> **構成の UX はまだ利用できません。**
> 
> 代わりに、[ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com) に電子メールでフィードバックをお寄せください。
>
> 次のように機能する予定です。

[アラート] ページを開きます。アダプティブ失敗アラートは、手動で設定したすべてのアラートと共に含まれており、現在アラート状態にあるかどうかを確認できます。

![On the Overview page, click Alerts tile.Or on any Metrics page, click Alerts button.](./media/app-insights-nrt-proactive-diagnostics/020.png)

アラートをクリックして構成します。

![構成](./media/app-insights-nrt-proactive-diagnostics/030.png)

アダプティブ失敗アラートは無効にできますが、削除 (および作成) することはできません。


## 違い...

NRT のプロアクティブ診断は、Application Insights の類似しているが異なるその他の機能を補完します。

* [メトリック アラート](app-insights-alerts.md)は、ユーザーが設定するアラートであり、CPU の占有率、要求率、ページの読み込み時間など、広範なメトリックを監視できます。このアラートを使用すると、リソースを追加する必要がある場合などに自分に警告することができます。これに対し、NRT のプロアクティブ診断は、重要な少数のメトリック (現在は要求失敗率のみ) を対象としており、Web アプリの要求失敗率が Web アプリの通常の動作と比較して大幅に増加すると、ほぼリアルタイムでユーザーに通知するよう設計されています。
* また、[プロアクティブな検出](app-insights-proactive-detection.md)では、マシン インテリジェンスを使用して、メトリックの異常なパターンを検出します。ユーザーが構成する必要はありません。ただし、NRT のプロアクティブ診断とは異なり、プロアクティブな検出の目的は、(たとえば、特定の種類のブラウザーの特定のページで) 適切に処理されない可能性のあるさまざまな使用量のセグメントを検出することです。分析は毎日実行され、見つかった結果はアラートよりも緊急度が大幅に低い可能性があります。これに対し、NRT のプロアクティブ診断の分析は、受け取ったテレメトリに対して継続的に実行され、サーバーのエラー率が予想を超えると数分以内にユーザーに通知します。


## フィードバックをお寄せください

*この機能は初期試用版です。ぜひフィードバックをお寄せください。フィードバックは、*[ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com) *宛てに送信するか、アラート メッセージのフィードバック リンクをクリックしてお寄せください。*

<!---HONumber=AcomDC_0218_2016-->