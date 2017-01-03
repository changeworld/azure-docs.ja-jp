アプリケーションごと (インストルメンテーション キーごと) のメトリックとイベントの数には制限があります。 

制限は、選択する[料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)によって異なります。

| **リソース** | **既定の制限** | **注**
| --- | --- | --- |
| 1 日あたりの合計データ量 | 100 GB | 上限を設定することで削減できます。 さらに必要な場合は、AIDataCap@microsoft.com まで電子メールでご連絡ください。 
| 1 月あたりの無料データ量<br/> (Basic 料金プラン) | 1 GB | 超過分については、GB 単位で課金されます
| データの保持 | 90 日間 | 用途: [Search](../articles/application-insights/app-insights-diagnostic-search.md)、[Analytics](../articles/application-insights/app-insights-analytics.md)、[メトリックス エクスプローラー](../articles/application-insights/app-insights-metrics-explorer.md)
| [可用性の複数手順のテスト](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests)の詳細な結果の保持 | 90 日間 | 各手順の詳細な結果
| [プロパティ](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)と[メトリック](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)<sup>2</sup> 名の数 | 200 | 
| プロパティとメトリック名の長さ | 150 |
| プロパティ値の文字列の長さ | 8192 |
| プロパティの個別の値<sup>3、4</sup> | 100 | 100 を超える場合、メトリックス エクスプローラーでプロパティをフィルターとして使用できません
| トレースおよび例外のメッセージ長 | 10000 |
| アプリあたりの[可用性テスト](../articles/application-insights/app-insights-monitor-web-app-availability.md)の数  | 10 |

1. これらはすべてインストルメンテーション キーあたりの数です。
2. メトリック名は、TrackMetric と、その他の Track*() 呼び出しの測定パラメーターの両方で定義されます。 メトリック名は、インストルメンテーション キーごとにグローバルです。
3. 各プロパティに対する一意の値は 100 未満であり、プロパティは、フィルタリングとグループ化のみに使用できます。 一意の値の数が 100 を超えてもプロパティを検索することはできますが、フィルターまたはグループ化には使用できなくなります。
4. 要求名やページの URL などの標準プロパティは、1 週間あたりの 1000 の一意な値に制限されます。 1000 の一意の値を超えると、追加の値は "その他の値" としてマークされます。 元の値は、全文テキスト検索とフィルター処理に引き続き使用できます。


[Application Insights の価格とクォータについて](../articles/application-insights/app-insights-pricing.md)

<!--HONumber=Dec16_HO3-->


