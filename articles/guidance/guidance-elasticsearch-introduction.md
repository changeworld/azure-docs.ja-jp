
<properties
   pageTitle="Azure での Elasticsearch に関するガイダンス | Microsoft Azure"
   description="Azure での Elasticsearch に関するガイダンス。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Azure での Elasticsearch に関するガイダンス

Elasticsearch は、非常にスケーラブルなオープンソースの検索エンジンとデータベースです。大規模なデータセットに含まれる情報を高速で検索し、分析する場合に最適です。このガイダンスは、Elasticsearch クラスターを設計するときに考慮する必要がある、いくつかの重要な側面について説明します。

- **[一般的なガイダンス][]**では、Elasticsearch の一般的構造を簡単に説明し、Azure を使用して Elasticsearch クラスターを実装する方法について説明します。
- **[データ取り込みに関するガイダンス][]**では、高速のデータ取り込みが予測される Elasticsearch クラスターを実装するときに考慮する必要がある、デプロイと構成のオプションについて説明します。
- **[パフォーマンス テストに関するガイダンス][]**では、Elasticsearch クラスターのパフォーマンスをテストするための環境を設定する方法について説明します。
- **[JMeter に関するガイダンス][]**では、データを生成して Elasticsearch クラスターにアップロードできる JUnit サンプラーを作成して使用する方法について説明します。
- **[JMeter に関する考慮事項][]**では、データ取り込みおよびクエリに関するテスト計画の構築および実行から得られる主要なエクスペリエンスの概要を示します。 

  > [AZURE.NOTE] このガイダンスでは、Elasticsearch の基本的知識を前提としています。詳細については、[Elasticsearch の Web サイト](https://www.elastic.co/products/elasticsearch)にアクセスしてください。

[一般的なガイダンス]: guidance-elasticsearch.md
[データ取り込みに関するガイダンス]: guidance-elasticsearch-data-ingestion.md
[パフォーマンス テストに関するガイダンス]: guidance-elasticsearch-performance-testing-environment.md
[JMeter に関するガイダンス]: guidance-elasticsearch-implementing-jmeter.md
[JMeter に関する考慮事項]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->