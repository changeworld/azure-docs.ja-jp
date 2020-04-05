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
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 2f12cf303c58f0fa614c59ffe643c6c2ee5d2415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246183"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューのデータ ストレージおよびイングレス

この記事では、Azure Time Series Insights プレビューのデータ ストレージとイングレスに対する更新について説明します。 基になるストレージ構造、ファイル形式、Time Series ID プロパティについて説明します。 また、基になるイングレス プロセス、ベスト プラクティス、現在のプレビューの制限事項についても説明します。

## <a name="data-ingress"></a>データのイングレス

Azure Time Series Insights 環境には、時系列データを収集、処理、格納するための*インジェスト エンジン*が含まれています。 

[環境を計画する](time-series-insights-update-plan.md)ときは、すべての着信データが確実に処理されるようにし、高いイングレス スケールを実現し、*インジェストの待機時間* (Time Series Insights でイベント ソースからデータが読み取られて処理されるまでに要する時間) を最小限に抑えるために考慮すべき検討事項がいくつかあります。

Time Series Insights プレビューのデータ イングレス ポリシーにより、データのソースにすることができる場所と、データに必要な形式が決まります。

### <a name="ingress-policies"></a>イングレス ポリシー

*データのイングレス*には、データが Azure Time Series Insights プレビュー環境に送信される方法が含まれています。 

主な構成、書式設定、ベストプラクティスを以下にまとめています。

#### <a name="event-sources"></a>イベント ソース

Azure Time Series Insights プレビューでは、次のイベント ソースがサポートされています。

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights プレビューでは、インスタンスごとに最大で 2 つのイベント ソースがサポートされています。

> [!IMPORTANT] 
> * プレビュー環境にイベント ソースをアタッチすると、初期の待機時間が長くなることがあります。 
> イベント ソースの待機時間は、現在 IoT Hub またはイベント ハブにあるイベントの数によって変わります。
> * 最初にイベント ソース データが取り込まれた後は、待機時間が短くなります。 長い待機時間が継続する場合は、Azure portal からサポート チケットを送信してください。

#### <a name="supported-data-format-and-types"></a>サポートされるデータの形式と型

Azure Time Series Insights では、Azure IoT Hub または Azure Event Hubs から送信される UTF8 でエンコードされた JSON がサポートされます。 

サポートされるデータ型は次のとおりです。

| データ型 | 説明 |
|---|---|
| **bool** | 2 つの状態 (`true` または `false`) のいずれかを持つデータ型。 |
| **dateTime** | 特定の時点を表します。通常、日時形式で表されます。 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 形式で表されます。 |
| **double** | 倍精度 64 ビットの [IEEE 754](https://ieeexplore.ieee.org/document/8766229) 浮動小数点。 |
| **string** | Unicode 文字で構成されるテキスト値。          |

#### <a name="objects-and-arrays"></a>オブジェクトと配列

オブジェクトや配列などの複合型はイベント ペイロードの一部として送信できますが、格納時にはデータのフラット化プロセスが実行されます。 

JSON イベントの調整方法、複合型の送信方法、入れ子になったオブジェクトのフラット化の詳細については、計画と最適化を支援する[イングレスとクエリのための JSON の調整方法](./time-series-insights-update-how-to-shape-events.md)に関する記事を参照してください。

### <a name="ingress-best-practices"></a>イングレスのベスト プラクティス

次のベスト プラクティスのようにすることをお勧めします。

* 待機時間が発生する可能性を減らすために、Azure Time Series Insights と IoT Hub またはイベント ハブを同じリージョンに構成します。

* 予想されるインジェスト レートを計算し、以下に示すサポートされるレート内に収まることを確認することで、[スケールのニーズに合うように計画](time-series-insights-update-plan.md)します。

* [イングレスとクエリに対して JSON を整形する方法](./time-series-insights-update-how-to-shape-events.md)に関する記事を読み、Json データを最適化して整形する方法と、プレビューでの現在の制限事項について理解します。

### <a name="ingress-scale-and-preview-limitations"></a>イングレス スケールとプレビューの制限事項 

Azure Time Series Insights プレビューのイングレスの制限事項について以下で説明します。

> [!TIP]
> プレビューのすべての制限の包括的な一覧については、[プレビュー環境の計画](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)に関する記事を参照してください。

#### <a name="per-environment-limitations"></a>環境ごとの制限

一般に、イングレス レートの要因としては、組織内のデバイスの数、イベント出力の頻度、各イベントのサイズがあります。

*  **デバイスの数** × **イベント出力の頻度** × **各イベントのサイズ**。

既定では、Time Series Insights プレビューは、**Time Series Insights 環境ごとに最大 1 MB/秒 (MBps)** の速度で受信データを取り込むことができます。

> [!TIP] 
> * 要求により、環境でサポートされる取り込み速度を最大 16 MBps に指定できます。
> * より高いスループットが必要な場合は、Azure portal からサポート チケットを送信してお問い合わせください。
 
* **例 1:**

    Contoso Shipping には、1 分につき 3 回イベントを発生させる 100,000 個のデバイスがあります。 1 つのイベントのサイズは 200 バイトです。 それらのデバイスは、4 つのパーティションを持つイベント ハブを Time Series Insights イベント ソースとして使用しています。

    * それらの Time Series Insights 環境のインジェスト率は次のようになります。**100,000 デバイス * 200 バイト/イベント * (3/60 イベント/秒) = 1 MBps**。
    * パーティションあたりのインジェスト率は 0.25 MBps です。
    * Contoso Shipping のインジェスト率は、プレビュー スケール制限内になります。

* **例 2:**

    Contoso Fleet Analytics には、毎秒 1 つのイベントを発生させる 60,000 個のデバイスがあります。 それらのデバイスは、IoT Hub 24 パーティション数 4 を Time Series Insights イベント ソースとして使用しています。 1 つのイベントのサイズは 200 バイトです。

    * 環境のインジェスト率は次のようになります。**20,000 デバイス * 200 バイト/イベント * 1 イベント/秒 = 4 MBps**。
    * パーティションあたりの率は 1 MBps です。
    * Contoso Fleet Analytics では、それらの環境のインジェスト率を高めるために、Azure portal 経由で Time Series Insights に要求を送信することができます。

#### <a name="hub-partitions-and-per-partition-limits"></a>ハブのパーティションとパーティションごとの制限

Time Series Insights 環境を計画するときは、Time Series Insights に接続するイベント ソースの構成を考慮することが重要です。 Azure IoT Hub と Event Hubs はどちらもパーティションを使用して、イベント処理のための水平スケールを有効にします。 

*パーティション*は、ハブで保持されている順序付けされた一連のイベントです。 パーティション数は、ハブの作成フェーズ中に設定され、変更することはできません。 

Event Hubs のパーティション分割のベスト プラクティスについては、「[パーティションはいくつ必要ですか。](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)」を参照してください。

> [!NOTE]
> Azure Time Series Insights で使用されるほとんどの IoT Hub には、4 つのパーティションのみが必要です。

Time Series Insights 環境用の新しいハブを作成する場合でも、既存のハブを使用する場合でも、パーティションごとのインジェスト率を計算して、それがプレビューの制限内にあるかどうかを判断する必要があります。 

Azure Time Series Insights プレビューには現在、一般的な **0.5 MBps のパーティションごとの制限**があります。

#### <a name="iot-hub-specific-considerations"></a>IoT Hub 固有の考慮事項

デバイスは、IoT Hub に作成されるとパーティションに永続的に割り当てられます。 これにより、IoT Hub ではイベントの順序を保証できます (この割り当ては変更されないため)。

固定パーティション割り当ては、IoT Hub のダウンストリームから送信されるデータを取り込んでいる Time Series Insights インスタンスにも影響します。 複数のデバイスからのメッセージが同じゲートウェイ デバイス ID を使用してハブに転送されると、それらは同じパーティションに同時に到着することがあるため、パーティションごとのスケール制限を超える可能性があります。 

**影響**:

* 1 つのパーティションでプレビューの制限を超えるインジェスト率が継続的に発生している場合は、IoT Hub のデータ保有期間を超える前に Time Series Insights がすべてのデバイス テレメトリを同期しない可能性があります。 その結果、インジェストの制限を一貫して超えている場合、送信されたデータが失われる可能性があります。

この状況を軽減するために、次のベスト プラクティスが推奨されます。

* ソリューションをデプロイする前に、環境ごとおよびパーティションごとのインジェスト率を計算します。
* IoT Hub のデバイスを可能な限り負荷分散するようにします。

> [!IMPORTANT]
> IoT Hub をイベント ソースとして使用している環境では、使用中のハブ デバイスの数を使ってインジェスト率を計算し、プレビューでのパーティションあたりの制限が確実に 0.5 MBps 未満になるようにします。
> * 複数のイベントが同時に到着した場合でも、プレビューの制限を超えなくなります。

  ![IoT Hub パーティションのダイアグラム](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

ハブのスループットとパーティションの最適化の詳細については、次のリソースを参照してください。

* [IoT Hub のスケール](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [イベント ハブのスケール](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [イベント ハブのパーティション](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>データ ストレージ

Time Series Insights プレビューの*従量課金制* (PAYG) SKU 環境を作成するときは、次の 2 つの Azure リソースを作成します。

* ウォーム データ ストレージ用に構成できる Azure Time Series Insights プレビュー環境。
* コールド データ ストレージ用の Azure Storage General Purpose V1 BLOB アカウント。

ウォーム ストア内のデータは、[Time Series Query](./time-series-insights-update-tsq.md) および [Azure Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)を使用することによってのみ使用できます。 ウォーム ストアには、Time Series Insights 環境の作成時に選択された[保有期間](./time-series-insights-update-plan.md#the-preview-environment)内の最新のデータが含まれます。

Time Series Insights プレビューを使うと、コールド ストア データが [Parquet ファイル形式](#parquet-file-format-and-folder-structure)で Azure Blob Storage に保存されます。 Time Series Insights プレビューでは、このコールド ストア データが排他的に管理されますが、標準の Parquet ファイルとして直接読み取ることができます。

> [!WARNING]
> コールド ストア データが存在する Azure Blob ストレージ アカウントの所有者は、アカウント内のすべてのデータに対するフルアクセス権を持っています。 このアクセスには、書き込みおよび削除のアクセス許可が含まれます。 データが失われる原因になる可能性があるため、Time Series Insights プレビューによって書き込まれたデータを編集または削除しないでください。

### <a name="data-availability"></a>データの可用性

Azure Time Series Insights プレビューでは、最適なクエリ パフォーマンスのために、データのパーティション分割とインデックス付けが行われます。 データは、インデックスが作成された後、ウォーム ストア (有効な場合) とコールド ストアの両方からクエリを実行できるようになります。 取り込まれたされるデータの量は、この可用性に影響を与える可能性があります。

> [!IMPORTANT]
> プレビュー中は、データが利用可能になるまでに最大 60 秒の期間が発生することがあります。 待機時間が 60 秒を大きく上回る場合は、Azure portal を通じてサポート チケットを送信してください。

## <a name="azure-storage"></a>Azure Storage

このセクションでは、Azure Time Series Insights プレビューに関連する Azure Storage の詳細について説明します。

Azure Blob Storage の詳細については、[Storage Blob の概要](../storage/blobs/storage-blobs-introduction.md)に関する記事をご覧ください。

### <a name="your-storage-account"></a>ストレージ アカウント

Azure Time Series Insights プレビューの PAYG 環境を作成すると、Azure Storage General Purpose V1 BLOB アカウントが、長期的なコールド ストアとして作成されます。  

Azure Time Series Insights プレビューでは、Azure Storage アカウントで、各イベントごとに最大 2 つのコピーが保持されます。 1 つのコピーには、イベントがインジェスト時間によって並べ替えられて格納され、常に時間順に並べられた一連のイベントにアクセスできるようになります。 時間の経過と共に、Time Series Insights プレビューでは、高パフォーマンスの Time Series Insights クエリに最適化するために、データの再パーティション分割コピーも作成されます。 

パブリック プレビュー中、データは、Azure Storage アカウントに無期限に格納されます。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights BLOB の作成と編集

クエリのパフォーマンスとデータの可用性を確保するため、Time Series Insights プレビューによって作成されたすべての BLOB を編集または削除しないでください。

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Time Series Insights プレビューのコールド ストア データへのアクセス 

[Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)および [Time Series Query](./time-series-insights-update-tsq.md) からデータにアクセスするだけでなく、コールド ストアに格納されている Parquet ファイルから直接データにアクセスすることもできます。 たとえば、Jupyter Notebook でデータの読み取り、変換、クレンジングを行った後、それを使用して同じ Spark ワークフローで Azure Machine Learning モデルをトレーニングできます。

Azure Storage アカウントから直接データにアクセスするには、Time Series Insights プレビュー データを格納するために使用しているアカウントへの読み取りアクセスが必要です。 次に、「[Parquet ファイル形式](#parquet-file-format-and-folder-structure)」セクションで説明されている `PT=Time` フォルダーにある Parquet ファイルの作成時刻に基づいて、選択したデータを読み取ることができます。  ストレージ アカウントへの読み取りアクセスを有効にする方法の詳細については、[ストレージ アカウント リソースへのアクセスの管理](../storage/blobs/storage-manage-access-to-resources.md)に関するページを参照してください。

#### <a name="data-deletion"></a>データの削除

Time Series Insights プレビューのファイルは削除しないでください。 関連するデータは、Time Series Insights プレビュー内からのみ管理します。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet ファイル形式とフォルダー構造

Parquet は、効率的なストレージとパフォーマンスのために設計されている、オープンソースの列指向ファイル形式です。 Time Series Insights プレビューでは、Parquet を使用して、大規模な時系列 ID ベースのクエリ パフォーマンスを実現します。  

Parquet ファイルの種類の詳細については、[Parquet のドキュメント](https://parquet.apache.org/documentation/latest/)を参照してください。

Time Series Insights プレビューでは、次のようにデータのコピーが格納されます。

* 1 つ目の初期コピーは、インジェスト時間によって分割され、データは到着順にほぼ格納されます。 データは次のような `PT=Time` フォルダーにあります。

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 2 つ目の再パーティション分割されるコピーは、時系列 ID に応じてグループ化され、`PT=TsId` フォルダーに格納されます。

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

どちらの場合も、Parquet ファイルの time プロパティは BLOB の作成時刻に対応します。 `PT=Time` フォルダー内のデータは、一旦ファイルに書き込まれると、変更されずに保持されます。 `PT=TsId` フォルダー内のデータは、時間の経過と共にクエリに合わせて最適化され、静的ではありません。

> [!NOTE]
> * `<YYYY>` は、4 桁の年表記に対応します。
> * `<MM>` は、2 桁の月表記に対応します。
> * `<YYYYMMDDHHMMSSfff>` は、4 桁の年 (`YYYY`)、2 桁の月 (`MM`)、2 桁の日 (`DD`)、2 桁の時 (`HH`)、2 桁の分 (`MM`)、2 桁の秒 (`SS`)、3 桁のミリ秒 (`fff`) のタイムスタンプ表現に対応します。

Time Series Insights プレビューのイベントは、次のように、Parquet ファイル コンテンツにマップされます。

* 各イベントは、1 行にマップされます。
* すべての行には、イベントのタイムスタンプの **timestamp** 列が含まれています。 タイムスタンプ プロパティが null になることはありません。 イベント ソースでタイムスタンプ プロパティが指定されていない場合の既定値は、**イベント ソース エンキュー時刻**になります。 格納されているタイムスタンプは、常に UTC 形式です。
* すべての行には、Time Series Insights 環境の作成時に定義される時系列 ID (TSID) 列が含まれます。 TSID プロパティ名には、サフィックス `_string` が含まれます。
* テレメトリ データとして送信される他のすべてのプロパティは、プロパティの型に応じて、`_string` (文字列)、`_bool` (ブール値)、`_datetime` (datetime)、または `_double` (double) で終わる列名にマップされます。
* このマッピング スキーマは、ファイル形式の最初のバージョンに適用され、**V = 1** として参照され、同じ名前のベース フォルダーに格納されます。 この機能が進化するにつれて、このマッピング スキーマが変更され、参照名が増える可能性があります。

## <a name="next-steps"></a>次のステップ

- [イングレスとクエリのための JSON の調整方法](./time-series-insights-update-how-to-shape-events.md)に関するページをお読みください。

- 新しい[データ モデリング](./time-series-insights-update-tsm.md)についてご覧ください。
