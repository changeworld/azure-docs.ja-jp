---
title: Confluent Cloud 用のコネクタを追加する - Azure パートナー ソリューション
description: この記事では、Azure リソースで使用する Confluent Cloud 用のコネクタをインストールする方法について説明します。
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c4a15a54c5b9fb3b73a3fcaf81d63a9e176b49c5
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112534889"
---
# <a name="add-connectors-for-confluent-cloud"></a>Confluent Cloud 用のコネクタを追加する

この記事では、Confluent Cloud 用に Azure リソースへのコネクタをインストールする方法について説明します。

## <a name="connector-to-azure-cosmos-db"></a>Azure Cosmos DB へのコネクタ

Confluent ハブ クライアント内から、[Confluent ハブの一覧](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos)で推奨されているように Cosmos DB コネクタをインストールします。 

コネクタを手動でインストールするには、最初に [Cosmos DB リリース ページ](https://github.com/microsoft/kafka-connect-cosmosdb/releases)から uber JAR をダウンロードします。 または、[ソース コードから独自の uber JAR を直接作成](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)することもできます。 [コネクタを手動でインストール](https://docs.confluent.io/home/connect/install.html#install-connector-manually)するための Confluent ドキュメントに記載されているガイダンスに従って、インストールを完了します。  

## <a name="next-steps"></a>次の手順

トラブルシューティングの支援については、「[Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング](troubleshoot.md)」を参照してください。
