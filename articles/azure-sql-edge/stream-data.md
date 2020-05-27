---
title: Azure SQL Edge (プレビュー) でのデータ ストリーミング
description: Azure SQL Edge (プレビュー) でのデータ ストリーミングの詳細
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594511"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) でのデータ ストリーミング

Azure SQL Edge (プレビュー) には、データ ストリーミングを実装するための 2 つのオプションが用意されています。 

1. Azure で作成された Azure Streaming Analytics Edge ジョブをデプロイする。 Azure SQL Edge で Azure Streaming Analytics Edge ジョブをデプロイする方法の詳細については、[Azure Stream Analytics ジョブのデプロイ](deploy-dacpac.md)に関するページを参照してください。
2. Azure でストリーミング ジョブを構成せずに、新しい **T-SQL ストリーミング**機能を使用して SQL Edge でストリーミング ジョブを作成する。 

どちらのオプションを使用しても SQL Edge でデータ ストリーミングを実装できますが、いずれか 1 つだけを使用することを強くお勧めします。 両方を使用すると、データ ストリーミング操作の機能に影響を及ぼす競合状態が発生する可能性があります。

このドキュメントの残りの部分では、複数のソースからの大量の高速ストリーミング データを同時に分析および処理するためのリアルタイムのデータ ストリーミング、分析、イベント処理を提供する新しい機能、**T-SQL ストリーミング**について説明します。 *T-SQL ストリーミング*は、Microsoft Azure の [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) で採用されているのと同じハイ パフォーマンスのストリーミング エンジンを使用して構築されており、エッジで実行されている Azure Stream Analytics で提供されるのと同様の機能セットをサポートします。

T-SQL ストリーミングでは、Azure Stream Analytics と同様に、デバイス、センサー、アプリケーションなどのさまざまな IoT 入力ソースから抽出された情報に含まれるパターンやリレーションシップを認識できます。 これらのパターンを使用してアクションをトリガーし、アラートの作成、レポート作成ソリューションや視覚化ソリューションへの情報のフィード、後で使用するためのデータの保存などのワークフローを開始できます。 

T-SQL ストリーミングを使用できる場合の例として、次のシナリオがあります。

* IoT デバイスからのリアルタイムのテレメトリ ストリームの分析。
* 無人の自動運転車両から生成されたデータのリアルタイム分析。
* 高価な産業資産や製造資産のリモート監視と予測メンテナンス。
* 農園や発電所での IoT センサーの読み取り値の異常検出やパターン認識。

## <a name="how-does-t-sql-streaming-work"></a>T-SQL ストリーミングのしくみ

T-SQL ストリーミングのしくみは、[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work) とまったく同じです。たとえば、リアルタイム データ ストリーミングの処理には、ストリーミング ジョブの概念を使用します。 

ストリーム分析ジョブは次のもので構成されます。

- ストリーム入力 - ストリーム入力は、データ ストリームの読み取り元となるデータ ソースへの接続を定義します。 現在、Azure SQL Edge では次の種類のストリーム入力がサポートされています。
    - Edge ハブ
    - Kafka - 現在、Kafka 入力のサポートは Intel/AMD64 バージョンの Azure SQL Edge でのみ利用可能です。

- ストリーム出力 - ストリーム出力は、データ ストリームの書き込み先となるデータ ソースへの接続を定義します。 現在、Azure SQL Edge では次の種類のストリーム出力がサポートされています。
    - Edge ハブ
    - SQL - SQL 出力は、SQL Edge インスタンス内のローカル データベースか、リモートの Microsoft SQL サーバーまたは Microsoft Azure SQL データベースです。 
    - Azure Blob Storage

- ストリーム クエリ - ストリーム クエリは、入力ストリームがストリーム出力に書き込まれる前に適用する必要がある変換、集計、フィルター、並べ替え、結合を定義します。 ストリーム クエリは、Azure Stream Analytics で使用されているのと同じクエリ言語に基づいています。 Azure Stream Analytics クエリ言語の詳細については、[Stream Analytics クエリ言語](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)に関するページを参照してください。

> [!IMPORTANT]
> Azure Stream Analytics とは異なり、現在、T-SQL ストリーミングでは[ルックアップでの参照データの使用](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)や[ストリーム ジョブでの UDF および UDA の使用](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)はサポートされていません。

> [!NOTE]
> T-SQL ストリーミングでは、Azure Stream Analytics でサポートされている言語サーフェス領域のサブセットのみがサポートされます。 Azure Stream Analytics クエリ言語の詳細については、[Stream Analytics クエリ言語](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)に関するページを参照してください。

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

T-SQL ストリーミングには、次の制限事項と制約事項が適用されます。 

- どの時点でも、アクティブにできるストリーミング ジョブは 1 つだけです。 既に実行中のジョブは、別のジョブを開始する前に停止する必要があります。
- 各ストリーミング ジョブの実行はシングル スレッドです。 ストリーミング ジョブに複数のクエリが含まれている場合、各クエリは直列の順序で評価されます。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge (プレビュー) での Stream Analytics ジョブの作成](create-stream-analytics-job.md)
- [Azure SQL Edge (プレビュー) でのストリーム ジョブに関連付けられたメタデータの表示](streaming-catalog-views.md)
- [外部ストリームの作成](create-external-stream-transact-sql.md)