---
title: Azure Stream Analytics の BLOB 出力用の DateTime パス パターン (プレビュー)
description: この記事では、Azure Stream Analytics ジョブからの BLOB ストレージ出力に対するカスタム DateTime パス パターン機能について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ba386539c3f3c6740b843575bbccd4b028b8a5a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090790"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Azure Stream Analytics の BLOB ストレージ出力用のカスタム DateTime パス パターン (プレビュー)

Azure Stream Analytics では、BLOB ストレージ出力用のファイル パス内のカスタム日次形式指定子をサポートします。 カスタム DateTime パス パターンを使用すると Hive Streaming 規則に合致する出力形式を指定できます。これにより、Azure Stream Analytics は、ダウンストリーム処理するためのデータを Azure HDInsight と Azure Databricks に送信できるようになります。 カスタム DateTime パス パターンは、BLOB 出力の [パス プレフィックス] フィールドで `datetime` キーワードと書式指定子を使用することで、簡単に実装されます。 たとえば、「 `{datetime:yyyy}` 」のように入力します。

この [Azure Portal](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) へのリンクを使用して、BLOB ストレージ出力のプレビューに対してカスタム DateTime パス パターンを有効にする機能フラグを切り替えます。 この機能は、メイン ポータルでまもなく有効になる予定です。

## <a name="supported-tokens"></a>サポートされているトークン

次の書式指定子トークンを単独でまたは組み合わせて使用して、カスタム DateTime 形式を指定できます。

|書式指定子   |説明   |サンプル時間 2018-01-02T10:06:08 に対する結果|
|----------|-----------|------------|
|{datetime:yyyy}|4 桁の数値としての年|2018|
|{datetime:MM}|月 (01 ～ 12)|01|
|{datetime:M}|月 (1 ～ 12)|1|
|{datetime:dd}|日 (01 ～ 31)|02|
|{datetime:d}|日 (1 ～ 12)|2|
|{datetime:HH}|24 時間表記での時 (00 ～ 23)|10|
|{datetime:mm}|分 (00 ～ 24)|06|
|{datetime:m}|分 (0 ～ 24)|6|
|{datetime:ss}|秒 (00 ～ 60)|08|

カスタム DateTime パターンを使用しない場合は、パス プレフィックスに {date} トークンと {time} トークンのどちらかまたは両方を追加して、組み込みの DateTime 形式を表示するドロップダウン リストを生成できます。

![Stream Analytics の 古い DateTime 形式](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>拡張性と制限事項

プレフィックス文字数の制限に達するまで、任意の数のトークン `{datetime:<specifier>}` をパス パターン内で使用できます。 日時ドロップダウンに既に一覧されている組み合わせ以外の書式指定子を 1 つのトークンの中で組み合わせて使用することはできません。 

`logs/MM/dd` のパス パーティションの場合:

|有効な式   |無効な式   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

パス プレフィックス内で、同じ書式指定子を複数回使用できます。 トークンは毎回繰り返す必要があります。

## <a name="hive-streaming-conventions"></a>Hive Streaming 規則

BLOB ストレージ用のカスタム パス パターンを Hive Streaming 規則と共に使用して、フォルダー名に `column=` によるラベルが付けられることを想定できます。

たとえば、「 `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}` 」のように入力します。

カスタム出力は、Azure Stream Analytics と Hive 間でデータを移植するためのテーブルの変更と手動でのパーティションの追加という煩わしい操作を排除します。 代わりに、次を使用して、多数のフォルダーを自動的に追加できます。

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>例

[Azure Stream Analytics Portal](stream-analytics-quick-create-portal.md) クイック スタート ガイドに従って、ストレージ アカウント、リソース グループ、Stream Analytics ジョブ、および入力ソースを作成します。 クイック スタート ガイドで使用されているサンプル データを使用します。[GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) から入手することもできます。

次の構成の BLOB 出力シンクを作成します。

![Stream Analytics の BLOB 出力シンクの作成](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

完全なパス パターンは次のようになります。


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


ジョブを開始すると、パス パターンに基づくフォルダー構造が BLOB コンテナー内に作成されます。 日レベルまでドリルダウンできます。

![カスタム パス パターンでの Stream Analytics の BLOB 出力](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics からの出力を理解する](stream-analytics-define-outputs.md)
