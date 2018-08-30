---
title: サンプル データを利用した Azure Stream Analytics ジョブのテスト
description: Stream Analytics ジョブでクエリをテストする方法
keywords: この記事では､Azure ポータルからサンプル入力を利用して Azure Stream Analytics ジョブをテストし､サンプル データをアップロードする方法を説明しています｡
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: d699d69362b2d28c205aab14a4bfb26570a68a4e
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "42141957"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>サンプル データを利用した Stream Analytics クエリのテスト

Azure Stream Analytics を使用すると、ジョブの起動や停止をすることなく、Azureポータルでサンプル データのアップロードと クエリのテストができます。

## <a name="upload-sample-data-and-test-the-query"></a>サンプル データのアップロードとクエリのテスト

1. Azure ポータルにサインインします。 

2. Stream Analytics ジョブがある場所を探し､ジョブを選択します｡

3. [Stream Analytics ジョブ] ページの **ジョブ トポロジ** の見出しの部分から **クエリ** を選択し､クエリ エディター ウィンドウを開きます。 

4. サンプルの入力データを使ってクエリをテストするには､入力のいずれかを右クリックします｡  **ファイルからサンプルデータをアップロード** を選択します｡ データは、JSON、CSV または AVRO でシリアル化する必要があります。

    ![Stream Analytics クエリ エディターでクエリをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. アップロードが完了したら、**[テスト]** を選択して、用意したサンプル データでこのクエリをテストします。

    ![Stream Analytics クエリ エディターでサンプル データをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. テストの出力を保存して後で利用できるよう、ブラウザーに表示されるクエリの出力には、ダウンロード結果へのリンクが含まれています｡ 

7. クエリの変更とテストを繰り返して､出力がどのように変化するかを確認します｡

   ![Stream Analytics クエリ エディターのサンプル出力](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   1 つのクエリで複数の出力を使用すると､結果は出力ごとに別のタブに表示されますが､タブは簡単に切り替えることができます｡

8. ブラウザで結果を確認したら､クエリを **保存** します｡ ジョブを **Start** します｡外部からのイベントがジョブによって処理されます｡

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
