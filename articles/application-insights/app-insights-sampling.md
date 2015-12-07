<properties 
	pageTitle="Application Insights におけるテレメトリ サンプリング" 
	description="テレメトリのボリュームを抑制する方法。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>

#  Application Insights におけるサンプリング

*Application Insights はプレビュー段階です。*


サンプリングは Application Insights の機能です。サンプリングを使用すると、テレメトリを収集し、サイズを減らして保存できるだけでなく、アプリケーション データを統計的に正しく分析することができます。また、トラフィックも減るので、[スロットル](app-insights-pricing.md#data-rate)も回避できます。データは、関連する項目単位でフィルター処理されるので、診断調査を実行するときも項目単位で操作できます。ポータルでメトリック数が表示されるときは、統計値への影響を最小限に抑えるために、メトリック数を再正規化してサンプリングに配慮します。

Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降の既定では、アダプティブ サンプリングは有効です。サンプリングは現在ベータ版の段階にあり、今後変更される可能性があります。

現在は 2 つのサンプリング モジュールがあります。

* アダプティブ サンプリングはサンプリングの割合を自動的に調整し、要求が一定の量になるようにします。現在は、ASP.NET サーバー側テレメトリでのみ使用できます。  
* 固定レート サンプリングも利用できます。サンプリングの割合を指定します。ASP.NET Web アプリ コードおよび JavaScript Web ページで使用できます。クライアントとサーバーはサンプリングを同期するので、検索では関連のあるページ ビューと要求の間を移動できます。

## アダプティブ サンプリングを有効にする

**プロジェクトの NuGet** パッケージを Application Insights の最新の*プレリリース* バージョンに更新します。ソリューション エクスプ ローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選択し、**[プレリリースを含める]** をオンにして、Microsoft.ApplicationInsights.Web を検索します。

[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) で、`AdaptiveSamplingTelemetryProcessor` ノードのさまざまなパラメーターを調整できます。次の図は既定値です。

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    アダプティブ アルゴリズムが**単一のサーバー ホスト**で目標とするレート。Web アプリが多数のホストで実行される場合は、Application Insights ポータルのトラフィックの目標レート内に収まるようにこの値を減らします。

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    テレメトリの現在のレートを再評価する間隔。評価は移動平均として実行されます。急変しやすいテレメトリの場合は、この間隔を短くすることもできます。

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    サンプリング率が変化してから、どのくらいの時間が経過すると、サンプリング率を下げてキャプチャ データ量を減らすことができるようになるかを指定します。

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageDecreaseTimeout>`

    サンプリング率が変化してから、どのくらいの時間が経過すると、サンプリング率を上げてキャプチャ データ量を増やすことができるようになるかを指定します。

* `<MinSamplingPercentage>0.1<\MinSamplingPercentage>`

    サンプリング率の変化に合わせて、設定できる最小値。

* `<MaxSamplingPercentage>100.0<\MaxSamplingPercentage>`

    サンプリング率の変化に合わせて、設定できる最大値。

* `<MovingAverageRatio>0.25</MovingAverageRatio>`

    移動平均の計算で最新値に割り当てられる重み。1 以下の値を使用します。小さい値にすると、急変に対する反応が低いアルゴリズムになります。

* `<InitialSamplingPercentage>100<\InitialSamplingPercentage>`

    アプリの起動直後に割り当てられる値。デバッグ中はこの値を減らさないでください。

<a name="other-web-pages"></a>
## JavaScript を使用した Web ページのサンプリング

任意のサーバーから固定レートのサンプリング用に Web ページを構成できます。

[Application Insights 用に Web ページを構成する](app-insights-javascript.md)場合は、Application Insights ポータルからスニペットを入手して、それを変更します(通常、ASP.NET アプリでは \_Layout.cshtml を利用できます)。 `samplingPercentage: 10,` のような行をインストルメンテーション キーの前に挿入します。

    <script>
	var appInsights= ... 
	}({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

サンプリング率には、N を整数として 100/N に近い割合を選択します。サンプリングでは現在、その他の値はサポートされていません。

サーバーでも固定レート サンプリングを有効にしている場合は、クライアントとサーバーはサンプリングを同期するので、検索で関連のあるページ ビューと要求の間を移動できます。


## サーバーで固定レートのサンプリングを有効にする

1. **プロジェクトの NuGet パッケージ**を Application Insights の最新の*プレリリース* バージョンに更新します。ソリューション エクスプ ローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選択し、**[プレリリースを含める]** をオンにして、Microsoft.ApplicationInsights.Web を検索します。 

2. **アダプティブ サンプリングを無効にする**: [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) の `AdaptiveSamplingTelemetryProcessor` ノードを削除するか、コメント アウトします。

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **固定レート サンプリング モジュールを有効にします。** 次のスニペットを [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) に追加します。

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE]サンプリング率には、N を整数として 100/N に近い割合を選択します。サンプリングでは現在、その他の値はサポートされていません。



### 代替手順: サーバー コードでサンプリングを設定します。


.config ファイルにサンプリング パラメーターを設定する代わりに、コードを使用できます。

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([テレメトリ プロセッサについてはこちらを参照してください](app-insights-api-filtering-sampling/#filtering))。

## サンプリングを使用する場合

ASP.NET SDK バージョン 2.0.0-beta3 以降を使用している場合、アダプティブ サンプリングは自動的に有効になります。

ほとんどの中小規模アプリケーションには、サンプリングは不要です。最も役立つ診断情報や最も正確な統計値は、すべてのユーザー アクティビティからデータを収集することで入手します。

 
サンプリングの主な利点:

* アプリが短期間に非常に高いレートのテレメトリを送信すると、Application Insights サービスがデータ ポイントを削減 ("スロットル") する。 
* データ ポイントを、価格レベルの[クォータ](app-insights-pricing.md)以内に抑える。 
* テレメトリの収集によるネットワーク トラフィックを削減する。 

### 固定サンプリングとアダプティブ サンプリング

次の場合は固定レート サンプリングを使用します。

* クライアントとサーバーのサンプリングを同期する場合。[検索](app-insights-diagnostic-search.md)でイベントを調査するときに、クライアントとサーバーの関連するイベント単位 (ページ ビュー、HTTP 要求など) で操作できます。
* アプリに適したサンプリング率を確保する場合。正確なメトリックを取得できる程度に高く、価格クォータとスロットル制限を超えないレートにする必要があります。 
* アプリのデバッグ中ではなく、F5 キーを押してアプリの数ページを試すと、すべてのテレメトリを確認できる場合。

それ以外の場合は、アダプティブ サンプリングをお勧めします。

## サンプリングのしくみ

アプリケーションの観点から見ると、サンプリングとは Application Insights SDK の機能です。ユーザーは、すべてのデータ ポイントの何パーセントが Application Insights サービスに送信されるかを指定します。Application Insights SDK のバージョン 2.0.0 からは、コードによってサンプリング率を制御できます(SDK の今後のバージョンでは、ApplicationInsights.config file からサンプリング率を構成する機能が追加されます)。

SDK では、削除するテレメトリ項目と、維持するテレメトリ項目を決めます。サンプリングはいくつかのルールに基づいて決定されますが、このルールのねらいは、削減されたデータ セットを使用する場合でも、Application Insights の診断機能を実用的で信頼性の高いものに維持しながら、すべての相互に関連するデータ ポイントはそのままの状態で保持することです。たとえば、失敗した要求に対してアプリが追加のテレメトリ項目 (この要求からログに記録された例外やトレースなど) を送信した場合、サンプリングでは、この要求とその他のテレメトリを分割することはありません。すべてをまとめて維持するか、削除するかのどちらかです。そのため、Application Insights で要求の詳細を表示する場合は、その要求に加えて、関連付けられているテレメトリ項目も常に表示されます。

"ユーザー" を定義するアプリケーション (つまり、最も一般的な Web アプリケーション) の場合、サンプリングはユーザー ID のハッシュに基づいて決定されるため、特定のユーザーのすべてのテレメトリが保持されるか、削除されることになります。ユーザーを定義しないタイプのアプリケーション (Web サービスなど) の場合、サンプリングは要求の操作 ID に基づいて決定されます。最後に、ユーザーも操作 ID も設定されていないテレメトリ項目 (http コンテキストのない、非同期スレッドから報告されたテレメトリ項目など) の場合、サンプリングでは各タイプのテレメトリ項目を単純に一定の割合ずつキャプチャします。

テレメトリを表示する場合、Application Insights サービスは、不足しているデータ ポイントを補正するために、収集時に使用したものと同じサンプリング率でメトリックを調整します。そのため、Application Insights でテレメトリを表示する場合、ユーザーは統計的に正しく、実際の数値に非常に近い近似値を見ていることになります。

近似精度は、構成したサンプリング率に大きく左右されます。また、多数のユーザーからの同じような要求を大量に処理するアプリケーションの場合は、近似精度が高くなります。一方で、処理量が膨大ではないアプリケーションの場合、通常はクォータの範囲内ですべてのテレメトリを送信でき、スロットルによるデータの損失もないため、サンプリングは必要ありません。

Application Insights では、精度が問題のあるレベルまで低下する可能性があるため、メトリックおよびセッションのテレメトリ タイプに対するサンプリングは行いません。

### アダプティブ サンプリング

アダプティブ サンプリングの場合、SDK からの現在の転送速度を監視し、目標最大レート以下になるようにサンプリング率を調整するコンポーネントが追加されます。調整は、定期的に、送信レートの移動平均に基づいて再計算されます。

## サンプリングと JavaScript SDK

サンプリングでは、クライアント側 (JavaScript) SDK とサーバー側 SDK を組み合わせて使用します。SDK が追加されたページは、サーバー側が "サンプリングに入れる" と判断したユーザーと同じユーザーからのクライアント側テレメトリのみを送信します。これは、クライアント側とサーバー側の間でユーザー セッションの整合性を維持するためのロジックです。その結果、Application Insights 内の特定のテレメトリ項目から、このユーザーまたはセッションに関するその他すべてのテレメトリ項目を見つけることができます。

*クライアント側とサーバー側のテレメトリに、前述されているような調整済みのサンプルが表示されない場合。*

* サーバーとクライアントの両方でサンプリングを有効にしていることを確認してください。
* SDK のバージョンが 2.0 以降であることを確認してください。
* クライアントとサーバーの両方で同じサンプリング率を設定していることを確認してください。


## よく寄せられる質問 

*サンプリングを、"各テレメトリ タイプの X% を収集" という単純な方法にしないのはなぜですか。*

 *  このサンプリング方法の場合、メトリックの近似精度は非常に高くなりますが、一方でユーザーごと、セッションごと、要求ごとに診断データを関連付けるという、診断には欠かせない機能が利用できなくなります。そのため、サンプリングには "アプリ ユーザーの X% に関するすべてのテレメトリ項目を収集"、または "アプリ要求の X% に関するすべてのテレメトリを収集" というロジックが適しています。要求に関連付けられていないテレメトリ項目 (バックグラウンドの非同期処理など) の場合は、代わりに "各テレメトリ タイプに関するすべての項目の X% を収集" とします。 

*サンプリング率は、時間経過に伴い変化する可能性がありますか。*

 * はい。アダプティブ サンプリングの場合、現在監視されているテレメトリのデータ量に基づいて、サンプリング率が徐々に変化します。

*アダプティブ サンプリングが使用しているサンプリング レートは確認できますか。*

 * 現在のバージョンでは確認できません。

*固定レート サンプリング率を使用している場合、自分のアプリに最適なサンプリング率は、どのようにして確認できますか。*

* 現状では、推測するしかありません。AI での現在のテレメトリ使用状況を分析し、スロットルに関連したデータの削除を観察して、収集されるテレメトリのボリュームを見積もります。これら 3 つの情報に、選択した価格レベルを合わせて考えると、収集されるテレメトリのボリュームをどれくらい削減する必要があるかがわかります。ただし、テレメトリ ボリュームのパターンが変化することで、最適に構成されたサンプリング率が無効になる場合もあります (たとえばユーザー数の増加など)。

*サンプリング率を低く構成しすぎると、どうなりますか。*

* Application Insights がデータ ボリュームの減少に関してデータの可視化を補正しようとしている場合、極端に低いサンプリング率 (過度にアグレッシブなサンプリング) は、近似精度の低下につながります。また、低頻度の失敗や遅い要求の一部はサンプリングから除外される場合があるため、診断機能に悪影響を及ぼす可能性もあります。

*サンプリング率を高く構成しすぎると、どうなりますか。*

* サンプリング率を高く構成しすぎた (十分にアグレッシブでない) 場合は、収集されるテレメトリのボリュームが十分に減少しないことになります。スロットルに関連するテレメトリ データが失われる可能性が残り、超過料金によって Application Insights の使用コストが予定額を超える場合もあります。

*サンプリングはどのようなプラットフォームで使用できますか。*

* 現在、アダプティブ サンプリングは、(Azure またはオンプレミス サーバーにホストされている) ASP.NET Web ページのサーバー側で使用できます。固定レート サンプリングは、任意の Web ページと、クライアント側およびサーバー側の両方の .NET Web アプリケーションで使用できます。

*常に確認したい頻度が低いイベントがあります。サンプリング モジュールを使わずに確認するにはどうすればよいですか。*

 * 別の TelemetryConfiguration で TelemetryClient の別インスタンスを作成します。そのインスタンスを使用して、頻度の低いイベントを送信してください。

<!---HONumber=AcomDC_1125_2015-->