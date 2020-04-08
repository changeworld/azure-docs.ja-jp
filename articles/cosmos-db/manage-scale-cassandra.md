---
title: Azure Cosmos DB で Cassandra API を使用してエラスティックにスケールする
description: Azure Cosmos DB Cassandra API アカウントのスケールに使用できるオプションと、それらの長所と短所について説明します
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474681"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Azure Cosmos DB Cassandra API アカウントをエラスティックにスケーリングする

Azure Cosmos DB の Cassandra 用 API のエラスティック特性を調べるためのさまざまなオプションがあります。 Azure Cosmos DB で効果的にスケールする方法を理解するには、システムのパフォーマンス要求を考慮して適切な量の要求ユニット (RU/秒) をプロビジョニングする方法を理解することが重要です。 要求ユニットの詳細については、[要求ユニット](request-units.md)に関する記事を参照してください。 

Cassandra API では、[.NET および Java SDK](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api) を使用して、個々のクエリの要求ユニットの料金を取得できます。 これは、サービスでプロビジョニングする必要がある RU/秒の量を決定するのに役立ちます。

![データベース操作による要求ユニットの消費](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>レート制限の処理 (429 エラー)

クライアントがプロビジョニングされた量よりも多くのリソースを消費する (RU/秒) と、Azure Cosmos DB によってレート制限 (429) エラーが返されます。 Azure Cosmos DB の Cassandra API は、このような例外を Cassandra ネイティブ プロトコルの過負荷エラーに変換します。 

システムが待機時間の影響を受けない場合は、再試行を使用してスループットのレート制限を処理するだけで十分な場合があります。 Java で [Cassandra 再試行ポリシー](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/)に [Azure Cosmos DB 拡張機能](https://github.com/Azure/azure-cosmos-cassandra-extensions)を使用して、レート制限を透過的に処理する方法については、[Java のコード サンプル](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)を参照してください。 [Spark 拡張機能](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)を使用して、レート制限を処理することもできます。

## <a name="manage-scaling"></a>スケーリングの管理

待機時間を最小限に抑える必要がある場合は、Cassandra API でスケールとプロビジョニングのスループット (RU) を管理するためのさまざまなオプションがあります。

* [Azure portal を使用して手動で](#use-azure-portal)
* [コントロール プレーン機能を使用してプログラムで](#use-control-plane)
* [特定の SDK で CQL コマンドを使用してプログラムで](#use-cql-queries)
* [オートパイロットを使用して動的に](#use-autopilot)

以降のセクションでは、それぞれの方法の長所と短所について説明します。 その後、ご使用のシステムのスケーリング ニーズ、全体的なコスト、およびソリューションの効率のニーズのバランスを取るための最適な方法を決定できるようになります。

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Azure portal を使用する

Azure portal を使用して Azure Cosmos DB Cassandra API アカウントのリソースをスケールできます。 詳細については、「[コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)」を参照してください。 この記事では、Azure portal で[データベース](set-throughput.md#set-throughput-on-a-database)または[コンテナー](set-throughput.md#set-throughput-on-a-container)のいずれかのレベルでスループットを設定することによる相対的利点が説明されています。 これらの記事で言及されている "データベース" と "コンテナー" という用語は、それぞれ Cassandra API の "キースペース" と "テーブル" に対応します。

この方法の利点は、データベースのスループット容量を簡単かつすばやく管理できることです。 ただし、欠点は、多くの場合、スケーリングの方法には、コスト効率と高いパフォーマンスの両方を実現するために、ある程度の自動化が必要になる場合があることです。 以降のセクションでは、関連するシナリオと方法について説明します。

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>コントロール プレーンを使用する

Azure Cosmos DB の Cassandra 用 API には、さまざまなコントロール プレーン機能を使用して、プログラムでスループットを調整する機能が用意されています。 ガイダンスとサンプルについては、[Azure Resource Manager](manage-cassandra-with-resource-manager.md)、[Powershell](powershell-samples-cassandra.md)、および [Azure CLI](cli-samples-cassandra.md) の記事を参照してください。

この方法の利点は、ピーク時のアクティビティ、またはアクティビティの少ない時間帯を考慮して、タイマーに基づいてリソースのスケールアップまたはスケールダウンを自動化できることです。 Azure Functions と Powershell を使用してこれを実現する方法については、[こちら](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)のサンプルをご覧ください。

この方法の欠点は、スケールのニーズの予測不能な変化にリアルタイムで対応できないことです。 代わりに、クライアントまたは SDK レベルで、または[オートパイロット](provision-throughput-autopilot.md)を使用して、システム内のアプリケーション コンテキストを利用することが必要になる場合があります。

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>特定の SDK で CQL クエリを使用する

指定されたデータベースまたはコンテナーに対して [CQL ALTER コマンド](cassandra-support.md#keyspace-and-table-options)を実行することで、コードを使用してシステムを動的にスケールできます。

この方法の利点は、スケールのニーズに動的に対応でき、しかもご利用のアプリケーションに適したカスタムの方法で対応できることです。 この方法でも、標準 RU/秒の課金とレートを引き続き利用できます。 システムのスケール ニーズがほぼ予測可能 (約 70% 以上) な場合は、CQL で SDK を使用する方が、オートパイロットを使用した場合よりも自動スケールのコスト効率が高くなる可能性があります。 この方法の欠点は、再試行を実装するのが非常に複雑になる上に、レート制限によって待機時間が長くなる可能性があることです。

## <a name="use-autopilot"></a><a id="use-autopilot"></a>オートパイロットを使用する

手動またはプログラムによるスループットのプロビジョニングの方法に加えて、オートパイロット モードでも Azure Cosmos のコンテナーを構成することができます。 オートパイロット モードでは、SLA を損なうことなく、消費ニーズに合わせて、指定された RU 範囲内で自動的かつ即座にスケールが行われます。 詳細については、「[オートパイロット モードで Azure Cosmos のコンテナーとデータベースを作成する](provision-throughput-autopilot.md)」を参照してください。

この方法の利点は、これがシステムのスケーリング ニーズを管理する最も簡単な方法だということです。 これによりレート制限が**構成された RU 範囲内**に適用されないことが保証されます。 欠点は、システムのスケーリング ニーズが予測可能な場合、オートパイロットは、スケーリング ニーズを処理する方法としては、前述のカスタムのコントロール プレーンまたは SDK レベルのアプローチを使用するよりもコスト効率に劣る可能性があることです。

## <a name="next-steps"></a>次のステップ

- Java アプリケーションを使用した[ Cassandra API アカウント、データベースおよびテーブルの作成](create-cassandra-api-account-java.md)の開始
