---
title: Azure Stream Analytics の機能の比較
description: この記事では、Azure portal、Visual Studio、Visual Studio Code でサポートされている、Azure Stream Analytics のクラウド ジョブと IoT Edge ジョブの機能を比較します。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: b169a067f05de422b714e18a4d7ebb9c9e7f0428
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133495"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics の機能の比較

Azure Stream Analytics では、[Azure portal](stream-analytics-quick-create-portal.md)、[Visual Studio](stream-analytics-quick-create-vs.md)、[Visual Studio Code](quick-create-vs-code.md) を使用して、クラウドおよび IoT Edge でストリーミング ソリューションを作成できます。 この記事の表は、両方のジョブの種類について、各プラットフォームでサポートされている機能を示しています。

> [!NOTE]
> Visual Studio と Visual Studio Code のツールでは、中国東部、中国北部、ドイツ中部、ドイツ北東部のリージョンでのジョブはサポートされていません。

## <a name="cloud-job-features"></a>クラウド ジョブの機能


|機能  |ポータル  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|クロス プラットフォーム     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|スクリプトの作成     |はい         |はい         |はい         |
|スクリプトの IntelliSense     |構文の強調表示         |構文の強調表示</br>コード補完</br>エラー マーカー         |構文の強調表示</br>コード補完</br>エラー マーカー         |
|すべての種類の入力、出力、およびジョブ構成の定義     |はい         |はい         |はい         |
|ソース管理     |いいえ         |はい         |はい         |
|CI/CD のサポート     |部分的         |はい         |はい         |
|複数のクエリでの入力と出力の共有     |いいえ         |はい         |はい         |
|サンプル ファイルを使用したクエリのテスト     |はい         |はい        |はい         |
|ライブ データでのローカル テスト     |いいえ         |はい       |はい      |
|ジョブを一覧表示してジョブ エンティティを表示する     |はい         |はい        |はい         |
|ジョブのローカル プロジェクトへのエクスポート     |いいえ         |はい         |はい         |
|ジョブの送信、開始、停止     |はい         |はい         |はい         |
|ジョブ メトリックとジョブ ダイアグラムの表示     |はい         |はい         |ポータルで開く         |
|ジョブの実行時エラーの表示     |はい         |はい         |いいえ         |
|リソース ログ     |はい         |いいえ         |いいえ         |
|カスタム メッセージ プロパティ     |はい         |はい         |いいえ       |
|C# カスタム コード関数とデシリアライザー|読み取り専用モード|はい|いいえ|
|JavaScript UDF および UDA     |はい         |はい         |Windows のみ         |
|Machine Learning Service     |はい        |はい         |いいえ         |
|Machine Learning Studio     |はい。ただし、クエリはテストできません        |はい |いいえ         |
|互換性レベル     |1.0</br>1.1</br>1.2 (既定値)         |1.0</br>1.1</br>1.2 (既定値)           |1.0</br>1.1</br>1.2 (既定値)           |
|組み込みの ML ベースの異常検出関数     |はい         |はい         |はい         |
|組み込みの地理空間関数     |はい         |はい         |はい         |



## <a name="iot-edge-job-features"></a>IoT Edge ジョブの機能

|機能  |ポータル  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|ジョブの作成     |はい         |はい         |いいえ         |
|ソース管理     |いいえ         |はい         |いいえ         |
|ジョブのローカル プロジェクトへのエクスポート     |いいえ         |はい         |いいえ         |
|サンプル ファイルを使用したクエリのテスト     |はい         |はい         |いいえ         |
|複数のクエリでの入力と出力の共有     |いいえ         |はい         |いいえ         |
|C# UDF     |いいえ         |はい         |いいえ         |
|ジョブの送信     |はい         |はい         |いいえ         |
|ジョブを一覧表示してジョブ エンティティを表示する     |はい         |はい         |いいえ         |
|ジョブ メトリックとジョブ ダイアグラムの表示     |はい         |部分的         |いいえ         |
|ジョブの実行時エラーの表示     |はい         |部分的         |いいえ         |
|CI/CD のサポート     |いいえ         |いいえ         |いいえ         |


## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics on IoT Edge](stream-analytics-edge.md)
* [チュートリアル:Azure Stream Analytics IoT Edge ジョブの C# ユーザー定義関数を記述する (プレビュー)](stream-analytics-edge-csharp-udf.md)
* [Visual Studio Tools を使用して Stream Analytics IoT Edge ジョブを作成する](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Visual Studio を使用して Azure Stream Analytics ジョブを表示する](stream-analytics-vs-tools.md)
* [Explore Azure Stream Analytics with Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md) (Visual Studio Code で Azure Stream Analytics の詳細を確認する (プレビュー))


