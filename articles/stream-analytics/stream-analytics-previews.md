---
title: Azure Stream Analytics のプレビュー機能
description: この記事では、現在プレビュー段階にある Azure Stream Analytics の機能を示します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 637afab45e04c68777f8d1b42817c912cdc09941
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133749"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics のプレビュー機能

この記事は、現在プレビュー段階にある Azure Stream Analytics のすべての機能をまとめたものです。 プレビュー機能を運用環境で使用することはお勧めしません。

## <a name="public-previews"></a>パブリック プレビュー

以下の機能はパブリック プレビュー段階です。 これらの機能は現在でも利用できますが、運用環境では使用しないでください。

### <a name="integration-with-azure-machine-learning"></a>Azure Machine Learning との統合

Machine Learning (ML) 関数を使用して Stream Analytics のジョブをスケーリングできます。 Stream Analytics ジョブで ML 関数を使用する方法について詳しくは、「[Azure Machine Learning 関数を使用した Stream Analytics ジョブのスケーリング](stream-analytics-scale-with-machine-learning-functions.md)」をご覧ください。 現実のシナリオについては、「[Azure Stream Analytics と Azure Machine Learning を使用した感情分析の実行](stream-analytics-machine-learning-integration-tutorial.md)」を確認してください。

### <a name="session-windows"></a>セッション ウィンドウ

Stream Analytics には、ウィンドウ関数に対するネイティブ サポートがあるため、開発者は複雑なストリーム処理ジョブを最小限の作業で作成できます。 [セッション ウィンドウ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics)は、類似した時刻に到着するイベントをグループ化することにより、データが存在しない期間をフィルターで除外します。 ウィンドウ関数について詳しくは、「[Stream Analytics ウィンドウ関数の概要](stream-analytics-window-functions.md)」をご覧ください。

### <a name="blob-output-partitioning-by-custom-time"></a>カスタム日時による BLOB 出力のパーティション分割

Azure Stream Analytics は、カスタム日時属性に基づいて BLOB ストレージに出力できます。 詳しくは、「[Azure Stream Analytics の BLOB ストレージ出力用のカスタム DateTime パス パターン](stream-analytics-custom-path-patterns-blob-storage-output.md)」をご覧ください。

### <a name="javascript-user-defined-aggregate"></a>JavaScript ユーザー定義集計

Azure Stream Analytics は JavaScript で記述されたユーザー定義集計 (UDA) をサポートしています。これを使用すると複雑なステートフルのビジネス ロジックを実装できます。 UDA の使用方法については、「[Azure Stream Analytics の JavaScript ユーザー定義集計](stream-analytics-javascript-user-defined-aggregates.md)」をご覧ください。 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio でのライブ データ テスト

Visual Studio Tools for Azure Stream Analytics ではローカル テスト機能が強化され、イベント ハブや IoT ハブなどのクラウド ソースからのライブ イベント ストリームに対してクエリをテストすることができます。 方法については、「[Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする](stream-analytics-live-data-local-testing.md)」をご覧ください。

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge での .NET ユーザー定義関数

.NET Standard のユーザー定義関数を使用すると、ストリーミング パイプラインの一部として .NET Standard のコードを実行できます。 簡単な C# クラスを作成したり、完全なプロジェクトやライブラリをインポートしたりできます。 Visual Studio では完全な作成およびデバッグ エクスペリエンスがサポートされています。 詳しくは、「[Azure Stream Analytics Edge ジョブの .NET Standard ユーザー定義関数の開発](stream-analytics-edge-csharp-udf-methods.md)」をご覧ください。

## <a name="private-previews"></a>プライベート プレビュー

以下の機能は、プライベート プレビュー段階です。 これらのプレビューを使用するには、Azure Stream Analytics のプライベート プレビュー [サインアップ](https://aka.ms/ASApreview1) ページにアクセスしてください。

### <a name="anomaly-detection"></a>異常検出

Azure Stream Analytics では、"*スパイク*" と "*ディップ*" の検出、および双方向、ゆっくりした増加、ゆっくりした減少の傾向の検出のサポートを備えた、新しい機械学習モデルが導入されています。

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics on IoT Edge 用の C# カスタム デシリアライザー

開発者は、C# でカスタム デシリアライザーを実装して、Azure Stream Analytics で受信したイベントを逆シリアル化できるようになりました。 逆シリアル化できる形式としては、Parquet、Protobuf、XML、または任意のバイナリ形式などがあります。

### <a name="blob-output-partitioning-by-custom-attribute"></a>カスタム属性による BLOB 出力のパーティション分割

クエリの任意の列に基づいて、Azure Stream Analytics の出力を BLOB ストレージにパーティション分割できるようになりました。

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Azure Data Lake Storage に対する Azure リソース認証用のマネージド ID

Azure Data Lake Storage Gen1 に書き込みながら、Azure リソース ベースの認証用のマネージド ID でリアルタイム パイプラインを運用化できるようになり、プログラムでジョブを作成できます。 詳しくは、「[Azure Stream Analytics ジョブを認証するマネージド ID を Azure Data Lake Storage Gen1 出力で使用する](stream-analytics-managed-identities-adls.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics での 8 つの新機能](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Azure Stream Analytics で利用可能になった 4 つの新機能](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
