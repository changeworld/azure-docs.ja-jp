---
title: Azure Event Hubs とは - ビッグ データ インジェスト サービス | Microsoft Docs
description: 1 秒間に百万単位のイベントを取り込むビッグ データ ストリーミング サービスである Azure Event Hubs について説明します。
ms.topic: overview
ms.date: 05/25/2021
ms.openlocfilehash: fa32e26439cfd7f2e4319fdb7dc631bfadb023d4
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322150"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs - ビッグ データのストリーミング プラットフォームとなるイベント インジェスト サービス
Azure Event Hubs は、ビッグ データのストリーミング プラットフォームとなるイベント インジェスト サービスです。 1 秒間に何百万ものイベントを受信して処理することができます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。

次のシナリオは、Event Hubs の用途の一例です。

- 異常検出 (不正/外れ値)
- アプリケーションのログ記録
- クリックストリームなどの分析パイプライン
- ライブ ダッシュボード
- データのアーカイブ
- トランザクション処理
- ユーザー利用統計情報処理
- デバイス利用統計情報ストリーミング

## <a name="why-use-event-hubs"></a>Event Hubs を使用する理由
データは、データ ソースを処理してタイムリーに分析情報を取得する簡単な方法があって初めて価値があります。 Event Hubs が提供する分散ストリーム処理プラットフォームは、待ち時間が短く、Azure の内部および外部にあるデータおよび分析サービスとシームレスに統合して、完全なビッグ データ パイプラインを構築します。

Event Hubs はイベント パイプラインの "玄関口" を表し、ソリューション アーキテクチャでは "*イベント インジェスター*" と呼ばれることがよくあります。 イベント取り込みとは、イベント ストリームの生成とそのようなイベントの消費とを分離するために、イベント パブリッシャーとイベント コンシューマーとの間に置かれるコンポーネントやサービスです。 Event Hubs は、時間保持バッファーを備えた統合ストリーミング プラットフォームを提供し、イベント プロデューサーをイベント コンシューマーから切り離します。


以降のセクションで、Azure Event Hubs サービスの主要な機能について説明します。

## <a name="fully-managed-paas"></a>フル マネージド PaaS
Event Hubs は構成または管理のオーバーヘッドがほとんどないフル マネージドの PaaS (サービスとしてのプラットフォーム) なので、ユーザーはビジネス ソリューションに集中できます。 [Apache Kafka エコシステム向けの Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) では PaaS Kafka エクスペリエンスが提供され、独自のクラスターを管理、構成、実行する必要はありません。

## <a name="support-for-real-time-and-batch-processing"></a>リアルタイム処理とバッチ処理のサポート
ストリームの取り込み、バッファー、格納、処理をリアルタイムで行い、アクションにつながる分析情報を取得します。 Event Hubs は[パーティション分割されたコンシューマー モデル](event-hubs-scalability.md#partitions)を使用して、複数のアプリケーションがストリームを同時に処理し、処理の速度を制御できるようにします。 また、Azure Event Hubs は サーバーレス アーキテクチャ用の [Azure Functions](../azure-functions/index.yml) と統合します。

## <a name="capture-event-data"></a>イベント データをキャプチャする
長期保有またはマイクロ バッチ処理のために、ほぼリアルタイムでデータを [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) または [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)  に[キャプチャ](event-hubs-capture-overview.md)します。 この動作は、リアルタイム分析の派生に使用するものと同じストリームで実現できます。 イベント データをキャプチャするための設定に時間はかかりません。 それを実行しても管理コストは発生せず、Event Hubs の [スループット ユニット数](event-hubs-scalability.md#throughput-units)または[処理ユニット数](event-hubs-scalability.md#processing-units)に応じて、自動的にスケーリングされます。 Event Hubs を使用することで、ユーザーはデータ キャプチャではなくデータ処理に専念できます。

## <a name="scalable"></a>拡張性
Event Hubs では、メガバイト単位のデータ ストリームで開始し、ギガバイトまたはテラバイトまで拡張できます。 [自動インフレ](event-hubs-auto-inflate.md)機能は、使用状況のニーズに合わせてスループット ユニット数または処理ユニット数をスケーリングするために利用できる多くのオプションの 1 つです。

## <a name="rich-ecosystem"></a>充実したエコシステム

業界標準の AMQP 1.0 プロトコルに基づき、さまざまな言語 ([.NET](https://github.com/Azure/azure-sdk-for-net/)、[Java](https://github.com/Azure/azure-sdk-for-java/)、[Python](https://github.com/Azure/azure-sdk-for-python/)、[JavaScript](https://github.com/Azure/azure-sdk-for-js/)) で使用可能な広範なエコシステムにより、Event Hubs からのストリームの処理を簡単に開始できます。 サポートされているすべてのクライアント言語が、低レベルの統合を提供します。 また、このエコシステムを通じて、Azure Stream Analytics や Azure Functions などの Azure サービスとシームレスに統合し、サーバーレスのアーキテクチャを構築することができます。

## <a name="event-hubs-for-apache-kafka"></a>Apache Kafka 用 Event Hubs
さらに、[Apache Kafka エコシステム向けの Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) により、[Apache Kafka (1.0 以降)](https://kafka.apache.org/) のクライアントとアプリケーションは Event Hubs と通信できます。 独自の Kafka クラスターや Zookeeper クラスターを設定、構成、管理したり、Azure にネイティブではない "サービスとしての Kafka" オファリングを使用したりする必要はありません。

## <a name="event-hubs-premium-and-dedicated"></a>Event Hubs の Premium と Dedicated 
Event Hubs の **Premium** は、マネージド マルチテナント PaaS 環境での優れたパフォーマンス、待機時間を予測できる優れた分離性、最小限の干渉を必要とする、ハイエンドのストリーミング ニーズに対応します。 Premium レベルには、Standard オファリングのすべての機能に加えて、動的パーティション スケールアップ、保有延長、カスタマー マネージド キーなど、いくつかの追加機能が用意されています。 詳細については、[Event Hubs Premium](event-hubs-premium-overview.md) に関する記事を参照してください。

Event Hubs の **Dedicated** レベルからは、最も厳しいストリーミング ニーズを持つお客様向けのシングルテナント デプロイが提供されます。 このシングルテナントのサービスでは、99.99% の SLA が保証されており、専用の価格レベルでのみご利用いただけます。 Event Hubs クラスターでは、1 秒あたり数百万のイベントを、保証された容量と 1 秒未満の待ち時間でイングレスすることができます。 Dedicated クラスター内に作成される名前空間とイベント ハブには、Premium オファリングのすべての機能とそれ以外の機能が含まれます。 詳細については、[Event Hubs Dedicated](event-hubs-dedicated-overview.md) に関する記事を参照してください。

詳細については、[Event Hubs のレベル間の比較](event-hubs-quotas.md)に関する記事を参照してください。

## <a name="event-hubs-on-azure-stack-hub"></a>Azure Stack Hub 上の Event Hubs
Azure Stack Hub 上の Event Hubs を使用すると、ハイブリッド クラウド シナリオを実現できます。 オンプレミスと Azure クラウドの両方の処理で、ストリーミングやイベントベースのソリューションがサポートされます。 ハイブリッド (接続型) シナリオであれ非接続型のシナリオであれ、大規模なイベント処理またはストリーム処理に対応したソリューションを実現できます。 シナリオは Event Hubs クラスターによってのみ制限され、その点は、必要に応じてプロビジョニングすることができます。 

(Azure Stack Hub 上および Azure 上の) Event Hubs エディションには、高度な機能パリティが実現されています。 ここでいうパリティとは、SDK、サンプル、PowerShell、CLI、ポータルからいずれも同様のエクスペリエンスが得られ、違いがほとんどないということです。 

パブリック プレビュー期間中は、Stack 上の Event Hubs は無料です。 詳細については、「[Azure Stack Hub 上の Event Hubs の概要](/azure-stack/user/event-hubs-overview)」を参照してください。

## <a name="key-architecture-components"></a>主なアーキテクチャ コンポーネント
Event Hubs には次の[主要コンポーネント](event-hubs-features.md)が含まれます。

- **イベント プロデューサー**: イベント ハブにデータを送信するエンティティ。 イベント パブリッシャーは、HTTPS、AMQP 1.0、または Apache Kafka (1.0 以降) を使用してイベントを発行できます。
- **パーティション**: 各コンシューマーは、メッセージ ストリームの特定のサブセット (パーティション) のみを読み取ります。
- **コンシューマー グループ**: イベント ハブ全体のビュー (状態、位置、またはオフセット)。 コンシューマー グループを使用すると、コンシューマー側アプリケーションがそれぞれイベント ストリーム ビューを持つことができるようになります。 それらは、独自のペースとオフセットで個別にストリームを読み取ります。
- [スループット ユニット](event-hubs-scalability.md#throughput-units)または[処理ユニット](event-hubs-scalability.md#processing-units): Event Hubs のスループット容量を制御する、購入前の容量の単位。
- **イベント レシーバー**: イベント ハブからイベント データを読み取るエンティティ。 Event Hubs のすべてのコンシューマーは、AMQP 1.0 セッションを介して接続します。 Event Hubs サービスは、イベントが利用可能になると、セッションを通じてそれらを配信します。 すべての Kafka コンシューマーは、Kafka プロトコル 1.0 以降を通じて接続します。

次の図は、Event Hubs ストリーム処理のアーキテクチャを示しています。

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.svg)


## <a name="next-steps"></a>次のステップ

Event Hubs を使い始めるには、以下の **イベントの送受信** のチュートリアルを参照してください。

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (送信のみ)](event-hubs-c-getstarted-send.md)
- [Apache Storm (受信のみ)](event-hubs-storm-getstarted-receive.md)


Event Hubs の詳細については、次の記事を参照してください。

- [Event Hubs の機能の概要](event-hubs-features.md)
- [よく寄せられる質問](event-hubs-faq.yml)
