---
title: Azure Network Watcher の接続モニターのスキーマ | Microsoft Docs
description: Azure Network Watcher の接続モニターのテスト データ スキーマとパス データ スキーマについて説明します。
services: network-watcher
documentationcenter: na
author: mjha
manager: vinigam
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2021
ms.author: mjha
ms.openlocfilehash: 4cad1ea0d90f85a12e7d7f9b7dbc869a61a91a39
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969767"
---
# <a name="azure-network-watcher-connection-monitor-schemas"></a>Azure Network Watcher の接続モニターのスキーマ

接続モニターを使用すると、Azure Network Watcher で、統合されたエンド ツー エンドの接続監視が提供されます。 接続モニターの機能では、ハイブリッド デプロイと Azure クラウド デプロイがサポートされています。 Network Watcher には、Azure のデプロイに対する接続に関連したメトリックを監視、診断、表示するためのツールが用意されています。

以下に、接続モニターのいくつかのユース ケースを示します。

- フロントエンド Web サーバー仮想マシン (VM) と、多層アプリケーション内のデータベース サーバー VM が通信している。 2 つの VM 間のネットワーク接続を確認したい。
- 米国東部リージョンの VM から、米国中部リージョンの VM に ping を実行して、リージョン間のネットワーク待ち時間を比較したい。
- ワシントン州シアトルと、バージニア州アッシュバーンに複数のオンプレミス オフィス サイトがある。 オフィス サイトは Microsoft 365 の URL に接続している。 Microsoft 365 URL のユーザーについて、シアトルとアッシュバーンの間で待機時間を比較する。
- ハイブリッド アプリケーションで Azure Storage エンドポイントに接続する必要がある。 オンプレミスのサイトと Azure アプリケーションは、同じ Azure Storage エンドポイントに接続する。 オンプレミス サイトの待機時間と、Azure アプリケーションの待機時間を比較したい。
- オンプレミスのセットアップと、クラウド アプリケーションがホストされている Azure VM 間の接続を確認したい。

以下に、接続モニターの利点をいくつか示します。

* Azure とハイブリッドの監視のニーズに対応した、統合された直感的なエクスペリエンス
* クロス リージョン、クロス ワークスペースの接続の監視
* プローブ頻度の増加、およびネットワーク パフォーマンスの可視性の向上
* ハイブリッド デプロイでのアラートの高速化
* HTTP、TCP、ICMP に基づく接続チェックのサポート 
* Azure と Azure 以外のテストのセットアップに対するメトリックと Log Analytics のサポート

Log Analytics に取り込まれるログやデータには、2 つの種類があります。 テスト データ (NWConnectionMonitorTestResult クエリ) は、特定のテスト グループの監視頻度に基づいて更新されます。 パス データ (NWConnectionMonitorPathResult クエリ) は、損失率またはラウンドトリップ時間が大きく変化したときに更新されます。 両者は独立しているため、テスト データは更新され続けているのに、パス データはあまり更新されない期間が発生することがあります。

## <a name="connection-monitor-tests-schema"></a>接続モニターのテスト スキーマ

接続モニターのテスト データ スキーマのフィールドとその説明を次の表に示します。 

| フィールド  |    説明   |
|---|---|
| TimeGenerated | ログが生成されたときのタイムスタンプ (UTC) |
| レコード ID  | テスト結果レコードを一意に識別するためのレコード ID |
| ConnectionMonitorResourceId   | テストの接続モニター リソース ID |
| TestGroupName | テストが属しているテスト グループの名前 |
| TestConfigurationName | テストが属しているテスト構成の名前 |
| SourceType    | テスト用に構成されたソース マシンの種類 |
| SourceResourceId  | ソース マシンのリソース ID |
| SourceAddress | テスト用に構成されたソースのアドレス |
| SourceSubnet  | ソースのサブネット |
| SourceIP  | ソースの IP アドレス |
| SourceName    | ソース エンドポイント名 |
| SourceAgentId | ソースのエージェント ID |
| DestinationPort   | テスト用に構成された宛先ポート |
| [DestinationType]   | テスト用に構成されたターゲット マシンの種類 |
| DestinationResourceId | ターゲット マシンのリソース ID |
| DestinationAddress    | テスト用に構成されたターゲットのアドレス |
| DestinationSubnet | ターゲットのサブネット (該当する場合) |
| DestinationIP | ターゲットの IP アドレス |
| [DestinationName]   | ターゲットのエンドポイント名 |
| DestinationAgentId    | ターゲットのエージェント ID |
| Protocol  | テストのプロトコル |
| ChecksTotal   | テストで行われたチェックの合計数 |
| ChecksFailed  | テストで失敗したチェックの合計数 |
| TestResult    | テストの結果 |
| TestResultCriterion   | テストの結果の条件 |
| ChecksFailedPercentThreshold  | テストに設定されているチェック失敗の割合のしきい値 |
| RoundTripTimeMsThreshold  | テストに設定されたラウンドトリップしきい値 (ミリ秒) |
| MinRoundTripTimeMs    | テストの最小ラウンドトリップ時間 (ミリ秒) |
| MaxRoundTripTimeMs    | テストの最大ラウンドトリップ時間 |
| AvgRoundTripTimeMs    | テストの平均ラウンドトリップ時間 |
| JitterMs  | テストの平均偏差ラウンドトリップ時間 |
| AdditionalData    | テストの追加データ |


## <a name="connection-monitor-path-schema"></a>接続モニターのパス スキーマ

接続モニターのパス データ スキーマのフィールドとその説明を次の表に示します。 

| フィールド  |    説明   |
|---|---|
| TimeGenerated  | ログが生成されたときのタイムスタンプ (UTC) |
| レコード ID  | テスト結果レコードを一意に識別するためのレコード ID |
| TopologyId    | パス レコードのトポロジ ID |
| ConnectionMonitorResourceId   | テストの接続モニター リソース ID |
| TestGroupName | テストが属しているテスト グループの名前 |
| TestConfigurationName | テストが属しているテスト構成の名前 |
| SourceType    | テスト用に構成されたソース マシンの種類 |
| SourceResourceId  | ソース マシンのリソース ID |
| SourceAddress | テスト用に構成されたソースのアドレス |
| SourceSubnet  | ソースのサブネット |
| SourceIP  | ソースの IP アドレス | 
| SourceName    | ソース エンドポイント名 |
| SourceAgentId | ソースのエージェント ID |
| DestinationPort   | テスト用に構成された宛先ポート |
| [DestinationType]   | テスト用に構成されたターゲット マシンの種類 |
| DestinationResourceId | ターゲット マシンのリソース ID |
| DestinationAddress    | テスト用に構成されたターゲットのアドレス |
| DestinationSubnet | ターゲットのサブネット (該当する場合) |
| DestinationIP | ターゲットの IP アドレス |
| [DestinationName]   | ターゲットのエンドポイント名 |
| DestinationAgentId    | ターゲットのエージェント ID |
| Protocol  | テストのプロトコル |
| ChecksTotal   | テストで行われたチェックの合計数 |
| ChecksFailed  | テストで失敗したチェックの合計数 |
| PathTestResult    | テストの結果 |
| PathResultCriterion   | テストの結果の条件 | 
| ChecksFailedPercentThreshold  | テストに設定されているチェック失敗の割合のしきい値 |
| RoundTripTimeMsThreshold  | テストに設定されたラウンドトリップしきい値 (ミリ秒) |
| MinRoundTripTimeMs    | テストの最小ラウンドトリップ時間 (ミリ秒) |
| MaxRoundTripTimeMs    | テストの最大ラウンドトリップ時間 |
| AvgRoundTripTimeMs    | テストの平均ラウンドトリップ時間 |
| JitterMs  | テストの平均偏差ラウンドトリップ時間 |
| HopAddresses | テストで識別されたホップのアドレス |
| HopTypes  | テストで識別されたホップの種類 |
| HopLinkTypes  | テストで識別されたホップ リンクの種類 |
| HopResourceIds    | テストで識別されたホップ リソース ID |
| 発行    | テストで識別された問題 |
| Hops  | テストで識別されたホップの数 |
| AdditionalData | テストの追加データ |
