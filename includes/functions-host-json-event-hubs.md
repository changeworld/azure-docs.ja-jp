```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|maxBatchSize|64|受信ループあたりで受信される最大イベント数。|
|prefetchCount|該当なし|基になる EventProcessorHost に使用される既定の PrefetchCount。| 
|batchCheckpointFrequency|1|EventHub カーソル チェックポイントを作成する前に処理するイベント バッチ数。| 
