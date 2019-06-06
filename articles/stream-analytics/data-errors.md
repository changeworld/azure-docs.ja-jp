---
title: Azure Stream Analytics 診断ログ データ エラー
description: この記事では、Azure Stream Analytics の使用時に発生することがあるさまざまな入力および出力データ エラーについて説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: b00eb12092838746f4bfe16f00eac55df9224b09
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607110"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics データ エラー

Azure Stream Analytics ジョブによって処理されたデータに不一致があるとき、Stream Analytics から診断ログにデータ エラー イベントが送信されます。 データ エラーが発生すると、Stream Analytics により、その診断ログに詳しい情報とサンプル イベントが書き込まれます。 一部のエラーに関しては、ポータル通知からもこの情報の概要が提供されます。

この記事では、入力および出力データ エラーについて、さまざまなエラーの種類、原因、診断ログの詳細を説明します。

## <a name="diagnostic-log-schema"></a>診断ログのスキーマ

診断ログのスキーマの詳細については、[診断ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema)に関するページを参照してください。 次の JSON は、データ エラーの診断ログの **[プロパティ]** フィールドのサンプル値です。

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>入力データ エラー

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* 原因: 選択された入力圧縮タイプがデータに一致しません。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 入力メッセージ ID。 Event Hub の場合、識別子はパーティション ID、オフセット、シーケンス番号になります。

**エラー メッセージ**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* 原因: 入力データのヘッダーが無効です。 たとえば、CSV に名前が重複する列があります。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 入力メッセージ ID。 
   * 実際のペイロードは最大で数キロバイトです。

**エラー メッセージ**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* 原因: CREATE TABLE または TIMESTAMP BY で定義された入力列が存在しません。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 入力メッセージ ID。 
   * 不足している列の名前。 
   * 実際のペイロードは最大で数キロバイトです。

**エラー メッセージ**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* 原因: CREATE TABLE ステートメントで指定された型に入力を変換できません。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 入力メッセージ ID。 
   * 列の名前と予想される型。

**エラー メッセージ**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* 原因: 入力データの形式が正しくありません。 たとえば、入力が有効な JSON ではありません。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 入力メッセージ ID。 
   * 実際のペイロードは最大で数キロバイトです。

**エラー メッセージ**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* 原因: TIMESTAMP BY 式の値を datetime に変換できません。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 入力メッセージ ID。 
   * エラー メッセージ。 
   * 実際のペイロードは最大で数キロバイトです。

**エラー メッセージ**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* 原因: TIMESTAMP BY OVER timestampColumn の値が NULL です。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 実際のペイロードは最大で数キロバイトです。

**エラー メッセージ**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* 原因: アプリケーション時間と到着時刻の差が到着遅延許容範期間を超えています。
* ポータル通知提供:いいえ
* 診断ログのレベル:情報
* ログの詳細
   * アプリケーション時間と到着時刻 
   * 実際のペイロードは最大で数キロバイトです。

**エラー メッセージ**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* 原因: アプリケーション時間と到着時刻の差が 5 分以上あります。
* ポータル通知提供:いいえ
* 診断ログのレベル:情報
* ログの詳細
   * アプリケーション時間と到着時刻 
   * 実際のペイロードは最大で数キロバイトです。

**エラー メッセージ**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* 原因: 定義されている許容範囲が正しくないため、イベントが順序逸脱であると見なされます。
* ポータル通知提供:いいえ
* 診断ログのレベル:情報
* ログの詳細
   * 実際のペイロードは最大で数キロバイトです。

**エラー メッセージ**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>出力データ エラー

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* 原因: 出力に必要な列が存在しません。 たとえば、Azure Table PartitionKey として定義されている列が存在しません。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 列の名前と、レコード ID またはレコードの一部。

**エラー メッセージ**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* 原因: 列の値が出力に準拠していません。 たとえば、列名が有効な Azure テーブル列ではありません。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 列の名前と、レコード ID またはレコードの一部。

**エラー メッセージ**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* 原因: 列を出力で有効な型に変換できません。 たとえば、列の値が、SQL テーブルに定義されている制約や型と互換性がありません。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 列の名前です。
   * レコード ID またはレコードの一部。

**エラー メッセージ**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* 原因: メッセージの値がサポートされている出力サイズを超えています。 たとえば、イベント ハブ出力のレコードが 1 MB を超えています。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * レコード ID またはレコードの一部。

**エラー メッセージ**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* 原因: あるレコードに、System 列と同じ名前の列が既に含まれています。 たとえば、CosmosDB 出力に ID という名前の列がありますが、ID 列は別の列の名前です。
* ポータル通知提供:はい
* 診断ログのレベル:警告
* ログの詳細
   * 列の名前です。
   * レコード ID またはレコードの一部。

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>次の手順

* [診断ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)

* [Stream Analytics ジョブ監視とクエリの監視方法の概要](stream-analytics-monitoring.md)
