---
title: プレビューのデータ ストレージおよびイングレス - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights プレビューのデータ ストレージおよびイングレスについて説明します。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: dada1a8ed8b1725905ee2ad159e385d1bee62fc6
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615092"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューのデータ ストレージおよびイングレス

この記事では、Azure Time Series Insights プレビューのデータ ストレージとイングレスに対する更新について説明します。 基になるストレージ構造、ファイル形式、および Time Series ID プロパティについて取り上げます。 また、基になっているイングレス プロセス、ベスト プラクティス、および現在のプレビューの制限事項についても説明します。

## <a name="data-ingress"></a>データのイングレス

Azure Time Series Insights 環境には、時系列データを収集、処理、格納するためのインジェスト エンジンが含まれています。 環境を計画するときは、すべての着信データが確実に処理されるようにし、高いイングレス スケールを実現し、インジェストの待機時間 (TSI がイベントソースからデータを読み取って処理するのに要する時間) を最小限に抑えるために考慮する検討事項がいくつかあります。 

Time Series Insights プレビューでは、データ イングレス ポリシーにより、データのソースにすることができる場所と、データに必要な形式が決まります。

### <a name="ingress-policies"></a>イングレス ポリシー

Time Series Insights プレビューでは、次のイベント ソースがサポートされています。

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights プレビューでは、インスタンスごとに最大で 2 つのイベント ソースがサポートされています。 Azure Time Series Insights では、Azure IoT Hub または Azure Event Hubs を介して送信された JSON がサポートされています。

> [!WARNING] 
> * プレビュー環境にイベント ソースをアタッチすると、初期の待機時間が長くなることがあります。 
> イベント ソースの待機時間は、現在 IoT Hub またはイベント ハブにあるイベントの数によって変わります。
> * 最初にイベント ソース データが取り込まれた後は、待機時間が短くなります。 継続的に待機時間が長い場合は、Azure portal からサポート チケットを提出してお問い合わせください。

## <a name="ingress-best-practices"></a>イングレスのベスト プラクティス

次のベスト プラクティスのようにすることをお勧めします。

* Time Series Insights と IoT ハブまたはイベント ハブは、同じリージョンに構成します。 これにより、ネットワークに起因するインジェストの待機時間が短縮されます。
* 予想されるインジェスト レートを計算し、後で示すサポートされるレート内に収まることを確認することで、スケールのニーズに合うように計画します
* [イングレスとクエリに対して JSON を整形する方法](./time-series-insights-update-how-to-shape-events.md)に関する記事を読み、Json データを最適化して整形する方法と、プレビューでの現在の制限事項について理解します。

### <a name="ingress-scale-and-limitations-in-preview"></a>プレビューでのイングレス スケールと制限

既定では、プレビュー環境で、**環境あたり最大で毎秒 1 メガバイト (MB/秒)** のイングレス レートをサポートできます。 お客様は、必要に応じて、最大 **16 MB/秒**のスループットまで、プレビュー環境を拡張できます。
また、パーティションごとに **0.5 MB/秒**の制限があります。 

パーティションごとの制限は、IoT Hub を使用するお客様に影響します。 具体的に、IoT Hub デバイスとパーティションとの間に関係があるとします。 1 つのゲートウェイ デバイスが独自のデバイス ID と接続文字列を使用してハブにメッセージを転送しているシナリオでは、イベント ペイロードで異なるタイム シリーズ ID が指定されている場合でも、メッセージが 1 つのパーティションに到着すると、0.5 MB/秒の制限に達する危険があります。 

一般に、イングレス レートの要因としては、組織内のデバイスの数、イベント出力の頻度、各イベントのサイズがあります。

*  **デバイスの数** × **イベント出力の頻度** × **各イベントのサイズ**。

> [!TIP]
> IoT Hub をイベント ソースとして使用する環境では、使用中または組織内のデバイスの合計ではなく、使用中のハブ接続の数を使用して、インジェスト レートを計算します。

スループット ユニット、制限、およびパーティションの詳細については、次を参照してください。

* [IoT Hub のスケール](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [イベント ハブのスケール](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [イベント ハブのパーティション](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>データ ストレージ

Time Series Insights プレビューの従量課金制 SKU 環境を作成するときは、次の 2 つの Azure リソースを作成します。

* 必要に応じてウォーム ストア機能を含めることができる、Time Series Insights プレビュー環境。
* コールド データ ストレージ用の Azure Storage General Purpose V1 BLOB アカウント。

ウォーム ストア内のデータは、[Time Series Query](./time-series-insights-update-tsq.md) および [Azure Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)を使用することによってのみ使用できます。 

Time Series Insights プレビューを使うと、コールド ストア データが [Parquet ファイル形式](#parquet-file-format-and-folder-structure)で Azure Blob Storage に保存されます。 Time Series Insights プレビューでは、このコールド ストア データが排他的に管理されますが、標準の Parquet ファイルとして直接読み取ることができます。

> [!WARNING]
> コールド ストア データが存在する Azure Blob ストレージ アカウントの所有者は、アカウント内のすべてのデータに対するフルアクセス権を持っています。 このアクセスには、書き込みおよび削除のアクセス許可が含まれます。 データが失われる原因になる可能性があるため、Time Series Insights プレビューによって書き込まれたデータを編集または削除しないでください。

### <a name="data-availability"></a>データの可用性

Time Series Insights プレビューでは、最適なクエリ パフォーマンスのために、データのパーティション分割とインデックス付けが行われます。 インデックス付けが済むと、クエリでデータを使用できるようになります。 取り込まれたされるデータの量は、この可用性に影響を与える可能性があります。

> [!IMPORTANT]
> 予定されている Time Series Insights の一般提供 (GA) リリースでは、データは、イベント ソースから読み取られた後 60 秒以内に利用できるようになります。 プレビュー期間中は、データが利用可能になるまでにさらに時間がかかることがあります。 待機時間が 60 秒を大きく上回る場合は、Azure portal を通じてサポート チケットを送信してください。

## <a name="azure-storage"></a>Azure Storage

このセクションでは、Azure Time Series Insights プレビューに関連する Azure Storage の詳細について説明します。

Azure Blob Storage の詳細については、[Storage Blob の概要](../storage/blobs/storage-blobs-introduction.md)に関する記事をご覧ください。

### <a name="your-storage-account"></a>ストレージ アカウント

Time Series Insights プレビューの従量課金制環境を作成すると、Azure Storage General Purpose V1 BLOB アカウントが、長期的なコールド ストアとして作成されます。  

Time Series Insights プレビューでは、Azure Storage アカウントで、各イベントの最大 2 つのコピーが発行されます。 最初のコピーでは、インジェスト時刻によって並べ替えられたイベントが常に保持されるので、他のサービスを使用してアクセスできます。 Spark、Hadoop、その他の使い慣れたツールを使用して、未加工の Parquet ファイルを処理することができます。 

Time Series Insights プレビューでは、Time Series Insights クエリに対して最適になるように、Parquet ファイルのパーティションが再分割されます。 この再パーティション分割されたデータのコピーも保存されます。

パブリック プレビュー中、データは、Azure Storage アカウントに無期限に格納されます。

### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights BLOB の作成と編集

クエリのパフォーマンスとデータの可用性を確保するため、Time Series Insights プレビューによって作成されたすべての BLOB を編集または削除しないでください。

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Time Series Insights プレビューからのデータのアクセスとエクスポート

他のサービスと連携して使用するために、Time Series Insights プレビュー エクスプローラーに表示されているデータにアクセスする場合があります。 たとえば、データを使用して、Power BI でレポートを作成したり、Azure Machine Learning Studio で機械学習モデルをトレーニングしたりすることができます。 または、データを使用して、Jupyter ノートブックの変換、視覚化、モデル化を行うことができます。

次の 3 つの一般的な方法でデータにアクセスできます。

* Time Series Insights プレビュー エクスプローラーから。 エクスプローラーからデータを CSV ファイルとしてエクスポートできます。 詳細については、[Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)に関するページを参照してください。
* Time Series Insights Preview API から。 API エンドポイントには `/getRecorded` でアクセスできます。 この API の詳細については、[Time Series クエリ](./time-series-insights-update-tsq.md)に関するページを参照してください。
* Azure Storage アカウントから直接。 Time Series Insights プレビューのデータへのアクセスに使用するどのアカウントにも、読み取りアクセス権が必要です。 詳細については、[ストレージ アカウント リソースへのアクセスの管理](../storage/blobs/storage-manage-access-to-resources.md)に関するページを参照してください。

### <a name="data-deletion"></a>データの削除

Time Series Insights プレビューのファイルは削除しないでください。 関連するデータは、Time Series Insights プレビュー内からのみ管理します。

## <a name="parquet-file-format-and-folder-structure"></a>Parquet ファイル形式とフォルダー構造

Parquet は、効率的なストレージとパフォーマンスのために設計されている、オープンソースの列指向ファイル形式です。 Time Series Insights プレビューでは、このような理由で Parquet が使用されています。 データは、大規模なクエリのパフォーマンスのために時系列 ID でパーティション分割されます。  

Parquet ファイルの種類の詳細については、[Parquet のドキュメント](https://parquet.apache.org/documentation/latest/)を参照してください。

Time Series Insights プレビューでは、次のようにデータのコピーが格納されます。

* 1 つ目の初期コピーは、インジェスト時間によって分割され、データは到着順にほぼ格納されます。 データは次のような `PT=Time` フォルダーにあります。

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 2 つ目の再パーティション分割されるコピーは、タイム シリーズ ID のグループ化によってパーティション分割され、`PT=TsId` フォルダーに格納されます。

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

どちらの場合も、時刻値は BLOB の作成時刻に対応します。 `PT=Time` フォルダー内のデータは保持されます。 `PT=TsId` フォルダー内のデータは、時間の経過と共にクエリに合わせて最適化され、静的なままではありません。

> [!NOTE]
> * `<YYYY>` は、4 桁の年表記に対応します。
> * `<MM>` は、2 桁の月表記に対応します。
> * `<YYYYMMDDHHMMSSfff>` は、4 桁の年 (`YYYY`)、2 桁の月 (`MM`)、2 桁の日 (`DD`)、2 桁の時 (`HH`)、2 桁の分 (`MM`)、2 桁の秒 (`SS`)、3 桁のミリ秒 (`fff`) のタイムスタンプ表現に対応します。

Time Series Insights プレビューのイベントは、次のように、Parquet ファイル コンテンツにマップされます。

* 各イベントは、1 行にマップされます。
* すべての行には、イベントのタイムスタンプの **timestamp** 列が含まれています。 タイムスタンプ プロパティが null になることはありません。 イベント ソースでタイムスタンプ プロパティが指定されていない場合の既定値は、**イベント ソース エンキュー時刻**になります。 タイムスタンプは常に UTC です。
* すべての行には、Time Series Insights 環境の作成時に定義される時系列 ID 列が含まれます。 プロパティ名には、サフィックス `_string` が含まれます。
* テレメトリ データとして送信される他のすべてのプロパティは、プロパティの型に応じて、`_string` (文字列)、`_bool` (ブール値)、`_datetime` (datetime)、または `_double` (double) で終わる列名にマップされます。
* このマップ スキームは、**V=1** として参照される、ファイル形式の最初のバージョンに適用されます。 この機能の発展に伴い、名前が増分される可能性があります。

## <a name="next-steps"></a>次のステップ

- [Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)に関するページをご覧ください。

- 新しい[データ モデリング](./time-series-insights-update-tsm.md)についてご覧ください。
