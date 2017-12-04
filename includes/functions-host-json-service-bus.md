```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|メッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。 一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、`maxConcurrentCalls` を 1 に設定します。 | 
|prefetchCount|該当なし|基になる MessageReceiver に使用される既定の PrefetchCount。| 
|autoRenewTimeout|00:05:00|メッセージ ロックが自動的に更新される最大間隔。| 
