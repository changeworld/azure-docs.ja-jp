
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
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Azure での Elasticsearch に関するガイダンス

Elasticsearch は、非常にスケーラブルなオープンソースの検索エンジンとデータベースです。大規模なデータセットに含まれる情報を高速で検索し、分析する場合に最適です。このガイダンスは、Elasticsearch クラスターを設計するときに考慮する必要がある、いくつかの重要な側面について説明します。

- **[Azure で Elasticsearch を実行する][]**では、Elasticsearch の一般的構造を簡単に説明し、Azure を使用して Elasticsearch クラスターを実装する方法について説明します。
- **[Azure での Elasticsearch のデータ インジェスト パフォーマンスのチューニング][]**では、高速のデータ取り込みが予測される Elasticsearch クラスターを実装するときに考慮する必要がある、デプロイと構成のオプションについて説明します。
- **[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの集計とクエリのパフォーマンスを調整する)][]** では、クエリと検索に関して最高のパフォーマンスが得られるシステムにする最善の方法を決定するときに考慮できるオプションについて説明します。
- **[Azure 上の Elasticsearch での復元と回復の設定][]**では、Azure でホストされている場合の Elasticsearch で使用できる復元と回復のオプションについて説明します。
- **[Azure での Elasticsearch のパフォーマンス テスト環境の作成][]**では、Elasticsearch クラスターのパフォーマンスをテストするための環境を設定する方法について説明します。
- **[Elasticsearch 用の JMeter テスト計画を実装する][]**では、データを生成し Elasticsearch クラスターにアップロードするための JUnit サンプラーを作成し使用する方法について説明します。
- **[Elasticsearch のパフォーマンスをテストするための JMeter JUnit サンプラーをデプロイする][]**では、データ取り込みおよびクエリに関するテスト計画の構築および実行から得られる主要なエクスペリエンスの概要を示します。 
- **[Elasticsearch の自動復元テストの実行][]**では、上の記事で使用される復元テストの実行について説明します。
- **[自動化された Elasticsearch パフォーマンス テストの実行][]**では、上の記事で使用されるパフォーマンス テストの実行について説明します。

> [AZURE.NOTE] このガイダンスでは、Elasticsearch の基本的知識を前提としています。詳細については、[Elasticsearch の Web サイト](https://www.elastic.co/products/elasticsearch)にアクセスしてください。

[Azure で Elasticsearch を実行する]: guidance-elasticsearch-running-on-azure.md
[Azure での Elasticsearch のデータ インジェスト パフォーマンスのチューニング]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Azure での Elasticsearch のパフォーマンス テスト環境の作成]: guidance-elasticsearch-creating-performance-testing-environment.md
[Elasticsearch 用の JMeter テスト計画を実装する]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Elasticsearch のパフォーマンスをテストするための JMeter JUnit サンプラーをデプロイする]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの集計とクエリのパフォーマンスを調整する)]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Azure 上の Elasticsearch での復元と回復の設定]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Elasticsearch の自動復元テストの実行]: guidance-elasticsearch-running-automated-resilience-tests.md
[自動化された Elasticsearch パフォーマンス テストの実行]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016-->