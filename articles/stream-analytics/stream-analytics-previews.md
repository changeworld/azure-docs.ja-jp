---
title: Azure Stream Analytics のプレビュー機能
description: この記事では、現在プレビュー段階にある Azure Stream Analytics の機能を示します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: f3838bf6b9f7daa24c0cdb5b0c5a08d41d164530
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561130"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics のプレビュー機能

この記事は、現在プレビュー段階にある Azure Stream Analytics のすべての機能をまとめたものです。 プレビュー機能を運用環境で使用することはお勧めしません。

## <a name="public-previews"></a>パブリック プレビュー

以下の機能はパブリック プレビュー段階です。 これらの機能は現在でも利用できますが、運用環境では使用しないでください。

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Azure Stream Analytics 用の Visual Studio Code (2019 年 5 月リリース)

Visual Studio Code で Azure Stream Analytics のジョブを作成できます。 [VS Code の使用に関するチュートリアル](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)をご覧ください。

### <a name="anomaly-detection"></a>異常検出

Azure Stream Analytics では、"*スパイク*" と "*ディップ*" の検出、および双方向、ゆっくりした増加、ゆっくりした減少の傾向の検出のサポートを備えた、新しい機械学習モデルが導入されています。 詳細については、「[Azure Stream Analytics での異常検出](stream-analytics-machine-learning-anomaly-detection.md)」を参照してください。

### <a name="sql-database-reference-data"></a>SQL Database 参照データ

Azure Stream Analytics では、参照データ入力のソースとして Azure SQL Database がサポートされています。 Stream Analytics ツールを使用して、Azure portal および Visual Studio で Stream Analytics ジョブに対する参照データとして SQL Database を使用できます。 詳細については、[SQL Database からの参照データの Azure Stream Analytics ジョブでの使用](sql-reference-data.md)に関するページを参照してください。

### <a name="integration-with-azure-machine-learning"></a>Azure Machine Learning との統合

Machine Learning (ML) 関数を使用して Stream Analytics のジョブをスケーリングできます。 Stream Analytics ジョブで ML 関数を使用する方法について詳しくは、「[Azure Machine Learning 関数を使用した Stream Analytics ジョブのスケーリング](stream-analytics-scale-with-machine-learning-functions.md)」をご覧ください。 現実のシナリオについては、「[Azure Stream Analytics と Azure Machine Learning を使用した感情分析の実行](stream-analytics-machine-learning-integration-tutorial.md)」を確認してください。

### <a name="javascript-user-defined-aggregate"></a>JavaScript ユーザー定義集計

Azure Stream Analytics は JavaScript で記述されたユーザー定義集計 (UDA) をサポートしています。これを使用すると複雑なステートフルのビジネス ロジックを実装できます。 UDA の使用方法については、「[Azure Stream Analytics の JavaScript ユーザー定義集計](stream-analytics-javascript-user-defined-aggregates.md)」をご覧ください。 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio でのライブ データ テスト

Visual Studio Tools for Azure Stream Analytics ではローカル テスト機能が強化され、イベント ハブや IoT ハブなどのクラウド ソースからのライブ イベント ストリームに対してクエリをテストすることができます。 方法については、「[Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする](stream-analytics-live-data-local-testing.md)」をご覧ください。

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge での .NET ユーザー定義関数

.NET Standard のユーザー定義関数を使用すると、ストリーミング パイプラインの一部として .NET Standard のコードを実行できます。 簡単な C# クラスを作成したり、完全なプロジェクトやライブラリをインポートしたりできます。 Visual Studio では完全な作成およびデバッグ エクスペリエンスがサポートされています。 詳しくは、「[Azure Stream Analytics Edge ジョブの .NET Standard ユーザー定義関数の開発](stream-analytics-edge-csharp-udf-methods.md)」をご覧ください。

## <a name="other-previews"></a>その他のプレビュー

次の機能もプレビューとして提供されています。

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>Azure Stream Analytics on IoT Edge およびクラウド用の C# カスタム デシリアライザー (2019 年 5 月発表)

開発者は、C# でカスタム デシリアライザーを実装して、Azure Stream Analytics で受信したイベントを逆シリアル化できます。 逆シリアル化できる形式としては、Parquet、Protobuf、XML、または任意のバイナリ形式などがあります。 このプレビューへのサインアップは[こちら](https://aka.ms/asapreview1)で行えます。

### <a name="parquet-output-announced-may-2019"></a>Parquet の出力 (2019 年 5 月発表)
Parquet は、ビッグ データの効率的な処理を可能にする列指向の形式です。 データ レイクのデータを Parquet 形式で出力することで、大規模なストリーミング ETL を実現する Azure Stream Analytics を活用して、バッチ処理を実行したり、機械学習アルゴリズムのトレーニングを行ったり、履歴データに対して対話型クエリを実行したりすることができます。 このプレビューへのサインアップは[こちら](https://aka.ms/asapreview1)で行えます。

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Event Hubs とのワンクリック統合 (2019 年 5 月発表) 
この統合により、受信データを視覚化し、Event Hub ポータルからワンクリックで Stream Analytics クエリの作成を開始できるようになります。 クエリの準備ができたら、数回のクリックでその運用を開始して、リアルタイムの洞察が得られるようになります。 これにより、リアルタイム分析ソリューションを開発するコストと時間が大幅に削減されます。 このプレビューへのサインアップは[こちら](https://aka.ms/asapreview1)で行えます。

### <a name="support-for-azure-stack-announced-may-2019"></a>Azure Stack のサポート (2019 年 5 月発表)
この機能は Azure IoT Edge ランタイムで有効になり、Azure Stack 上で実行するローカルの入出力 (Event Hubs、IoT Hub、Blob Storage など) のネイティブ サポートなど、Azure Stack のカスタム機能を活用します。 この新しい統合により、データを生成場所の近くで分析できるハイブリッド アーキテクチャを構築し、待機時間を短縮して分析情報を最大限に活用することができます。
このプレビューへのサインアップは[こちら](https://aka.ms/asapreview1)で行えます。

