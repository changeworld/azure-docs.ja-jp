---
title: Azure Data Factory の新機能
description: このページでは、Azure Data Factory の新機能と最近の機能強化について説明します。 Azure Data Factory は、ETL (抽出、変換、読み込み)、ELT (抽出、読み込み、変換)、データ統合という複雑なハイブリッド プロジェクト用に構築されたマネージド クラウド サービスです。
author: pennyzhou-msft
ms.author: xupzhou
ms.service: data-factory
ms.subservice: concepts
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: 52c59662e1dfa97e779cce0b812bcaf25a8494a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301629"
---
# <a name="whats-new-in-azure-data-factory"></a>Azure Data Factory の新機能

Azure Data Factory サービスには継続的に改善が施されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 

## <a name="october-2021"></a>2021 年 10 月
<br>
<table>
<tr><td><bpt id="p1"><b></bpt>サービス カテゴリ<ept id="p1"></b></ept></td><td><bpt id="p1"><b></bpt>サービスの機能強化<ept id="p1"></b></ept></td><td><b>詳細</b></td></tr>
  
<tr><td rowspan=3><b>データ フロー</b></td><td>Azure Data Explorer と アマゾン ウェブ サービス S3 コネクタ</td><td>Microsoft データ統合チームは、マッピング データ フロー用の新しいコネクタを 2 つリリースしました。 Azure Synapse を使用しているユーザーは、データ変換のために AWS S3 バケットに直接接続できるようになりました。 Azure Data Factory と Azure Synapse の両方とも、マッピング データ フローで Azure Data Explorer クラスターにネイティブに接続できるようになりました。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/mapping-data-flow-gets-new-native-connectors/ba-p/2866754">詳細情報</a></td></tr>
<tr><td>Power Query アクティビティがプレビューから一般提供 (GA) に</td><td>Microsoft は、Azure Data Factory Power Query パイプライン アクティビティを一般提供としてリリースしました。 この新機能によって、市民インテグレーター用にスケールアウトされたデータ準備とデータ ラングリングが ADF ブラウザー UI 内で利用可能となり、データ エンジニアに統合されたエクスペリエンスがもたらされます。 ADF Power Query のデータ ラングリング機能では、非常に複雑なデータ統合と ETL パターンを 1 つのサービスで解決することを可能にする、強力で使いやすいパイプライン機能が提供されます。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/data-wrangling-at-scale-with-adf-s-power-query-activity-now/ba-p/2824207">詳細情報</a></td></tr>
<tr><td>マッピング データ フローでの新しい文字列化によるデータ変換</td><td>マッピング データ フローに文字列化 (Stringify) という新しいデータ変換が追加され、構造体や配列などの複雑なデータ型を、構造化された出力先に送信可能な文字列形式に簡単に変換できるようになりました。<br><a href="data-flow-stringify.md">詳細情報</a></td></tr>
  
<tr>
  <td rowspan=2><bpt id="p1"><b></bpt>Integration Runtime<ept id="p1"></b></ept></td>
  <td>Azure Data Factory マネージド VNet が GA になりました</td>
  <td>マネージド仮想ネットワークの一部として Azure Integration Runtime をプロビジョニングし、プライベート エンドポイントを利用して、サポートされるデータ ストアに安全に接続できるようになりました。 データ トラフィックは、データ ソースへのセキュリティで保護された接続を提供する Azure Private Link を経由します。 また、パブリック インターネットへのデータ流出を防ぐことができます。<br><bpt id="p1"><a href="managed-virtual-network-private-endpoint.md"></bpt>詳細情報<ept id="p1"></a></ept></td>
 </tr>
 <tr>
   <td>SSIS 統合ランタイムでの高速 VNet インジェクション (パブリック プレビュー)</td>
   <td>SSIS 統合ランタイムで高速 VNet インジェクションがサポートされました。<br>
詳細情報:<br>
     <a href="join-azure-ssis-integration-runtime-virtual-network.md">SSIS 統合ランタイムでの VNet インジェクションの概要</a><br>
     <a href="azure-ssis-integration-runtime-virtual-network-configuration.md">SSIS 統合ランタイムでの VNet インジェクション (標準と高速の比較)</a><br>
     <a href="azure-ssis-integration-runtime-express-virtual-network-injection.md">SSIS 統合ランタイムでの高速 VNet インジェクション</a>
   </td>
 </tr>

<tr><td rowspan=2><b>Security</b></td><td>Azure Key Vault 統合の改善</td><td>Azure Key Vault 統合の改善として、リンクされたサービスのシークレット値をユーザーが選択できるようにするドロップダウンを追加した結果、生産性が向上し、人的エラーの原因となるユーザーによるシークレットの入力が不要になりました。</td></tr>
<tr><td>Azure Data Factory でのユーザー割り当てマネージド ID のサポート</td><td>資格情報の安全性は、あらゆる企業で重要です。 Azure Data Factory (ADF) チームはこの点を念頭に置いて、データ エンジニアリング プロセスを安全にしつつ、データ エンジニアにとって簡単なものになるよう取り組んでいます。 Azure Active Directory (Azure AD) ベースの認証をサポートしているすべてのコネクタとリンク されたサービスで、ユーザー割り当てマネージド ID (プレビュー) がサポートされたことをお知らせします。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-for-user-assigned-managed-identity-in-azure-data-factory/ba-p/2841013">詳細情報</a></td></tr>
</table>

## <a name="september-2021"></a>2021 年 9 月
<br>
<table>
<tr><td><bpt id="p1"><b></bpt>サービス カテゴリ<ept id="p1"></b></ept></td><td><bpt id="p1"><b></bpt>サービスの機能強化<ept id="p1"></b></ept></td><td><b>詳細</b></td></tr>
  <tr><td><bpt id="p1"><b></bpt>継続的インテグレーションと継続的デリバリー (CI/CD)<ept id="p1"></b></ept></td><td>拡張された CI/CD 機能</td><td>Azure Data Factory の他のブランチをベースに、新しい Git ブランチを作成できるようになりました。<br><bpt id="p1"><a href="source-control.md#version-control"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td rowspan=3><bpt id="p1"><b></bpt>データの移動<ept id="p1"></b></ept></td><td>Oracle ソース用 Amazon Relational Database Service (RDS)</td><td>Oracle ソース コネクタ用 Amazon RDS は、Azure Data Factory と Azure Synapse の両方で使用できるようになりました。<br><bpt id="p1"><a href="connector-amazon-rds-for-oracle.md"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td>SQL Server ソース用 Amazon RDS</td><td>SQL Server ソース コネクタ用 Amazon RDS は、Azure Data Factory と Azure Synapse の両方で使用できるようになりました。<br><bpt id="p1"><a href="connector-amazon-rds-for-sql-server.md"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td>Azure Database for PostgreSQL からの並列コピーをサポート</td><td>Azure Database for PostgreSQL コネクタは、並列コピー操作をサポートするようになりました。<br><bpt id="p1"><a href="connector-azure-database-for-postgresql.md"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td rowspan=3><bpt id="p1"><b></bpt>データ フロー<ept id="p1"></b></ept></td><td>Azure Data Lake Storage (ADLS) Gen2 を使用して事前および事後処理コマンドを実行する</td><td>Hadoop Distributed File System (HDFS) の事前および事後処理コマンドを、データフローの ADLS Gen2 シンクを使用して実行できるようになりました<br><bpt id="p1"><a href="connector-azure-data-lake-storage.md#pre-processing-and-post-processing-commands"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td>Azure Integration Runtime (IR) の既存インスタンスのデータ フロー プロパティを編集する</td><td>Azure Integration Runtime (IR) が更新され、既存の IR のデータ フロー プロパティの編集が可能になりました。 新しい Azure IR を作成することなく、データ フローのコンピューティング プロパティを変更できるようになりました。<br><bpt id="p1"><a href="concepts-integration-runtime.md"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td>パイプライン アクティビティの実行開始時間を向上するための Azure Synapse の TTL 設定</td><td>Azure Synapse Analytics によって、Azure Integration Runtime に TTL が追加され、データ フロー パイプラインのアクティビティを数秒で実行開始できるようにして、データ フロー パイプラインの実行時間を大幅に短縮しました。<br><bpt id="p1"><a href="control-flow-execute-data-flow-activity.md#data-flow-integration-runtime"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td><bpt id="p1"><b></bpt>Integration Runtime<ept id="p1"></b></ept></td><td>Azure Data Factory マネージド VNet が GA になりました</td><td>マネージド仮想ネットワークの一部として Azure Integration Runtime をプロビジョニングし、プライベート エンドポイントを利用して、サポートされるデータ ストアに安全に接続できるようになりました。 データ トラフィックは、データ ソースへのセキュリティで保護された接続を提供する Azure Private Link を経由します。 また、パブリック インターネットへのデータ流出を防ぐことができます。<br><bpt id="p1"><a href="managed-virtual-network-private-endpoint.md"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td rowspan=2><bpt id="p1"><b></bpt>オーケストレーション<ept id="p1"></b></ept></td><td>データ パイプライン用 SLA の運用化と提供</td><td>この新しい経過時間パイプライン実行メトリックと Data Factory アラートと組み合わせることで、データ パイプライン開発者はより適切に SLA をお客様に提供することができます。また、パイプラインの予想実行時間を Microsoft に知らせておくと、パイプラインの実行時間が予想より長いときに Microsoft から事前に通知されます。<br><bpt id="p1"><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/operationalize-and-provide-sla-for-data-pipelines/ba-p/2767768"></bpt>詳細情報<ept id="p1"></a></ept></td></tr>
<tr><td>Fail アクティビティ (パブリック プレビュー)</td><td>新しい Fail アクティビティを使用すると、何らかの理由で意図的にパイプラインにエラーをスローできます。 たとえば、ルックアップ アクティビティが一致するデータを返さない場合や、カスタム アクティビティが内部エラーで終了する場合は、Fail アクティビティを使用できます。<br><a href="control-flow-fail-activity.md">詳細情報</a></td></tr>
</table>

## <a name="august-2021"></a>2021 年 8 月
<br>
<table>
<tr><td><bpt id="p1"><b></bpt>サービス カテゴリ<ept id="p1"></b></ept></td><td><bpt id="p1"><b></bpt>サービスの機能強化<ept id="p1"></b></ept></td><td><b>詳細</b></td></tr>
  <tr><td><bpt id="p1"><b></bpt>継続的インテグレーションと継続的デリバリー (CI/CD)<ept id="p1"></b></ept></td><td>Azure Government と Azure China での GitHub サポートによる CICD の向上</td><td>米国政府向け Azure およびAzure China で GitHub のサポートが追加されました。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/cicd-improvements-with-github-support-in-azure-government-and/ba-p/2686918">詳細情報</a></td></tr>
<tr><td rowspan=2><bpt id="p1"><b></bpt>データの移動<ept id="p1"></b></ept></td><td>Azure Data Factory において、Azure Cosmos DB の MongoDB 用 API コネクタでバージョン 3.6 と 4.0 がサポートされます</td><td>Azure Data Factory Cosmos DB の MongoDB 用 API コネクタで、サーバー バージョン 3.6 と 4.0 がサポートされるようになりました。<br><a href="connector-azure-cosmos-db-mongodb-api.md">詳細情報</a></td></tr>
<tr><td>COPY ステートメントを使用した Azure Synapse Analytics へのデータ読み込みの強化</td><td>Azure Data Factory Azure Synapse Analytics コネクタで、ステージング コピーと、 <bpt id="p1">*</bpt>.<ept id="p1">*</ept> を COPY ステートメントの wildcardFilename として使用するコピー ソースがサポートされるようになりました。<br><a href="connector-azure-sql-data-warehouse.md#use-copy-statement">詳細情報</a></td></tr>
<tr><td><bpt id="p1"><b></bpt>データ フロー<ept id="p1"></b></ept></td><td>データ フローで REST エンドポイントをソースおよびシンクとして利用できます</td><td>Azure Data Factory と Azure Synapse Analytics のデータ フローで、JSON と XML の両方のペイロードが完全にサポートされるソースおよびシンクとして REST エンドポイントがサポートされるようになりました。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/rest-source-and-sink-now-available-for-data-flows/ba-p/2596484">詳細情報</a></td></tr>
<tr><td><bpt id="p1"><b></bpt>Integration Runtime<ept id="p1"></b></ept></td><td>セルフホステッド統合ランタイムの診断ツールが利用可能になりました</td><td>セルフホステッド統合ランタイム用の診断ツールは、ユーザー エクスペリエンスを向上させ、ユーザーによる潜在的な問題の検出を支援するように設計されています。 このツールでは、セルフホステッド統合ランタイム マシンで一連のテスト シナリオが実行されます。すべてのシナリオには、よくある問題に対する典型的な正常性チェック ケースが含まれています。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/diagnostic-tool-for-self-hosted-integration-runtime/ba-p/2634905">詳細情報</a></td></tr>
<tr><td><bpt id="p1"><b></bpt>オーケストレーション<ept id="p1"></b></ept></td><td>高度なフィルター処理オプションを含むカスタム イベント トリガーが一般提供されました</td><td>Event Grid にポストされたカスタム トピックに応答するトリガーを作成できるようになりました。 さらに、高度なフィルター処理を利用して、応答するイベントを細かく制御できます。<br><a href="how-to-create-custom-event-trigger.md">詳細情報</a></td></tr>
</table>

## <a name="july-2021"></a>2021 年 7 月
<br>
<table>
<tr><td><bpt id="p1"><b></bpt>サービス カテゴリ<ept id="p1"></b></ept></td><td><bpt id="p1"><b></bpt>サービスの機能強化<ept id="p1"></b></ept></td><td><b>詳細</b></td></tr>
<tr><td><bpt id="p1"><b></bpt>データの移動<ept id="p1"></b></ept></td><td>ADF のデータ コピー ツールでメタデータ駆動型のデータ インジェスト パイプラインを 10 分以内に取得します (パブリック プレビュー)</td><td>これにより、10 分以内に、データ コピー ツールでメタデータ駆動型アプローチを使用して大規模なデータ コピー パイプラインを作成できます (パブリック プレビュー)。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/get-metadata-driven-data-ingestion-pipelines-on-adf-within-10/ba-p/2528219">詳細情報</a></td></tr>
<tr><td><b>データ フロー</b></td><td>データ フロー変換関数に新しいマップ関数が追加されました</td><td>データ エンジニアがマップのデータ型と複雑なマップ構造を簡単に生成、読み取り、および更新できるように、データ フロー変換関数の新しいセットが追加されました。<br><a href="data-flow-expression-functions.md#map-functions">詳細情報</a></td></tr>
<tr><td><bpt id="p1"><b></bpt>Integration Runtime<ept id="p1"></b></ept></td><td>Azure Data Factory のマネージド VNET で 5 つの新しいリージョンが利用可能になりました (パブリック プレビュー)</td><td>これらの 5 つの新しいリージョン (中国東部 2、中国北部 2、US Gov アリゾナ、US Gov テキサス、US Gov バージニア) は、Azure Data Factory のマネージド仮想ネットワークで利用できます (パブリック プレビュー)。<br><a href="managed-virtual-network-private-endpoint.md#azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions">詳細情報</a></td></tr>
<tr><td rowspan=2><bpt id="p1"><b></bpt>開発者の生産性<ept id="p1"></b></ept></td><td>ADF ホームページの機能強化</td><td>Data Factory のホームページが再設計されて、コントラストとリフロー機能が改善されました。 さらに、データ統合体験で生産性を向上させるためのいくつかのセクションがホームページに追加されました。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">詳細情報</a></td></tr>
<tr><td>Azure Data Factory Studio の新しいランディング ページ</td><td>Azure portal の Data Factory ブレードのランディング ページ。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">詳細情報</a></td></tr>
</table>

## <a name="june-2021"></a>2021 年 6 月
<br>
<table>
<tr><td><bpt id="p1"><b></bpt>サービス カテゴリ<ept id="p1"></b></ept></td><td><bpt id="p1"><b></bpt>サービスの機能強化<ept id="p1"></b></ept></td><td><b>詳細</b></td></tr>
<tr><td rowspan=4 valign="middle"><bpt id="p1"><b></bpt>データの移動<ept id="p1"></b></ept></td><td>Azure Data Factory データ コピー ツールを使用した新しいユーザー エクスペリエンス</td><td>再設計されたデータ コピー ツールが利用可能になり、データ インジェストの操作性が向上しました。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">詳細情報</a></td></tr>
<tr><td>MongoDB と MongoDB Atlas が、ソースとシンクの両方としてサポートされます</td><td>この機能強化により、サポートされている任意のデータ ストアと MongoDB または MongoDB Atlas データベース間でのデータのコピーがサポートされます。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">詳細情報</a></td></tr>
<tr><td>Always Encrypted が、ソースとシンクの両方として Azure SQL Database、Azure SQL Managed Instance、および SQL Server コネクタでサポートされます</td><td>Always Encrypted は、Azure Data Factory for Azure SQL Database、Azure SQL Managed Instance、およびコピー アクティビティ用の SQL Server コネクタで使用できます。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">詳細情報</a></td></tr>
<tr><td>ADLS Gen2 または Azure Blob へのシンク時に、コピー アクティビティでカスタム メタデータの設定がサポートされます</td><td>ADLS Gen2 または Azure Blob に書き込むときに、コピー アクティビティで、カスタム メタデータの設定、またはソース ファイルの最後に変更された情報をメタデータとして保存することがサポートされています。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">詳細情報</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>データ フロー</b></td><td>SQL Server がデータ フロー内のソースおよびシンクとしてサポートされるようになりました</td><td>SQL Server がデータ フロー内のソースおよびシンクとしてサポートされるようになりました。 Azure Integration Runtime マネージド VNET 機能を使用してネットワークを構成し、SQL Server のオンプレミスとクラウドの VM ベースのインスタンスと通信する方法の説明については、このリンクをたどってください。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">詳細情報</a></td></tr>
<tr><td>データフロー クラスターのクイック再利用が、すべての新しい Azure Integration Runtime で既定で有効になりました</td><td>ADF は、人気のデータ フロー クイック スタートアップ再利用機能が一般提供されたことをお知らせします。 すべての新しい Azure Integration Runtime で、クイック再利用が既定で有効になります。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">詳細情報</a></td></tr>
<tr><td>Power Query アクティビティ (パブリック プレビュー)</td><td>Azure Data Factory データ ラングリングを使用して、Power Query シンクに複雑なフィールド マッピングを構築できるようになりました。 シンクは、この更新プログラムに対応するために Power Query (パブリック プレビュー) アクティビティのパイプラインで構成されるようになりました。<br><a href="wrangling-tutorial.md">詳細情報</a></td></tr>
<tr><td>Azure Data Factory の更新されたデータ フローの監視 UI</td><td>Azure Data Factory には、データ フロー ETL ジョブの実行状況を簡単に確認し、パフォーマンス チューニングの領域をすばやく特定できるようにするための監視 UI 用の新しい更新プログラムがあります。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">詳細情報</a></td></tr>
<tr><td><bpt id="p1"><b></bpt>SQL Server Integration Services (SSIS)<ept id="p1"></b></ept></td><td>Azure Data Factory の SSIS を使用して、3 つの簡単な手順で任意の場所で任意の SQL を実行する</td><td>この投稿では、Azure Data Factory の SSIS を使用して、任意の場所で任意の SQL ステートメントまたはスクリプトを実行するための 3 つの簡単な手順について説明します。<ol><li>セルフホステッド統合ランタイムまたは SSIS Integration Runtime を準備します。</li><li>Azure Data Factory パイプラインで SSIS パッケージの実行アクティビティを準備します。</li><li>セルフホステッド統合ランタイムまたは SSIS Integration Runtime で SSIS パッケージの実行アクティビティを実行します。</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">詳細情報</a></td></tr>
</table>

## <a name="more-information"></a>説明を見る

- <bpt id="p1">[</bpt>ブログ - Azure Data Factory<ept id="p1">](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)</ept>
- [Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [ビデオ](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)
