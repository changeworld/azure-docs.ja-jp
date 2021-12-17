---
title: インクルード ファイル
description: インクルード ファイル
services: synapse-analytics
author: jonburchel
ms.service: synapse-analytics
ms.topic: include
ms.date: 09/22/2021
ms.author: jburchel
ms.custom: include file
ms.openlocfilehash: d97a314a5928de17ad878aee5dfc14b5519d0f80
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129095950"
---
Azure Synapse Analytics には、お客様のサブスクリプションが互いのワークロードから確実に保護されるように、次の既定の制限があります。 サブスクリプションの上限まで制限を引き上げるには、サポートにお問い合わせください。

### <a name="synapse-workspace-limits"></a>Synapse ワークスペースの制限

| リソース | 既定の制限 | 上限 | 
| -------- | ------------- | ------------- |
| Azure サブスクリプションの Synapse ワークスペース | 20 | 20 |

### <a name="synapse-pipeline-limits"></a>Synapse パイプラインの制限

| リソース | 既定の制限 | 上限 |
| -------- | ------------- | ------------- |
| Synapse ワークスペース内の Synapse パイプライン | 800 | 800 |
| ワークスペース内のエンティティ (パイプライン、データセット、トリガー、リンクされたサービス、プライベート エンドポイント、統合ランタイムなど) の合計数 | 5,000 | <bpt id="p1">[</bpt>サポートにお問い合わせください<ept id="p1">](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)</ept>。 |
| 1 つのワークスペースでの Azure-SSIS Integration Runtime の CPU コア数の合計 | 256 | <bpt id="p1">[</bpt>サポートにお問い合わせください<ept id="p1">](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)</ept>。 |
| ワークスペースあたりの同時実行パイプラインの実行数 (ワークスペース内のすべてのパイプライン間で共有) | 10,000  | 10,000 |
| <bpt id="p1">[</bpt>Azure Integration Runtime リージョン<ept id="p1">](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)</ept>あたり、ワークスペースあたりの同時実行外部アクティビティの実行数<br><bpt id="p1"><small></bpt>外部アクティビティは統合ランタイムで管理されますが、リンクされたサービスで実行されます (Databricks、ストアド プロシージャ、HDInsight、Web などを含む)。この制限は、セルフホステッド IR には適用されません。<ept id="p1"></small></ept> | 3,000 | 3,000 |
| <bpt id="p1">[</bpt>Azure Integration Runtime リージョン<ept id="p1">](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)</ept>あたり、ワークスペースあたりの同時実行パイプライン アクティビティの実行数 <br><bpt id="p1"><small></bpt>パイプライン アクティビティは統合ランタイムで実行されます (Lookup、GetMetadata、Delete を含む)。この制限は、セルフホステッド IR には適用されません。<ept id="p1"></small></ept> | 1,000 | 1,000                                                        |
| <bpt id="p1">[</bpt>Azure Integration Runtime リージョン<ept id="p1">](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)</ept>あたり、ワークスペースあたりの同時実行作成操作数<br><bpt id="p1"><small></bpt>接続のテスト、フォルダー一覧とテーブル一覧の参照、データのプレビューを含みます。この制限は、セルフホステッド IR には適用されません。<ept id="p1"></small></ept> | 200 | 200                                                          |
| <bpt id="p2">[</bpt>Azure Integration Runtime リージョン<ept id="p2">](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)</ept>あたり、ワークスペースあたりの同時実行データ統合単位<bpt id="p1"><sup></bpt>1<ept id="p1"></sup></ept>の消費| リージョン グループ 1<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept>: 6,000<br>リージョン グループ 2<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept>: 3,000<br>リージョン グループ 3<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept>: 1,500<br>マネージド仮想ネットワーク<sup>2</sup>: 2,400 | リージョン グループ 1<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept>: 6,000<br/>リージョン グループ 2<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept>: 3,000<br/>リージョン グループ 3<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept>: 1,500<br>マネージド仮想ネットワーク: [サポートにお問い合わせください](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| パイプラインあたりの最大アクティビティ数 (コンテナーの内部アクティビティを含む) | 40 | 40 |
| 単一のセルフホステッド統合ランタイムに対して作成できる、リンクされた統合ランタイムの最大数 | 100 | <bpt id="p1">[</bpt>サポートにお問い合わせください<ept id="p1">](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)</ept>。 |
| パイプラインあたりの最大パラメーター数 | 50 | 50 |
| ForEach 項目数 | 100,000 | 100,000 |
| ForEach 並列処理 | 20 | 50 |
| パイプラインあたりのキューに入れられた実行の最大数 | 100 | 100 |
| 式ごとの文字数 | 8,192 | 8,192 |
| 最小タンブリング ウィンドウ トリガー間隔 | 5 分 | 15 分 |
| パイプラインのアクティビティ実行の最大タイムアウト | 7 日 | 7 日 |
| パイプライン オブジェクトのオブジェクトあたりのバイト数<bpt id="p1"><sup></bpt>3<ept id="p1"></sup></ept> | 200 KB | 200 KB |
| データセットおよびリンクされたサービス オブジェクトのオブジェクトあたりのバイト数<bpt id="p1"><sup></bpt>3<ept id="p1"></sup></ept> | 100 KB | 2,000 KB |
| 各アクティビティの実行のペイロードあたりのバイト数<bpt id="p1"><sup></bpt>4<ept id="p1"></sup></ept> | 896 KB | 896 KB |
| コピー アクティビティの実行あたりのデータ統合単位<bpt id="p1"><sup></bpt>1<ept id="p1"></sup></ept> | 256 | 256 |
| API 呼び出しの書き込み | 1,200/h | 1,200/h<br/><br/> この制限は、Azure Synapse Analytics ではなく、Azure Resource Manager によって課せられます。 |
| API 呼び出しの読み取り | 12,500/時 | 12,500/時<br/><br/> この制限は、Azure Synapse Analytics ではなく、Azure Resource Manager によって課せられます。 |
| 1 分あたりの監視クエリ | 1,000 | 1,000 |
| データ フロー デバッグ セッションの最大時間 | 8 時間 | 8 時間 |
| 統合ランタイムごとのデータ フロー同時実行数 | 50 | <bpt id="p1">[</bpt>サポートにお問い合わせください<ept id="p1">](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)</ept>。 |
| マネージド vNet 内の統合ランタイムごとのデータ フロー同時実行数| 20 | <bpt id="p1">[</bpt>サポートにお問い合わせください<ept id="p1">](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)</ept>。 |
| ワークスペースごと、かつユーザーごとのデータ フロー デバッグ セッションの同時実行数 | 3 | 3 |
| Data Flow の Azure IR の TTL 制限 | 4 時間 |  4 時間 |
| ワークスペースでのメタデータ エンティティのサイズ制限 | 2 GB | <bpt id="p1">[</bpt>サポートにお問い合わせください<ept id="p1">](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)</ept>。 |

<bpt id="p1"><sup></bpt>1<ept id="p1"></sup></ept> データ統合単位 (DIU) は、クラウド間のコピー操作で使用されます。詳細については、<bpt id="p2">[</bpt>データ統合単位 (バージョン 2)<ept id="p2">](../articles/data-factory/copy-activity-performance.md#data-integration-units)</ept> に関するページを参照してください。 課金の詳細については、「<bpt id="p1">[</bpt>Azure Synapse Analytics の価格<ept id="p1">](https://azure.microsoft.com/pricing/details/synapse-analytics/)</ept>」を参照してください。

<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept> <bpt id="p2">[</bpt>Azure Integration Runtime<ept id="p2">](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime)</ept> は、データ コンプライアンス、効率、およびネットワーク エグレス コストの削減を実現するために<bpt id="p3">[</bpt>グローバルに使用できます<ept id="p3">](https://azure.microsoft.com/global-infrastructure/services/)</ept>。 

| リージョン グループ | リージョン |
| -------- | ------ |
| リージョン グループ 1 | 米国中部、米国東部、米国東部 2、北ヨーロッパ、西ヨーロッパ、米国西部、米国西部 2 |
| リージョン グループ 2 | オーストラリア東部、オーストラリア南東部、ブラジル南部、インド中部、東日本、米国中北部、米国中南部、東南アジア、米国中西部 |
| リージョン グループ 3 | 他の地域 |
マネージド仮想ネットワークが有効な場合、すべてのリージョン グループのデータ統合ユニット (DIU) は 2,400 です。

<bpt id="p1"><sup></bpt>3<ept id="p1"></sup></ept> パイプライン、データ セット、およびリンクされたサービス オブジェクトは、ワークロードの論理的なグループ化を表します。 これらのオブジェクトの制限は、Azure Synapse Analytics で移動したり処理したりできるデータ量には関係ありません。 Synapse Analytics は、数ペタバイトのデータ処理までスケーリングできるように設計されています。

<bpt id="p1"><sup></bpt>4<ept id="p1"></sup></ept> 各アクティビティの実行のペイロードには、アクティビティの構成、関連付けられているデータセットとリンクされたサービスの構成 (ある場合)、アクティビティの種類ごとに生成されるシステム プロパティの一部が含まれます。 このペイロード サイズの制限は、Azure Synapse Analytics で移動したり処理したりできるデータ量には関係ありません。 この制限に達した場合の<bpt id="p1">[</bpt>現象と推奨事項<ept id="p1">](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large)</ept>について確認してください。

### <a name="dedicated-sql-pool-limits"></a>専用 SQL プールの制限
Azure Synapse Analytics の専用 SQL プールの容量制限の詳細については、<bpt id="p1">[</bpt>専用 SQL プールのリソースの制限<ept id="p1">](../articles/synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)</ept>に関するページを参照してください。

### <a name="web-service-call-limits"></a>Web サービス呼び出しの制限
Azure Resource Manager では、API 呼び出しの制限があります。 API の呼び出しは、 <bpt id="p1">[</bpt>Azure リソース マネージャーの API 制限<ept id="p1">](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)</ept>内の割合で実行できます。
