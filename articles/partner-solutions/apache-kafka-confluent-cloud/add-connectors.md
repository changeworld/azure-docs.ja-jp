---
title: Confluent Cloud 用のコネクタを追加する - Azure パートナー ソリューション
description: この記事では、Azure リソースで使用する Confluent Cloud 用のコネクタをインストールする方法について説明します。
ms.service: partner-services
ms.topic: conceptual
ms.date: 08/31/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: d37f34005dd2b4774828e79006d33d7a398cbc07
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315906"
---
# <a name="add-connectors-for-confluent-cloud"></a>Confluent Cloud 用のコネクタを追加する

この記事では、Confluent Cloud 用に Azure リソースへのコネクタをインストールする方法について説明します。

## <a name="connector-to-azure-cosmos-db"></a>Azure Cosmos DB へのコネクタ

Azure Cosmos DB シンク コネクタのフル マネージド コネクタは、Confluent Cloud 内で一般提供されています。 このフル マネージド コネクタを使用すると、カスタム統合の作成と管理が不要になり、Confluent Cloud と Azure Cosmos DB 間でデータを接続する全体的な運用負担が軽減されます。 Confluent Cloud の Microsoft Azure Cosmos シンク コネクタは、Microsoft Azure Cosmos データベースに対してデータの読み取りと書き込みを行います。 このコネクタは、Kafka からデータをポーリングし、データベース コンテナーに書き込みます。

コネクタを設定するには、[Confluent Cloud 用 Azure Cosmos DB シンク コネクタ](https://docs.confluent.io/cloud/current/connectors/cc-azure-cosmos-sink.html)に関連するページを参照してください。

コネクタを手動でインストールするには、最初に [Cosmos DB リリース ページ](https://github.com/microsoft/kafka-connect-cosmosdb/releases)から uber JAR をダウンロードします。 または、[ソース コードから独自の uber JAR を直接作成](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)することもできます。 [コネクタを手動でインストール](https://docs.confluent.io/home/connect/install.html#install-connector-manually)するための Confluent ドキュメントに記載されているガイダンスに従って、インストールを完了します。  

## <a name="next-steps"></a>次の手順

トラブルシューティングの支援については、「[Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング](troubleshoot.md)」を参照してください。
