アプリケーションごと (インストルメンテーション キーごと) のメトリックとイベントの数には制限があります。

制限は、選択する[価格レベル](https://azure.microsoft.com/pricing/details/application-insights/)によって異なります。

**リソース** | **既定の制限** | **上限**
-------- | ------------- | -------------
セッションのデータ ポイント数<sup>1</sup>/月 | 無制限 | 
その他のデータ ポイント数/月 | 500 万 | 5,000 万<sup>2</sup>
[トレースまたはログ](../articles/application-insights/app-insights-search-diagnostic-logs.md)のデータ速度 | 200 dp/s | 500 dp/s
[例外](../articles/application-insights/app-insights-asp-net-exceptions.md)データ速度 | 50 dp/s | 50 dp/s
その他のテレメトリ データ速度 | 200 dp/s | 500 dp/s
[生データ](../articles/application-insights/app-insights-diagnostic-search.md)の保持期間 | 7 日
[集計データ](../articles/application-insights/app-insights-metrics-explorer.md)の保持期間 | 90 日間
[プロパティ](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)名の数 | 100 |
プロパティ名の長さ | 100 | 
プロパティ値の長さ | 1,000 | 
トレースおよび例外のメッセージ長 | 10000 |
[メトリック](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)名の数 | 100 |
メトリック名の長さ | 100 | 
[可用性テスト](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> データ ポイントとは、個々のメトリック値またはイベントに、プロパティと測定値を付加したものです。

<sup>2</sup> 5,000 万を超える追加容量を購入することができます。
 
[Application Insights の価格とクォータについて](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0427_2016-->