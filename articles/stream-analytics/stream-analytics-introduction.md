---
title: Azure Stream Analytics の概要
description: モノのインターネット (IoT) からのストリーミング データをリアルタイムで分析するのに役立つ管理されたサービスである Stream Analytics について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.workload: data-services
ms.custom: mvc
ms.date: 03/27/2018
ms.openlocfilehash: 7b38548ce3266d74ffd51ce75ffa92bdb4ac24b9
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437622"
---
# <a name="what-is-stream-analytics"></a>Stream Analytics とは

Azure Stream Analytics は、デバイスからの大量のデータ ストリームを分析することができるイベント処理エンジンです。 受信データは、デバイス、センサー、Web サイト、ソーシャル メディア フィード、アプリケーションなどから得ることができます。 また、データ ストリームから情報を抽出し、パターンや関係を把握することもできます。 そうしたパターンを使用して、別のアクション (アラートなど) をダウンストリームでトリガーしたり、レポート ツールに情報をフィードしたり、後で使用できるようにその情報を保存したりすることができます。

Azure Stream Analytics を使用できるいくつかの例を次に示します。 

* デバイス テレメトリに基づくリアルタイム分析および IoT (モノのインターネット) センサー フュージョン
* Web ログ/クリックストリーム分析
* フリート マネジメントや無人車両用の地理空間分析
* 高価な資産のリモート監視と予測メンテナンス
* 販売時点管理データのリアルタイム分析による在庫管理や異常検出

## <a name="how-does-stream-analytics-work"></a>Stream Analytics の動作

Azure Stream Analytics の出発点となるのは、ストリーミング データのソースです。ソースは、Azure Event Hubs や Azure IoT Hub に取り込まれるほか、Azure Blob Storage などのデータ ストアから取り込まれます。 ストリームを分析するには、データのストリーム配信元の入力ソースを指定する Stream Analytics ジョブを作成します。 データやパターン、関係をどのように探し出すかを定義する変換クエリもこのジョブで指定します。 変換クエリには、SQL に似たクエリ言語が活用されます。特定の期間にわたるストリーミング データに対し、この言語を使ってフィルター処理、並べ替え、集計、結合が行われます。 ジョブを実行するときには、イベント順序のオプションを調整したり、集計操作を実行するときの時間枠の期間を調整したりすることができます。

受信データを分析した後は、変換済みデータの出力を指定します。これにより、分析した情報に応じて、処理の内容を制御することができます。 たとえば、次のようなアクションを実行することができます。

* 監視対象キューにデータを送信し、カスタム ワークフローをダウンストリームでトリガーする。
* Power BI ダッシュボードにデータを送信してリアルタイムに視覚化する。
* データを他の Azure Storage サービスにアーカイブする。

次の画像は、Stream Analytics のパイプラインを示したものです。Stream Analytics ジョブでは、入出力されるすべてのデータを使用できるほか、一部のデータを選んで使用することもできます。 この画像は、Stream Analytics に対してデータがどのように送信され、分析され、その後他のアクション (保存、プレゼンテーションなど) に送信されるかを示しています。

![Stream Analytics パイプライン](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

## <a name="key-capabilities-and-benefits"></a>主要な機能と利点

Azure Stream Analytics は、あらゆるジョブの規模に対応するスケーラビリティ、使いやすさ、柔軟性、信頼性を実現する設計になっています。 複数のデータセンターおよびソブリン クラウドにまたがって使用することができます。 次の画像は、Azure Stream Analytics の主な機能を示しています。

![Stream Analytics の主な機能](./media/stream-analytics-introduction/stream_analytics_key_capabilities.png)

## <a name="ease-of-getting-started"></a>簡単に始められる

Azure Stream Analytics は簡単に始めることができます。 ほんの数回のクリックでさまざまなソースやシンクに接続し、エンド ツー エンドのパイプラインを作成することができます。 ストリーミング データ インジェストに関して、Stream Analytics は、[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) や [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) に接続することができます。 また、[Azure Blob Storage](https://docs.microsoft.com/azure/storage/storage-introduction) サービスに接続して、履歴データを取り込むこともできます。 イベント ハブからのデータは、他のデータ ソースや処理エンジンと結び付けることが可能です。 ジョブの入力には、参照データ (静的なデータ、緩やかに変化するデータ) を含めることもでき、その参照データにストリーミング データを結合することで、ルックアップ操作を実行することができます。

Stream Analytics は、[Azure BLOB](https://docs.microsoft.com/azure/storage/storage-introduction)、[Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)、[Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/)、[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) など、さまざまなストレージ システムにジョブの出力をルーティングすることができます。 保存後、Azure HDInsight を使ってバッチ分析を実行したり、出力を別のサービス (イベント ハブなど) に送信して利用したり、出力を [Power BI](https://docs.microsoft.com/power-bi/) に送信して、Power BI のストリーミング API を使ってリアルタイムに視覚化したりすることができます。

## <a name="programmer-productivity"></a>プログラマの生産性

Azure Stream Analytics に使用されているシンプルな SQL ベースのクエリ言語は、移動中のデータを分析するための強力な時間制約によって拡張されています。 ジョブの変換を定義するには、単純な SQL コンストラクトで複雑なテンポラル クエリおよび分析を作成することができる、シンプルな宣言型の [Stream Analytics クエリ言語](https://msdn.microsoft.com/library/azure/dn834998.aspx)を使用します。 Stream Analytics のクエリ言語は、SQL 言語との一貫性が確保されているため、SQL 言語の知識があればすぐにジョブを作成することができます。 ジョブの作成には、Azure PowerShell、[Stream Analytics Visual Studio ツール](stream-analytics-tools-for-visual-studio-install.md)、Azure Resource Manager テンプレートなどの開発者ツールを使用することもできます。 開発者ツールを使用することで、変換クエリをオフラインで開発したり、[CI/CD パイプライン](stream-analytics-tools-for-visual-studio-cicd.md)を使用して Azure にジョブを送ったりすることができます。 

Stream Analytics クエリ言語には、ストリーミング データを分析したり処理したりするための関数が豊富に用意されています。 このクエリ言語では、単純なデータ操作と集計のための関数から、複雑な地理空間関数までサポートされます。 クエリはポータルで編集した後、ライブ ストリームから抽出されたサンプル データを使ってテストすることができます。

クエリ言語の機能は、別の関数を定義したり呼び出したりすることで拡張することができます。 Azure Machine Learning サービスで関数の呼び出しを定義することにより、Azure Machine Learning のソリューションを最大限に活かすことができます。また、JavaScript のユーザー定義関数 (UDF) を組み込んで、Stream Analytics のクエリ内で複雑な計算を行うこともできます。

## <a name="fully-managed"></a>完全管理型 

Azure Stream Analytics は、Azure のフル マネージド サーバーレス (PaaS) プランです。 つまり、目的のジョブを実行するために、自分でハードウェアをプロビジョニングしたりクラスターを管理したりする必要はありません。 ジョブは、Azure Stream Analytics によって完全に管理され、クラウドにおける複雑なコンピューティング クラスターの設定や、ジョブを実行するために必要なパフォーマンス チューニングは Azure Stream Analytics によって行われます。 Azure Event Hubs や Azure IoT Hub と統合することで、接続されたデバイス、クリックストリーム、およびログ ファイルから 1 秒間に数百万ものイベントを取り込むことができます。 イベント ハブのパーティション機能を使用すると、計算を論理上のステップに分割できます。スケーラビリティを高めるために、分割した各ステップは、さらに分割できるようになっています。

## <a name="low-total-cost-of-ownership"></a>総保有コストが低い

クラウド サービスである Stream Analytics は、コストに最適化されています。 初期費用はかかりません。課金の対象となるのは、[利用ストリーミング ユニット](stream-analytics-streaming-unit-consumption.md)と処理したデータの量だけです。 コミットメントやクラスターのプロビジョニングは不要です。 実際のビジネス ニーズに応じてストリーミング ジョブをスケールアップしたりスケールダウンしたりすることができます。 

## <a name="reliability"></a>信頼性 

マネージド サービスとして、Stream Analytics は 99.9% の可用性でイベント処理を保証し、データの損失を防ぎ、ビジネス継続性を実現します。詳細については、[Stream Analytics の SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) に関するページを参照してください。 Stream Analytics は、1 秒間に何百万というイベントを処理できるため、短い待ち時間で結果を配信することができます。
厳密には、Stream Analytics で保証されるイベントの処理は 1 回、イベントの配信は 1 回以上となります。 イベントの配信に失敗した場合のために、組み込みの回復機能が備わっています。 Stream Analytics は、内部にジョブの状態を維持できるため、最後の出力時点からジョブを開始することができます。同じ結果を常に出力することで繰り返し可能な結果を提供します。 根本原因の分析を実行する際には、Stream Analytics が持つこの機能によって、過去にさかのぼって計算処理を調査することができます。 

## <a name="performance"></a>[パフォーマンス]

Azure Stream Analytics は、高パフォーマンスを実現するように最適化され、ストリーミング データを処理して、インメモリ計算を実行することができます。 スケールアップまたはスケールダウンによって、リアルタイムの複雑なイベント処理アプリケーションに対応することができます。 Stream Analytics では、パーティション分割による動作がサポートされます。 複雑なクエリを並列化し、複数のストリーミング ノードで実行することができます。 

## <a name="next-steps"></a>次の手順

Azure Stream Analytics の概要は以上です。 次はさらに踏み込んで、初めての Stream Analytics ジョブを作成してみましょう。

* [Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)。
* [Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)。
