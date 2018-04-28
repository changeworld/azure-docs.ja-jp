---
title: サンプル データを使用した Azure Stream Analytics ジョブのテスト | Microsoft Docs
description: Stream Analytics ジョブでクエリをテストする方法
keywords: ジョブのテスト、サンプリングの入力、サンプルの日付のアップロード
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>サンプル データを使用した Stream Analytics クエリのテスト

Azure Stream Analytics を使用すると、ジョブの起動または停止をすることなく、サンプル データとテスト クエリをポータルにアップロードできます。

## <a name="upload-sample-data-and-test-the-query"></a>サンプル データのアップロードとクエリのテスト

1. 既存の Stream Analytics ジョブのいずれかに移動し、**[クエリ]** をクリックして [クエリ エディター] ウィンドウを開きます。 

2. サンプル入力データでクエリをテストするには、入力のいずれかを右クリックし、**[ファイルからサンプル データをアップロードする]** を選択します。 現在は、JSON 形式のデータのみをアップロードできます。 データが CSV などの別の形式になっている場合は、アップロードする前に JSON に変換する必要があります。 [CSV から JSON へのコンバーター](http://www.convertcsv.com/csv-to-json.htm)のような任意のオープン ソース変換ツールを使って、データを JSON に変換できます。

    ![Stream Analytics クエリ エディターでクエリをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. アップロードが完了したら、**[テスト]** をクリックして、用意したサンプル データでこのクエリをテストします。

    ![Stream Analytics クエリ エディターでサンプル データをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. テストの出力を保存して後で使用する場合に備えて、ブラウザーに表示されるクエリの出力には、結果のダウンロードのためのリンクが存在します。 これで、クエリを簡単に何度も編集してテストを繰り返し行い、出力がどのように変わるかを確認することができます。

   ![Stream Analytics クエリ エディターのサンプル出力](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

クエリで複数の出力を使用すると、それぞれの出力結果を個別に確認でき、それらを簡単に切り替えることができます。 ブラウザーに表示された結果を確認して、クエリを保存し、ジョブを開始して、エラーを発生させずにイベントを処理できます。
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
