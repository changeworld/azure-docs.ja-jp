---
title: Apache Kafka 用の Azure Event Hubs | Microsoft Docs
description: Kafka 対応 Azure Event Hubs の概要
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 8388a8988dec2629c5ab309f32b72ae45a157a02
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281462"
---
# <a name="azure-event-hubs-for-apache-kafka"></a>Apache Kafka 用の Azure Event Hubs

Event Hubs は、独自の Kafka クラスターを実行する代わりに、既存の Kafka ベースのアプリケーションが使用できる Kafka エンドポイントを提供します。 Event Hubs は、[Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) 以降のクライアント バージョンをサポートしており、MirrorMaker を含む既存の Kafka アプリケーションに対応します。 接続文字列を変更し、Kafka プロトコルを使用するアプリケーションから Event Hubs へのイベントのストリーミングを開始します。

## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka 用 Event Hubs で提供されるもの

Kafka 機能用 Event Hubs は、Azure Event Hubs 上で、Kafka トピックに対する読み取りと書き込みの両方について、Kafka バージョン 1.0 以降とバイナリ互換性のあるプロトコル ヘッドを提供します。 Kafka と Event Hubs は、概念的にはほぼ同じものです。これらはどちらも、ストリーミング データ用に構築されたパーティション分割型のログです。 次の表に、Kafka の概念と Event Hubs の概念のマッピングを示します。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka と Event Hubs の概念のマッピング

| Kafka での概念 | Event Hubs での概念|
| --- | --- |
| クラスター | 名前空間 |
| トピック | Event Hubs |
| Partition | Partition|
| コンシューマー グループ | コンシューマー グループ |
| offset | offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka と Event Hubs の主な違い

[Apache Kafka](https://kafka.apache.org/) は、選択した任意の場所で実行できるソフトウェアであるのに対して、Event Hubs は、Azure Blob Storage に似たクラウド サービスです。 管理するサーバーやネットワークはなく、構成するブローカーがありません。 ユーザーは名前空間を作成します。それが、トピックが存在する FQDN であり、次に、その名前空間内に Event Hubs またはトピックを作成します。 Event Hubs と名前空間の詳細については、「[Event Hubs とは](event-hubs-what-is-event-hubs.md)」をご覧ください。 クラウド サービスとしての Event Hubs は、単一の安定した仮想 IP アドレスをエンドポイントとして使用するため、クライアントは、ブローカーやクラスター内のマシンについて知っておく必要はありません。 

Event Hubs のスケールは、購入するスループット単位の数によって制御されます。各スループット単位では、1 秒あたり 1 MB、または受信の 1 秒あたり 1,000 イベントを利用できます。 既定では、Event Hubs は上限に達すると[自動インフレ](event-hubs-auto-inflate.md)機能によってスループット ユニットをスケールアップします。この機能は Kafka 機能用 Event Hubs でも働きます。 

### <a name="security-and-authentication"></a>セキュリティと認証

Azure Event Hubs は、すべての通信に対して SSL または TLS を要求し、認証のために Shared Access Signatures (SAS) を使用します。 この要件は、Event Hubs 内の Kafka エンドポイントにも適用されます。 Kafka との互換性のため、Event Hubs は認証用には SASL PLAIN を、トランスポート セキュリティのためには SASL SSL を使用します。 Event Hubs でのセキュリティに関する詳細については、「[Event Hubs の認証とセキュリティ](event-hubs-authentication-and-security-model-overview.md)」をご覧ください。

## <a name="other-event-hubs-features-available-for-kafka"></a>Kafka で使用できるその他の Event Hubs 機能

Kafka 機能用 Event Hubs では、あるプロトコルで書き込みを、別のプロトコルで読み取りを行えます。そのため、現在の Kafka プロデューサーは Kafka による発行を続けることができます。また、Event Hub で Azure Stream Analytics や Azure Functions などのリーダーを追加できます。 さらに、[Capture](event-hubs-capture-overview.md) や [Geo ディザスター リカバリー](event-hubs-geo-dr.md)などの Event Hubs 機能も、Kafka 機能用 Event Hubs で動作します。

## <a name="features-that-are-not-supported-in-the-preview"></a>プレビューでサポートされていない機能

Kafka 用 Event Hubs の統合のパブリック プレビューでは、以下の Kafka 機能はサポートされません。

*   べき等プロデューサー
*   トランザクション
*   圧縮
*   サイズ ベースのリテンション期間
*   ログの圧縮
*   既存トピックへのパーティションの追加
*   HTTP Kafka API のサポート
*   Kafka Connect
*   Kafka Streams

## <a name="next-steps"></a>次の手順

この記事では、Kafka 用 Event Hubs の概要について説明しました。 詳細については、以下のリンクをご覧ください。

* [Kafka 対応 Event Hubs の作成方法](event-hubs-create-kafka-enabled.md)
* [Kafka アプリケーションから Event Hubs へのストリーム](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Event Hubs のチュートリアル](event-hubs-dotnet-standard-getstarted-send.md)を開始する
* [Event Hubs の FAQ](event-hubs-faq.md)

 
 

