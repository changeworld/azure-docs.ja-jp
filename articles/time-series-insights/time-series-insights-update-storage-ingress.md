---
title: プレビューのデータ ストレージおよびイングレス - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights プレビューのデータ ストレージおよびイングレスについて説明します。
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: f00529d00312fd6acb045de698590047f991bec7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714304"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューのデータ ストレージおよびイングレス

この記事では、Azure Time Series Insights プレビューのデータ ストレージとイングレスに対する更新について説明します。 基になるストレージ構造、ファイル形式、および Time Series ID プロパティについて取り上げます。 また、基になっているイングレス プロセス、ベスト プラクティス、および現在のプレビューの制限事項についても説明します。

## <a name="data-ingress"></a>データのイングレス

Azure Time Series Insights 環境には、時系列データを収集、処理、格納するためのインジェスト エンジンが含まれています。 環境を計画するときは、すべての着信データが確実に処理されるようにし、高いイングレス スケールを実現し、インジェストの待機時間 (TSI がイベントソースからデータを読み取って処理するのに要する時間) を最小限に抑えるために考慮する検討事項がいくつかあります。 

Time Series Insights プレビューでは、データ イングレス ポリシーにより、データのソースにすることができる場所と、データに必要な形式が決まります。

### <a name="ingress-policies"></a>イングレス ポリシー

#### <a name="event-sources"></a>イベント ソース

Time Series Insights プレビューでは、次のイベント ソースがサポートされています。

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights プレビューでは、インスタンスごとに最大で 2 つのイベント ソースがサポートされています。

> [!WARNING] 
> * プレビュー環境にイベント ソースをアタッチすると、初期の待機時間が長くなることがあります。 
> イベント ソースの待機時間は、現在 IoT Hub またはイベント ハブにあるイベントの数によって変わります。
> * 最初にイベント ソース データが取り込まれた後は、待機時間が短くなります。 長い待機時間が継続する場合は、Azure portal からサポート チケットを送信してお問い合わせください。

#### <a name="supported-data-format-and-types"></a>サポートされるデータの形式と型

Azure Time Series Insights では、Azure IoT Hub または Azure Event Hubs を介して送信される UTF8 でエンコードされた JSON がサポートされます。 

サポートされるデータ型の一覧を次に示します。

| データ型 | 説明 |
|-----------|------------------|-------------|
| [bool]      |   2 つの状態 (true または false) のいずれかを持つデータ型。       |
| dateTime    |   特定の時点を表します。通常、日時形式で表されます。 DateTimes は ISO 8601 形式である必要があります。      |
| double    |   倍精度 64 ビットの IEEE 754 浮動小数点
| string    |   Unicode 文字で構成されるテキスト値。          |

#### <a name="objects-and-arrays"></a>オブジェクトと配列

オブジェクトや配列などの複合型はイベント ペイロードの一部として送信できますが、格納時にはデータのフラット化プロセスが実行されます。 JSON イベントの調整方法と、複合型および入れ子になったオブジェクトのフラット化の詳細については、[イングレスとクエリのための JSON の調整方法](./time-series-insights-update-how-to-shape-events.md)に関するページを参照してください。


### <a name="ingress-best-practices"></a>イングレスのベスト プラクティス

次のベスト プラクティスのようにすることをお勧めします。

* ネットワークで発生するインジェストの遅延を減らすために、Time Series Insights と IoT Hub またはイベント ハブは同じリージョンに構成します。
* 予想されるインジェスト レートを計算し、後で示すサポートされるレート内に収まることを確認することで、スケールのニーズに合うように計画します
* [イングレスとクエリに対して JSON を整形する方法](./time-series-insights-update-how-to-shape-events.md)に関する記事を読み、Json データを最適化して整形する方法と、プレビューでの現在の制限事項について理解します。

### <a name="ingress-scale-and-limitations-in-preview"></a>プレビューでのイングレス スケールと制限

#### <a name="per-environment-limitations"></a>環境ごとの制限

一般に、イングレス レートの要因としては、組織内のデバイスの数、イベント出力の頻度、各イベントのサイズがあります。

*  **デバイスの数** × **イベント出力の頻度** × **各イベントのサイズ**。

既定では、Time Series Insights プレビューは、**TSI 環境ごとに**最大 1 MB/秒 (MBps) で受信データを取り込むことができます。 これがお客様の要件を満たさない場合は、Azure portal でサポート チケットを送信することにより、1 つの環境に対して最大 16 MBps をサポートできます。
 
例 1:Contoso Shipping には、1 分につき 3 回イベントを発生させる 100,000 個のデバイスがあります。 1 つのイベントのサイズは 200 バイトです。 それらは、4 つのパーティションを持つイベント ハブを TSI のイベント ソースとして使用しています。
それらの TSI 環境のインジェスト率は次のようになります。100,000 デバイス * 200 バイト/イベント * (3/60 イベント/秒) = 1 MBps。
パーティションあたりのインジェスト率は 0.25 MBps です。
Contoso Shipping のインジェスト率は、プレビュー スケール制限内になります。
 
例 2:Contoso Fleet Analytics には、毎秒 1 つのイベントを発生させる 60,000 個のデバイスがあります。 それらは、IoT Hub 24 パーティション数 4 を TSI イベント ソースとして使用しています。 1 つのイベントのサイズは 200 バイトです。
環境のインジェスト率は次のようになります。20,000 デバイス * 200 バイト/イベント * 1 イベント/秒 = 4 MBps。
パーティションあたりの率は 1 MBps です。
Contoso Fleet Analytics は、このスケールを達成するための専用環境の要求を Azure portal 経由で TSI に送信する必要があります。

#### <a name="hub-partitions-and-per-partition-limits"></a>ハブのパーティションとパーティションごとの制限

TSI 環境を計画するときは、TSI に接続するイベント ソースの構成を考慮することが重要です。 Azure IoT Hub と Event Hubs はどちらもパーティションを使用して、イベント処理のための水平スケールを有効にします。  パーティションは、ハブで保持される順序付けされた一連のイベントです。 パーティション数は、IoT Hub または Event Hubs の作成フェーズ中に設定され、変更することはできません。 パーティション数の決定の詳細については、Event Hubs の FAQ の「パーティションはいくつ必要ですか」を参照してください。 IoT Hub を使用する TSI 環境では、通常、ほとんどの IoT Hub には 4 つのパーティションのみが必要です。 TSI 環境用の新しいハブを作成するか、既存のハブを使用するかにかかわらず、パーティションごとのインジェスト率を計算し、それがプレビューの制限内にあるかどうかを判断する必要があります。 TSI プレビューには、現在、0.5 MB/秒の**パーティションごとの**制限があります。 以降の例を参照として使用してください。また、IoT Hub ユーザーの場合は、次の IoT Hub 固有の考慮事項に注意してください。

#### <a name="iot-hub-specific-considerations"></a>IoT Hub 固有の考慮事項

デバイスは IoT Hub に作成されるとパーティションに割り当てられます。このパーティション割り当ては変更されません。 そうすることで、IoT Hub はイベント順序を保証することができます。 ただし、これは、TSI にとって、特定のシナリオではダウンストリーム リーダーとしての意味を持ちます。 複数のデバイスからのメッセージが同じゲートウェイ デバイス ID を使用してハブに転送されると、それらは同じパーティションに到着するため、パーティションごとのスケール制限を超える可能性があります。 

**影響**:1 つのパーティションで、持続してプレビュー制限を超えるインジェスト率が発生している場合は、IoT Hub のデータ保有期間を超える前に TSI リーダーが追いつかない可能性があります。 これにより、データが失われます。

次をお勧めします。 

* ソリューションをデプロイする前に、環境ごとおよびパーティションごとのインジェスト率を計算する
* IoT Hub のデバイス (とパーティション) を可能な限り負荷分散するようにする

> [!WARNING]
> IoT Hub をイベント ソースとして使用している環境では、使用中のハブ デバイスの数を使ってインジェスト率を計算し、プレビューでのパーティションあたりの制限が確実に 0.5 MBps 未満になるようにします。

  ![IoT Hub パーティションのダイアグラム](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

スループット ユニットとパーティションの詳細については、次のリンクを参照してください。

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
> プレビュー中は、データが利用可能になるまでに最大 60 秒の期間が発生することがあります。 待機時間が 60 秒を大きく上回る場合は、Azure portal を通じてサポート チケットを送信してください。

## <a name="azure-storage"></a>Azure Storage

このセクションでは、Azure Time Series Insights プレビューに関連する Azure Storage の詳細について説明します。

Azure Blob Storage の詳細については、[Storage Blob の概要](../storage/blobs/storage-blobs-introduction.md)に関する記事をご覧ください。

### <a name="your-storage-account"></a>ストレージ アカウント

Time Series Insights プレビューの従量課金制環境を作成すると、Azure Storage General Purpose V1 BLOB アカウントが、長期的なコールド ストアとして作成されます。  

Time Series Insights プレビューでは、Azure Storage アカウントで、各イベントの最大 2 つのコピーが発行されます。 最初のコピーでは、インジェスト時刻によって並べ替えられたイベントが常に保持されるので、他のサービスを使用してアクセスできます。 Spark、Hadoop、その他の使い慣れたツールを使用して、未加工の Parquet ファイルを処理することができます。 

Time Series Insights プレビューでは、Time Series Insights クエリに対して最適になるように、Parquet ファイルのパーティションが再分割されます。 この再パーティション分割されたデータのコピーも保存されます。

パブリック プレビュー中、データは、Azure Storage アカウントに無期限に格納されます。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights BLOB の作成と編集

クエリのパフォーマンスとデータの可用性を確保するため、Time Series Insights プレビューによって作成されたすべての BLOB を編集または削除しないでください。

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Time Series Insights プレビューからのデータのアクセスとエクスポート

他のサービスと連携して使用するために、Time Series Insights プレビュー エクスプローラーに表示されているデータにアクセスする場合があります。 たとえば、データを使用して、Power BI でレポートを作成したり、Azure Machine Learning Studio で機械学習モデルをトレーニングしたりすることができます。 または、データを使用して、Jupyter ノートブックの変換、視覚化、モデル化を行うことができます。

次の 3 つの一般的な方法でデータにアクセスできます。

* Time Series Insights プレビュー エクスプローラーから。 エクスプローラーからデータを CSV ファイルとしてエクスポートできます。 詳細については、[Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)に関するページを参照してください。
* Get Events クエリを使用して Time Series Insights Preview API から。 この API の詳細については、[Time Series クエリ](./time-series-insights-update-tsq.md)に関するページを参照してください。
* Azure Storage アカウントから直接。 Time Series Insights プレビューのデータへのアクセスに使用するどのアカウントにも、読み取りアクセス権が必要です。 詳細については、[ストレージ アカウント リソースへのアクセスの管理](../storage/blobs/storage-manage-access-to-resources.md)に関するページを参照してください。

#### <a name="data-deletion"></a>データの削除

Time Series Insights プレビューのファイルは削除しないでください。 関連するデータは、Time Series Insights プレビュー内からのみ管理します。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet ファイル形式とフォルダー構造

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

- [イングレスとクエリのための JSON の調整方法](./time-series-insights-update-how-to-shape-events.md)に関するページをお読みください。

- 新しい[データ モデリング](./time-series-insights-update-tsm.md)についてご覧ください。
