---
title: Azure Stream Analytics でのクエリのテスト
description: この記事では、Stream Analytics ジョブでサンプル データ ファイルを使用してクエリをテストする方法について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: fb7d936dfdc543c208ce98c588f5ad83704ff5dc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Azure Portal で Azure Stream Analytics クエリをテストする

Azure Stream Analytics では、ジョブを開始したり停止したりすることなく、Azure Portal でクエリをテストできます。

## <a name="test-the-input"></a>入力をテストする

1. サンプル入力データでテストするには、入力のいずれかを右クリックし、**[ファイルからサンプル データをアップロードする]** を選択します。 現在は、JSON 形式のデータのみをアップロードできます。 データが CSV などの別の形式になっている場合は、アップロードする前に JSON に変換する必要があります。 [CSV から JSON へのコンバーター](http://www.convertcsv.com/csv-to-json.htm)のような任意のオープン ソース変換ツールを使って、データを JSON に変換できます。

    ![Stream Analytics クエリ エディターでクエリをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. アップロードが完了したら、**[テスト]** をクリックして、用意したサンプル データでこのクエリをテストします。

    ![Stream Analytics クエリ エディターでサンプル データをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

クエリの出力はブラウザーに表示されます。後で使用するためにテストの出力を保存する場合は、結果のダウンロードのためのリンクを利用できます。 これで、クエリを簡単に何度も編集してテストを繰り返し行い、出力がどのように変わるかを確認することができます。

![Stream Analytics クエリ エディターのサンプル出力](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

クエリで複数の出力を使用している場合には、両方の出力結果を個別に確認できるほか、それらを簡単に切り替えることができます。

ブラウザーに表示された結果に問題がなければ、クエリを保存し、ジョブを開始して、エラーを発生させずにイベントを処理できます。

## <a name="get-help"></a>問い合わせ

さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
