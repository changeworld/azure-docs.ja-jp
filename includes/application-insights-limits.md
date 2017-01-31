アプリケーションごと (インストルメンテーション キーごと) のメトリックとイベントの数には制限があります。 

制限は、選択する[料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)によって異なります。

| **リソース** | **既定の制限** | **注**
| --- | --- | --- |
| 1 日あたりの合計データ量 | 500 GB | 上限を設定することで削減できます。 さらに必要な場合は、AIDataCap@microsoft.com まで電子メールでご連絡ください。 
| 1 月あたりの無料データ量<br/> (Basic 料金プラン) | 1 GB | 超過分については、GB 単位で課金されます
| 調整 | 16,000 件のイベント/秒 | 1 分以上にわたって測定されます。 
| データの保持 | 90 日間 | 用途: [Search](../articles/application-insights/app-insights-diagnostic-search.md)、[Analytics](../articles/application-insights/app-insights-analytics.md)、[メトリックス エクスプローラー](../articles/application-insights/app-insights-metrics-explorer.md)
| [可用性の複数手順のテスト](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests)の詳細な結果の保持 | 90 日間 | 各手順の詳細な結果
| プロパティとメトリック名の長さ | 150 |
| プロパティ値の文字列の長さ | 8192 |
| トレースおよび例外のメッセージ長 | 10000 |
| アプリあたりの[可用性テスト](../articles/application-insights/app-insights-monitor-web-app-availability.md)の数  | 10 |

1. これらはすべてインストルメンテーション キーあたりの数です。

[Application Insights の価格とクォータについて](../articles/application-insights/app-insights-pricing.md)


<!--HONumber=Jan17_HO4-->


