---
title: Azure Digital Twins とは
titleSuffix: Azure Digital Twins
description: サービスの構成や広範なクラウド ソリューションでの使用方法など、Azure Digital Twins の概要。
author: baanders
ms.author: baanders
ms.date: 10/5/2021
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 818f001666baa55c1c015974932a5d291a82614d
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134148"
---
# <a name="what-is-azure-digital-twins"></a>Azure Digital Twins とは

**Azure Digital Twins** は、環境全体 (建物、工場、農場、エネルギー ネットワーク、鉄道、スタジアム、さらには都市全体など) のデジタル モデル に基づいてツイン グラフを作成できるようにするサービスとしてのプラットフォーム (PaaS) です。 これらのデジタル モデルを使用して、より優れた製品、最適化された操作、コストの削減、および画期的なカスタマー エクスペリエンスを実現する分析情報を得ることができます。

Azure Digital Twins を使用すると、より広範なクラウド ソリューションでの実際の IoT デバイスを表し、ライブ データを送受信するために IoT Hub デバイス ツインに接続するデジタル ツイン アーキテクチャを設計できます。

> [!NOTE]
> "*IoT Hub デバイス ツイン*" は、Azure Digital Twins サービスの "*デジタル ツイン*" とは異なります。 "*IoT Hub デバイス ツイン*" は接続先の各 IoT デバイスの IoT ハブによって保持されますが、"*デジタル ツイン*" はデジタル モデルによって定義され、Azure Digital Twins 内でインスタンスが作成されるすべてのものを表すことができます。 

Azure Digital Twins に加えて特定分野の専門知識を活用し、次のようなカスタマイズされた接続済みのソリューションを構築します。
* あらゆる環境をモデル化し、デジタル ツインをスケーラブルで安全な方法で実現する
* IoT デバイスや既存のビジネス システムなどの資産を接続する
* 堅牢なイベント システムを使用して動的なビジネス ロジックとデータ処理を構築する
* Azure のデータ、分析、AI サービスと統合して、過去の追跡と将来の予測を支援する

## <a name="azure-digital-twins-capabilities"></a>Azure Digital Twins の機能

Azure Digital Twins で提供される機能の概要を次に示します。

### <a name="open-modeling-language"></a>オープン モデリング言語

Azure Digital Twins では、[モデル](concepts-models.md)と呼ばれるカスタム ツイン型を使用して、物理環境の人、場所、および物を表すデジタルエン ティティを定義します。 

これらのモデル定義は、ビジネスを説明するための特殊なボキャブラリと考えることができます。 たとえば、ビルの管理ソリューションでは、建物、階数、エレベーターなどのモデルを定義できます。 その後、これらのモデルに基づいて **デジタル ツイン** を作成して、特定の環境を表すことができます。

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

モデルは、[Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) と呼ばれる JSON に似た形式の言語で定義されており、状態プロパティ、テレメトリ イベント、コマンド、コンポーネント、およびリレーションシップによってツインを記述します。
* モデルは、エンティティ間のセマンティック **リレーションシップ** を定義して、それらのやり取りを反映するグラフにツインを接続できるようにします。 モデルは自分の環境を説明する名詞で、リレーションシップは動詞として考えることができます。
* モデルの継承を使用してツインを特殊化することもできます。 一方のモデルがもう一方のモデルから継承できます。

DTDL は、[IoT プラグ アンド プレイ](../iot-develop/overview-iot-plug-and-play.md)および [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) などの他の Azure IoT サービス全体でデータ モデルに使用されています。 この種類の共通性により、Azure Digital Twins ソリューションの接続と、Azure エコシステムの他の部分との互換性を維持できます。

### <a name="live-execution-environment"></a>ライブ実行環境

Azure Digital Twins のデジタル モデルは、現実世界のライブの最新の表現です。 カスタム DTDL モデルのリレーションシップを使用して、ご自分の環境を表す **ライブ グラフ** にツインを接続します。

Azure Digital Twins グラフは [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) で視覚化できます。これには、グラフと対話するための次のインターフェイスが用意されています。

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="デジタル ツインを表すノードのグラフが表示された Azure Digital Twins Explorer のスクリーンショット。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

Azure Digital Twins では、そのグラフをデータ処理とビジネス ロジックで最新の状態に保つための豊富な **イベント システム** を提供しています。 [Azure Functions](../azure-functions/functions-overview.md) などの外部のコンピューティング リソースを接続して、このデータ処理を柔軟でカスタマイズされた方法で行うことができます。

Azure Digital Twins の強力な **クエリ API** を使用して、ライブ実行環境から分析情報を抽出することもできます。 この API を使用すると、プロパティ値、リレーションシップ、リレーションシップ プロパティ、モデル情報など、豊富な検索条件を使用してクエリを実行できます。 また、クエリを結合して、ご自分の環境に関する幅広い分析情報を収集し、ご自分にとって重要なカスタムの質問に答えることもできます。

### <a name="input-from-iot-and-business-systems"></a>IoT およびビジネス システムからの入力

Azure Digital Twins のライブ実行環境を実際の最新の状態に保つために、[IoT Hub](../iot-hub/about-iot-hub.md) を使用して、ソリューションを IoT および IoT Edge デバイスに接続することができます。 これらのハブで管理されたデバイスはツイン グラフの一部として表され、モデルを駆動するデータを提供します。

Azure Digital Twins を使用してこの目的のための新しい IoT Hub を作成したり、既に管理しているデバイスと共に既存の IoT Hub を接続したりすることができます。

また、REST API や [Logic Apps](../logic-apps/logic-apps-overview.md) などのその他のサービスへのコネクタを使用して、他のデータ ソースから Azure Digital Twins を駆動することもできます。

### <a name="output-to-adx-tsi-storage-and-analytics"></a>ADX、TSI、ストレージ、分析への出力

Azure Digital Twins モデルのデータは、追加の分析またはストレージのために下流の Azure サービスにルーティングできます。 この機能は、[イベント ハブ](../event-hubs/event-hubs-about.md)、[Event Grid](../event-grid/overview.md)、または [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) を使用してデータ フローを動作させる **イベント ルート** を通じて提供されます。

イベント ルートでは、次のようなことが可能です。
* デジタル ツイン データを ADX に送信し、[Azure Data Explorer (ADX) 用の Azure Digital Twins クエリ プラグイン](concepts-data-explorer-plugin.md)を使用してクエリを実行する
* [Azure Digital Twins を Time Series Insights (TSI) に接続して](how-to-integrate-time-series-insights.md)、各ツインの時系列の履歴を追跡する
* Time Series Insights のタイム シリーズ モデルを Azure Digital Twins のソースと合わせる
* [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md) に Azure Digital Twins データを格納する
* [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) またはその他の Microsoft Data Analytics ツールを使用して Azure Digital Twins データを分析する
* より大きなワークフローと Logic Apps を統合する

このオプションは、Azure Digital Twins が大規模なソリューションに接続し、これらの分析情報を継続的に使用するためのカスタム ニーズをサポートするもう 1 つの方法です。

## <a name="azure-digital-twins-in-a-solution-context"></a>ソリューション コンテキストでの Azure Digital Twins

Azure Digital Twins は、大規模な IoT ソリューションの一部として、他の Azure サービスと組み合わせてよく使用されます。 

Azure Digital Twins を使用した完全なソリューションのサンプル アーキテクチャは、次のようになります。
* Azure Digital Twins サービス インスタンス。 このサービスは、ツイン モデルとツイン グラフをその状態で格納し、イベント処理を調整します。
* モデルの構成、トポロジの作成、ツイン グラフからの分析情報の抽出によって Azure Digital Twins インスタンスを駆動する 1 つ以上のクライアント アプリ。
* Azure Digital Twins によって生成されるイベントや、デバイスなどの接続されたデータ ソースを処理するための 1 つ以上の外部コンピューティング リソース。 コンピューティング リソースを提供する一般的な方法の 1 つは、[Azure Functions](../azure-functions/functions-overview.md) を使用することです。
* デバイス管理と IoT データ ストリーム機能を提供する IoT ハブ。
* ワークフロー統合 ([Logic Apps](../logic-apps/logic-apps-overview.md)、コールド ストレージ、Azure Data Explorer、時系列統合、分析など) のタスクを処理するダウンストリーム サービス。

次の図は、大規模な Azure IoT ソリューションのコンテキスト内で Azure Digital Twins が存在する場所を示しています。

:::image type="content" source="media/overview/solution-context.png" alt-text="入力ソース、出力サービス、およびクライアント アプリと外部コンピューティング リソースとの双方向の通信を示す図。" border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>サービスの制限

Azure Digital Twins の **サービスの制限** については、「[Azure Digital Twins サービスの制限](reference-service-limits.md)」を参照してください。 このリソースは、サービスを操作する際、サービスの機能とレート制限、および必要に応じて調整できる制限を理解するうえで役立つことがあります。

## <a name="terminology"></a>用語

**一般的な IoT 用語** と、Azure Digital Twins を含む Azure IoT サービス全体におけるその使い方の一覧については、[Azure IoT の用語集](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json)を参照してください。 このリソースは、Azure Digital Twins を開始して IoT ソリューションを構築する際に役立つ参考資料となることがあります。

## <a name="next-steps"></a>次のステップ

* 「[Azure Digital Twins Explorer の概要](quickstart-azure-digital-twins-explorer.md)」と「[エンド ツー エンドのソリューションを構築する](tutorial-end-to-end.md)」でサンプル シナリオを参照して Azure Digital Twins の使用方法の詳細について学習します。

* または、[DTDL モデル](concepts-models.md)に関するページで Azure Digital Twins の概念について学びます。