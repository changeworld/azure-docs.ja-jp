---
title: インクルード ファイル
description: インクルード ファイル
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: cf07d19966c08a63b9aa50475622aa0a1e5e1600
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236517"
---
## <a name="business-disaster-recovery"></a>ビジネスのディザスター リカバリー

このセクションでは、障害が発生した場合でもアプリやサービスを実行し続ける Azure Time Series Insights の機能 (**ビジネス ディザスター リカバリー**) について説明します。

### <a name="high-availability"></a>高可用性

Time Series Insights は、Azure サービスとして、Azure リージョン レベルでの冗長性を利用して特定の**高可用性**機能を提供します。 たとえば、Microsoft Azure では、Azure の**複数のリージョンにわたる可用性**機能を通じて、ディザスター リカバリー機能がサポートされています。

Azure を通じて提供される (および、すべての Time Series Insights インスタンスで利用可能な) 追加の高可用性機能として、次のようなものがあります。

1. **フェールオーバー**:Azure では [geo レプリケーションと負荷分散](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)が提供されます。
1. **データ復元**と**ストレージ復旧**: Azure では、[データを保持および復旧するためのいくつかのオプション](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)が提供されます。
1. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) を通じた**サイト復旧**機能。

デバイスやユーザーにグローバルで複数のリージョンにわたる高可用性を提供するには、これらの Azure 機能を有効にしてください。

> [!NOTE]
> Azure が**複数のリージョンにわたる可用性**を有効にするように構成されている場合、Azure Time Series Insights 内でさらに複数のリージョンにわたる可用性を構成する必要はありません。

### <a name="iot-and-event-hubs"></a>IoT ハブとイベント ハブ

次のような一部の Azure IoT サービスにも、組み込みのビジネス ディザスター リカバリー機能が含まれています。

1. リージョン内冗長性が含まれている [IoT Hub の高可用性ディザスター リカバリー](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)。
1. [Event Hub ポリシー](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)。
1. [Azure Storage の冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

Time Series Insights をこれらのサービスと統合すると、ディザスター リカバリーの可能性が向上します。 たとえば、Event Hub に送信されたテレメトリが、バックアップ Azure Blob Storage データベースに保持される場合があります。

### <a name="time-series-insights"></a>Time Series Insights

Time Series Insights のデータ、アプリ、およびサービスが中断された場合でも、それらの実行を維持するためのいくつかの方法があります。 また、Azure Time Series 環境の完全な複製バックアップ コピーが必要であると判断する場合もあります。

1. データとトラフィックのリダイレクト先となる TSI 固有の**フェールオーバー インスタンス**として。
1. 監査とデータ保持の目的で。

一般に、TSI 環境を複製するための最善の方法は、バックアップ Azure リージョンに 2 番目の TSI 環境を作成することです。 イベントは、プライマリ イベント ソースからこのセカンダリ環境にも送信されます。 必ず、2 つ目の専用のコンシューマー グループを使用し、そのソースのビジネス ディザスター リカバリー ガイドライン (上記のとおり) に従ってください。

具体的には、重複する環境を作成するために、以下の操作を行います。

1. 2 つ目のリージョンに環境を作成します (「[Azure portal で新しい Time Series Insights 環境を作成する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)」)。
1. イベント ソースの 2 つ目の専用コンシューマー グループを作成します。
1. そのイベント ソースを新しい環境に接続し、2 つ目の専用のコンシューマー グループを必ず指定してください。
1. Time Series Insights の [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) と [Event Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) のドキュメントを参照します。

最後に、次の操作を行います。

* 障害インシデントの発生時にプライマリ リージョンが影響を受けた場合は、運用をバックアップの Time Series Insights 環境に経路変更します。
* 2 つ目のリージョンを使用して、すべての TSI テレメトリとクエリ データをバックアップおよび復元します。

> [!IMPORTANT]
> * フェールオーバーの際に遅延が発生する可能性があることに注意してください。
> * フェールオーバーによって、操作が再ルーティングされるため、メッセージの処理に瞬間的なスパイクが発生する可能性もあります。
> * 詳細については、[Time Series Insights の待機時間の短縮](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)に関するページを参照してください。