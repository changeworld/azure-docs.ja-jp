---
title: Azure Arc 対応 SQL Managed Instance の機能
description: Azure Arc 対応 SQL Managed Instance の機能
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c3f973da688a20fa60f2fb649b2c8d689bc2a98
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076651"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance の機能

Azure Arc 対応 SQL Managed Instance では、SQL Server の最新の安定したバージョンと共通のコード ベースが共有されています。 標準の SQL 言語、クエリ処理、データベース管理機能のほとんどは同一です。 SQL Server と SQL Database または SQL Managed Instance に共通する機能は次のとおりです。

- 言語機能 - [フロー制御言語のキーワード](/sql/t-sql/language-elements/control-of-flow)、[カーソル](/sql/t-sql/language-elements/cursors-transact-sql)、[データ型](/sql/t-sql/data-types/data-types-transact-sql)、[DML ステートメント](/sql/t-sql/queries/queries)、[述語](/sql/t-sql/queries/predicates)、[シーケンス番号](/sql/relational-databases/sequence-numbers/sequence-numbers)、[ストアド プロシージャ](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)、[変数](/sql/t-sql/language-elements/variables-transact-sql)。
- データベース機能 - [自動チューニング (プランの強制)](/sql/relational-databases/automatic-tuning/automatic-tuning)、[変更の追跡](/sql/relational-databases/track-changes/about-change-tracking-sql-server)、[データベース照合順序](/sql/relational-databases/collations/set-or-change-the-database-collation)、[包含データベース](/sql/relational-databases/databases/contained-databases)、[包含ユーザー](/sql/relational-databases/security/contained-database-users-making-your-database-portable)、[データ圧縮](/sql/relational-databases/data-compression/data-compression)、[データベースの構成設定](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)、[オンライン インデックス操作](/sql/relational-databases/indexes/perform-index-operations-online)、[パーティション分割](/sql/relational-databases/partitions/partitioned-tables-and-indexes)、[テンポラル テーブル](/sql/relational-databases/tables/temporal-tables) ([ファースト ステップ ガイドを参照](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables))。
- セキュリティ機能 - [アプリケーション ロール](/sql/relational-databases/security/authentication-access/application-roles)、[動的データ マスク](/sql/relational-databases/security/dynamic-data-masking) ([Azure portal で SQL Database 動的データ マスクを使い始める](../../azure-sql/database/dynamic-data-masking-configure-portal.md))、[行レベル セキュリティ](/sql/relational-databases/security/row-level-security)
- マルチモデル機能 - [グラフの処理](/sql/relational-databases/graphs/sql-graph-overview)、[JSON データ](/sql/relational-databases/json/json-data-sql-server)、[OPENXML](/sql/t-sql/functions/openxml-transact-sql)、[空間](/sql/relational-databases/spatial/spatial-data-sql-server)、[OPENJSON](/sql/t-sql/functions/openjson-transact-sql)、[XML インデックス](/sql/t-sql/statements/create-xml-index-transact-sql)。



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance の機能

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS High Availability  
  
|機能|Azure Arc 対応 SQL Managed Instance|
|-------------|----------------|
|ログ配布|はい| 
|バックアップ圧縮|はい|
|データベース スナップショット|はい|
|Always On フェールオーバー クラスター インスタンス<sup>1</sup>| 適用外。 同様の機能を使用可能 |
|Always On 可用性グループ<sup>2</sup>|HA 機能が計画されています。|
|基本的な可用性グループ <sup>2</sup>|HA 機能が計画されています。|
|最小レプリカ コミット可用性グループ <sup>2</sup>|HA 機能が計画されています。|
|クラスターを使用しない可用性グループ|はい|
|オンライン ページおよびファイルの復元|はい|
|オンラインのインデックス構築|はい|
|再開可能なオンライン インデックス再構築|はい|
|オンラインのスキーマ変更|はい|
|高速復旧|はい|
|ミラー化バックアップ|はい|
|ホット アド メモリと CPU|はい|
|暗号化されたバックアップ|はい|
|Azure へのハイブリッド バックアップ (URL へのバックアップ)|はい|

<sup>1</sup> ポッドで障害が発生した場合は、新しい SQL Managed Instance が開始され、データが含まれる永続ボリュームに再アタッチされます。 [Kubernetes の永続ボリュームの詳細については、こちらを参照してください](https://kubernetes.io/docs/concepts/storage/persistent-volumes)。

<sup>2</sup> 今後のリリースで AG 機能が提供されます 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS Scalability and Performance  

| 機能 | Azure Arc 対応 SQL Managed Instance |
|--|--|
| 列ストア | はい |
| クラスター化列ストア インデックス内のラージ オブジェクト バイナリ | はい |
| オンライン非クラスター化列ストア インデックスの再構築 | はい |
| インメモリ OLTP | はい |
| 恒久的なメイン メモリ | はい |
| テーブルとインデックスのパーティション分割 | はい |
| データ圧縮 | はい |
| [リソース ガバナー] | はい |
| パーティション テーブルの並列処理 | はい |
| NUMA 対応のラージ ページ メモリとバッファー配列の割り当て | はい |
| IO リソース管理 | はい |
| 遅延持続性 | はい |
| 自動調整 | はい |
| バッチ モードの適応型結合 | はい |
| バッチ モード メモリ許可フィードバック | はい |
| 複数ステートメントのテーブル値関数のインターリーブ実行 | はい |
| 一括挿入の機能強化 | はい |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS Security

| 機能 | Azure Arc 対応 SQL Managed Instance |
|--|--|
| 行レベルのセキュリティ | はい |
| Always Encrypted | はい |
| セキュリティで保護されたエンクレーブが設定された Always Encrypted | いいえ |
| 動的データ マスク | はい |
| 基本的な監査 | はい |
| 詳細な監査 | はい |
| 透過的なデータベースの暗号化 | はい |
| ユーザー定義ロール | はい |
| 包含データベース | はい |
| バックアップの暗号化 | はい |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS Manageability  

| 機能 | Azure Arc 対応 SQL Managed Instance |
|--|--|
| 専用管理者接続 | はい |
| PowerShell スクリプティングのサポート | はい |
| データ層アプリケーション コンポーネントの操作のサポート - 抽出、配置、アップグレード、削除 | はい |
| ポリシー オートメーション (変更時とスケジュールに基づいて確認) | はい |
| パフォーマンス データ コレクター | はい |
| 標準的なパフォーマンス レポート | はい |
| プラン ガイドおよびプラン ガイドの固定計画 | はい |
| NOEXPAND ヒントを使用したインデックス付きビューの直接クエリ | はい |
| インデックス付きビューの自動メンテナンス | はい |
| 分散パーティション ビュー | はい |
| 並列インデックス操作 | はい |
| クエリ オプティマイザーによる自動的なインデックス付きのビュー使用 | はい |
| 並列整合性チェック | はい |

### <a name="programmability"></a><a name="Programmability"></a> Programmability  

| 機能 | Azure Arc 対応 SQL Managed Instance |
|--|--|
| JSON | はい |
| クエリ ストア | はい | 
| テンポラル | はい | 
| ネイティブ XML サポート | はい | 
| XML インデックスの作成 | はい | 
| MERGE と UPSERT の機能 | はい | 
| 日付および時刻データ型 | はい | 
| 国際化サポート | はい | 
| フルテキストおよびセマンティック検索 | いいえ |
| クエリ内の言語指定 | はい | 
| Service Broker (メッセージング) | はい | 
| Transact-SQL エンドポイント | はい | 
| グラフ | はい | 
| Machine Learning サービス | いいえ |
| PolyBase | いいえ |


### <a name="tools"></a>ツール

Azure Arc 対応 SQL Managed Instance では、データの管理に役立つさまざまなデータ ツールがサポートされています。

| **ツール** | Azure Arc 対応 SQL Managed Instance|
| --- | --- | --- |
| Azure portal <sup>1</sup> | いいえ |
| Azure CLI | いいえ |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | はい |
| Azure PowerShell | はい |
| [BACPAC ファイル (エクスポート)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | はい |
| [BACPAC ファイル (インポート)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | はい |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | はい |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | はい |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | はい |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | はい |

<sup>1</sup> プレビュー期間中、Azure portal は、Azure Arc 対応 SQL Managed Instance を読み取り専用モードで表示するためにのみ使用されます。


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> サポートされていない機能とサービス

次の機能とサービスは、Azure Arc 対応 SQL Managed Instance では利用できません。 これらの機能のサポートは、今後ますます使用可能になります。

| 領域 | サポートされていない機能またはサービス |
|-----|-----|
| **データベース エンジン** | マージ レプリケーション |
| &nbsp; | Stretch DB |
| &nbsp; | サード パーティの接続を使用した分散クエリ |
| &nbsp; | SQL Server と Azure SQL 製品以外のデータ ソースへのリンク サーバー |
| &nbsp; | システム拡張ストアド プロシージャ (XP_CMDSHELL など) |
| &nbsp; | FileTable、FILESTREAM |
| &nbsp; | EXTERNAL_ACCESS 権限または UNSAFE 権限が設定された CLR アセンブリ |
| &nbsp; | バッファー プール拡張 |
| **SQL Server エージェント** |  サブシステム: CmdExec、PowerShell、キュー リーダー、SSIS、SSAS、SSRS |
| &nbsp; | 警告 |
| &nbsp; | 管理対象のバックアップ |
| **高可用性** | データベース ミラーリング  |
| **Security** | 拡張キー管理 |
| &nbsp; | リンク サーバーに対する AD 認証 | 
| &nbsp; | 可用性グループに対する AD 認証 (AG) | 
