---
title: Anomaly Detector 多変量 API のトラブルシューティング
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API を使用する場合の一般的なエラー コードを修復する方法について説明します
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 異常検出, 機械学習, アルゴリズム
ms.openlocfilehash: 46b67e3f465bfb481389049e4c45e5c2edbefbfc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292693"
---
# <a name="troubleshooting-the-multivariate-api"></a>多変量 API のトラブルシューティング

この記事では、多変量 API を使用する場合の一般的なエラー メッセージのトラブルシューティングと修復を行う方法に関するガイダンスを提供します。

## <a name="multivariate-error-codes"></a>多変量のエラー コード

### <a name="common-errors"></a>一般的なエラー

| エラー コード                 | HTTP エラー コード | エラー メッセージ                                  | コメント                                                      |
| -------------------------- | --------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| `SubscriptionNotInHeaders` | 400             | ヘッダーに apim-subscription-id が見つかりません | ヘッダーに APIM サブスクリプション ID を追加してください。 ヘッダーの例: `{"apim-subscription-id": <Your Subscription ID>}` |
| `FileNotExist`             | 400             | ファイル \<source> が存在しません。                  | BLOB の Shared Access Signature (SAS) の有効性を確認してください。 その有効期限が切れていないことを確認します。 |
| `InvalidBlobURL`           | 400             |                                                | BLOB の Shared Access Signature (SAS) が有効な SAS ではありません。                            |
| `StorageWriteError`        | 403             |                                                | このエラーは、アクセス許可の問題によって発生する可能性があります。 サービスでは、カスタマー マネージド キー (CMK) によって暗号化された BLOB へのデータの書き込みが許可されていません。 CMK を削除するか、サービスにもう一度アクセス権を付与してください。 詳細については、[このページ](../../encryption/cognitive-services-encryption-keys-portal.md)を参照してください。 |
| `StorageReadError`         | 403             |                                                | `StorageWriteError` と同じ。                                 |
| `UnexpectedError`          | 500             |                                                | 詳細なエラー情報をご連絡ください。 [このドキュメント](../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fanomaly-detector%2fcontext%2fcontext)からサポート オプションを利用することも、[AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com) にメールを送信することもできます           |


### <a name="train-a-multivariate-anomaly-detection-model"></a>Train a Multivariate Anomaly Detection Model

| エラー コード               | HTTP エラー コード | エラー メッセージ                                                | コメント                                                      |
| ------------------------ | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `TooManyModels`          | 400             | このサブスクリプションは、モデルの最大数に達しました。  | 各 APIM サブスクリプション ID では、300 個のアクティブなモデルを使用できます。 新しいモデルをトレーニングする前に、未使用のモデルを削除してください |
| `TooManyRunningModels`   | 400             | このサブスクリプションは、実行中のモデルの最大数に達しました。 | 各 APIM サブスクリプション ID では、5 つのモデルを同時にトレーニングできます。 以前のモデルがトレーニング プロセスを完了した後に、新しいモデルをトレーニングしてください。 |
| `InvalidJsonFormat`      | 400             | JSON 形式が無効です。                                         | トレーニング要求は有効な JSON ではありません。                        |
| `InvalidAlignMode`       | 400             | `'alignMode'` フィールドは次のいずれかである必要があります: `'Inner'` または `'Outer'`。 | `'alignMode'` の値を確認してください。これは `'Inner'` または `'Outer'` (大文字と小文字が区別されます) のいずれかである必要があります。 |
| `InvalidFillNAMethod`    | 400             | `'fillNAMethod'` フィールドは次のいずれかである必要があります: `'Previous'`、`'Subsequent'`、`'Linear'`、`'Zero'`、`'Fixed'`、`'NotFill'`。および `'alignMode'` が `'Outer'` の場合に `'NotFill'` にすることはできません。 | `'fillNAMethod'` の値を確認してください。 詳細については、[このセクション](./best-practices-multivariate.md#optional-parameters-for-training-api)を参照できます。 |
| `RequiredPaddingValue`   | 400             | `'fillNAMethod'` が `'Fixed'` の場合、要求には `'paddingValue'` フィールドが必要です。 | `'fillNAMethod'` が `'Fixed'` の場合、有効な埋め込み値を指定する必要があります。 詳細については、[このセクション](./best-practices-multivariate.md#optional-parameters-for-training-api)を参照できます。 |
| `RequiredSource`         | 400             | 要求には `'source'` フィールドが必要です。             | トレーニング要求で、`'source'` フィールドの値が指定されていません。 例: `{"source": <Your Blob SAS>}`. |
| `RequiredStartTime`      | 400             | 要求には `'startTime'` フィールドが必要です。          | トレーニング要求で、`'startTime'` フィールドの値が指定されていません。 例: `{"startTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidTimestampFormat` | 400             | タイムスタンプの形式が無効です。 `<timestamp>` が有効な形式ではありません。 | 要求本文のタイムスタンプの形式が正しくありません。 `import pandas as pd; pd.to_datetime(timestamp)` を実行して確認できます。 |
| `RequiredEndTime`        | 400             | 要求には `'endTime'` フィールドが必要です。            | トレーニング要求で、`'startTime'` フィールドの値が指定されていません。 例: `{"endTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidSlidingWindow`   | 400             | `'slidingWindow'` フィールドには 28 から 2880 までの整数を指定してください。 | `'slidingWindow'` は、28 以上 2880 以下の整数を指定する必要があります。 |

### <a name="get-multivariate-model-with-model-id"></a>モデル ID で多変量モデルを取得する

| エラー コード      | HTTP エラー コード | エラー メッセージ             | コメント                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
| `ModelNotExist` | 404             | モデルが存在しません。 | 対応するモデル ID のモデルが存在しません。 要求 URL のモデル ID を確認してください。 |

### <a name="list-multivariate-models"></a>多変量モデルを一覧表示する

| エラー コード      | HTTP エラー コード | エラー メッセージ             | コメント                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
|`InvalidRequestParameterError`| 400             | $Skip または $top の値が無効です... | 2 つのパラメーターの値が数値であるかどうかを確認してください。 $skip と $top は、改ページ位置の自動修正されたモデルを一覧表示するために使用されます。 API によって最近更新された 10 個のモデルのみが返されるため、$skip と $top を使用して、それ以前に更新されたモデルを取得できます。 | 

### <a name="anomaly-detection-with-a-trained-model"></a>トレーニング済みのモデルによる異常検出

| エラー コード        | HTTP エラー コード | エラー メッセージ                                                | コメント                                                      |
| ----------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `ModelNotExist`   | 404             | モデルが存在しません。                                    | 推論に使用されるモデルが存在しません。 要求 URL のモデル ID を確認してください。 |
| `ModelFailed`     | 400             | モデルのトレーニングに失敗しました。                                  | モデルが正しくトレーニングされませんでした。 モデル ID でモデルを取得して、詳細情報を取得してください。 |
| `ModelNotReady`   | 400             | モデルの準備がまだできていません。                                  | モデルの準備がまだできていません。 トレーニング プロセスが完了するまでお待ちください。 |
| `InvalidFileSize` | 413             | ファイル \<file> がファイルのサイズ制限 (\<size limit> バイト) を超えています。 | 推論データのサイズが上限 (現在 2 GB) を超えています。 推論に使用するデータを減らしてください。 |

### <a name="get-detection-results"></a>検出結果の取得

| エラー コード       | HTTP エラー コード | エラー メッセージ              | コメント                                                      |
| ---------------- | --------------- | -------------------------- | ------------------------------------------------------------ |
| `ResultNotExist` | 404             | 結果が存在しません。 | 要求ごとの結果が存在しません。 推論が完了していないか、結果の有効期限 (7 日) が切れています。 |

### <a name="data-processing-errors"></a>データ処理エラー

次のエラーコードには、HTTP エラーコードが関連付けられていません。

| エラー コード             | エラー メッセージ                                                | コメント                                                      |
| ---------------------  | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `NoVariablesFound`     | 変数が見つかりません。 ファイルが指示に従って編成されていることを確認してください。 | データ ソースから csv ファイルが見つかりませんでした。 これは通常、ファイルの誤った編成によって発生します。 必要な構造については、サンプル データを参照してください。 |
| `DuplicatedVariables`  | 同じ名前の複数の変数あります。             | 重複した変数名があります。                         |
| `FileNotExist`         | ファイル \<filename> が存在しません。                              | このエラーは通常、推論中に発生します。 変数は、トレーニング データにありましたが、推論データにありません。 |
| `RedundantFile`        | ファイル \<filename> が重複しています。                                | このエラーは通常、推論中に発生します。 変数はトレーニング データに含まれていませんでしたが、推論データにありました。 |
| `FileSizeTooLarge`     | ファイル \<filename> のサイズが大きすぎます。                    | 1 つの csv ファイル \<filename> のサイズが制限を超えています。 データを減らしてトレーニングしてください。 |
| `ReadingFileError`     | \<filename> の読み取り中にエラーが発生しました。 \<error messages>    | ファイル \<filename> を読み取れませんでした。 詳細について、\<error messages> を参照するか、または、ローカル環境で `pd.read_csv(filename)` を使用して確認することもできます。 |
| `FileColumnsNotExist`  | ファイル \<filename> に列 timestamp または value が存在しません。  | 各 csv ファイルには、**timestamp** と **value** (大文字と小文字が区別されます) という名前の 2 つの列が必要です。 |
| `VariableParseError`   | 変数 \<variable> によって、\<error message> エラーが解析されます。             | 実行時エラーのため、\<variable> を処理できません。 詳細について、\<error message> を参照するか、または、\<error message> をご連絡ください。 |
| `MergeDataFailed`      | データをマージできませんでした。 データ形式を確認してください。              | データのマージに失敗しました。 これは、データ形式やファイルの編成などが間違っていることによる可能性があります。現在のファイル構造については、サンプル データを参照してください。 |
| `ColumnNotFound`       | マージされたデータに列 \<column> が見つかりません。          | マージ後に列がありません。 データを確認してください。     |
| `NumColumnsMismatch`   | マージされたデータの列数が変数の数と一致しません。 | データを確認してください。                                      |
| `TooManyData`          | データ ポイントが多すぎます。 変数あたりの最大数は 100 万です。       | 入力データのサイズを減らしてください。                        |
| `NoData`               | 有効なデータがありません                                   | 処理後にトレーニングまたは推論するデータがありません。 開始時刻と終了時刻を確認してください。 |
| `DataExceedsLimit`     | タイムスタンプが `startTime` と `endTime` の間であるデータの長さが制限 (\<limit>) を超えています。 | 処理後のデータのサイズが制限を超えています。 (現在、処理済みのデータの制限はありません。) |
| `NotEnoughInput`       | データが不足しています。 データの長さは \<data length> ですが、最小の長さは、\<sliding window size> であるスライディング ウィンドウよりも大きくする必要があります。 | 推論用の最少データ ポイント数は、スライディング ウィンドウのサイズです。 推論用に指定するデータを増やしてみてください。 |