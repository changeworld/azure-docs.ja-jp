```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|プロパティ  |既定値 | Description |
|---------|---------|---------| 
|maxPollingInterval|60000|キューのポーリングの最大間隔 (ミリ秒)。| 
|visibilityTimeout|0|メッセージの処理が失敗したときの再試行間隔。| 
|batchSize|16|並列で取得および処理されるキュー メッセージ数。 最大値は 32 です。| 
|maxDequeueCount|5|有害キューに移動する前に、メッセージの処理を試行する回数。| 
|newBatchThreshold|batchSize/2|新しいメッセージのバッチを取得するしきい値。| 
