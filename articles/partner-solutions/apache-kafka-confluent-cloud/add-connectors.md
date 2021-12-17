---
title: Confluent Cloud 用のコネクタを追加する - Azure パートナー ソリューション
description: この記事では、Azure リソースで使用する Confluent Cloud 用のコネクタをインストールする方法について説明します。
ms.service: partner-services
ms.topic: conceptual
ms.date: 09/03/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a5ef809be7518c4d99ff208e769af918fe579d4f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480698"
---
# <a name="add-connectors-for-confluent-cloud"></a>Confluent Cloud 用のコネクタを追加する

この記事では、Confluent Cloud 用に Azure リソースへのコネクタをインストールする方法について説明します。

## <a name="connector-to-azure-cosmos-db"></a>Azure Cosmos DB へのコネクタ

**Azure Cosmos DB シンク コネクタのフル マネージド コネクタ** は、Confluent Cloud 内で一般提供されています。 このフル マネージド コネクタを使用すると、カスタム統合の作成と管理が不要になり、Confluent Cloud と Azure Cosmos DB 間でデータを接続する全体的な運用負担が軽減されます。 Confluent Cloud の Microsoft Azure Cosmos シンク コネクタは、Microsoft Azure Cosmos データベースに対してデータの読み取りと書き込みを行います。 このコネクタは、Kafka からデータをポーリングし、データベース コンテナーに書き込みます。

コネクタを設定するには、[Confluent Cloud 用 Azure Cosmos DB シンク コネクタ](https://docs.confluent.io/cloud/current/connectors/cc-azure-cosmos-sink.html)に関連するページを参照してください。

**Azure Cosmos DB 自己管理型コネクタ** を手動でインストールする必要があります。 最初に [Cosmos DB リリース ページ](https://github.com/microsoft/kafka-connect-cosmosdb/releases)から uber JAR をダウンロードします。 または、[ソース コードから独自の uber JAR を直接作成](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)することもできます。 [コネクタを手動でインストール](https://docs.confluent.io/home/connect/install.html#install-connector-manually)するための Confluent ドキュメントに記載されているガイダンスに従って、インストールを完了します。  

## <a name="next-steps"></a>次の手順

トラブルシューティングの支援については、「[Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング](troubleshoot.md)」を参照してください。
