---
title: 'Db2 から Azure SQL Database: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Db2 (SSMA for Db2) を使用して、IMB Db2 データベースを Azure SQL Database に移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f20af8c61bbfbbbbc20c29470648c3df6a272396
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285498"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-database"></a>移行ガイド: IBM Db2 から Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、[SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Db2 を使用して、IBM Db2 データベースを Azure SQL Database に[移行する方法](https://azure.microsoft.com/migration/migration-journey)について説明します。 

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。 

## <a name="prerequisites"></a>前提条件 

Db2 データベースを SQL Database に移行するには、以下が必要です。

- [ソース環境がサポートされている](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites)ことを確認する。
- [SQL Server Migration Assistant (SSMA) for Db2](https://www.microsoft.com/download/details.aspx?id=54254) をダウンロードする。
- [Azure SQL Database](../../database/single-database-create-quickstart.md) 内のターゲット データベース。
- ソースとターゲットの両方への接続と、十分なアクセス許可。 

## <a name="pre-migration"></a>移行前

前提条件を満たしたら、環境のトポロジを検出し、[Azure Cloud 移行](https://azure.microsoft.com/migration)の実現可能性を評価する準備が整いました。

### <a name="assess-and-convert"></a>評価と変換

SSMA for Db2 を使用して、データベース オブジェクトとデータを確認し、データベースの移行を評価します。 

評価を作成するには、次の手順を行います。

1. [SSMA for Db2](https://www.microsoft.com/download/details.aspx?id=54254) を開きます。 
1. **[ファイル]**  >  **[新しいプロジェクト]** の順に選択します。 
1. プロジェクト名とプロジェクトを保存する場所を指定します。 次に、ドロップダウンリストから移行ターゲットとして [Azure SQL Database] を選択し、 **[OK]** を選択します。

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="指定するプロジェクトの詳細を示すスクリーンショット。":::


1. **[Db2 への接続]** で、Db2 接続の詳細の値を入力します。 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="Db2 インスタンスに接続するためのオプションを示すスクリーンショット。":::


1. 移行する Db2 スキーマを右クリックし、 **[レポートの作成]** を選択します。 これにより、HTML レポートが生成されます。 また、スキーマを選択した後、ナビゲーション バーから **[レポートの作成]** を選択することもできます。

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="レポートの作成方法を示すスクリーンショット。":::

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Db2 オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 このレポートの既定の場所は、*SSMAProjects* 内のレポート フォルダーです。

   たとえば、`drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>` です。 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="エラーまたは警告を特定するために確認するレポートのスクリーンショット。":::


### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]\(型のマッピング\)** タブを選択します。

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="スキーマと型のマッピングの選択を示すスクリーンショット。":::

1. **Db2 メタデータ エクスプローラー** でテーブルを選択し、各テーブルの型マッピングを変更します。 

### <a name="convert-schema"></a>スキーマの変換

スキーマを変換するには、次の手順を行います。

1. (省略可能) 動的またはアドホック クエリをステートメントに追加します。 ノードを右クリックし、 **[Add statements]\(ステートメントの追加\)** を選択します。 
1. **[Azure SQL Database に接続する]** を選択します。 
    1. Azure SQL Database でデータベースに接続するための接続の詳細を入力します。
    1. ドロップダウン リストからターゲット SQL データベースを選択するか、新しい名前を指定します。指定した場合は、ターゲット サーバー上にデータベースが作成されます。 
    1. 認証の詳細を指定します。 
    1. **[接続]** を選択します。

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="Azure 内の論理サーバーへの接続に必要な詳細を示すスクリーンショット。":::


1. スキーマを右クリックし、 **[スキーマの変換]** を選択します。 または、スキーマを選択した後、上部のナビゲーション バーから **[スキーマの変換]** を選択することもできます。

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="スキーマの選択と変換を示すスクリーンショット。":::

1. 変換が完了したら、スキーマの構造を比較、確認し、潜在的な問題を特定します。 推奨事項に基づいて問題に対処します。

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="スキーマの構造を比較、確認し、潜在的な問題を特定する方法を示すスクリーンショット。":::

1. **[出力]** ペインで、 **[結果の確認]** を選択します。 **[エラー一覧]** ペイン、エラーを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを SQL Database に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。

## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。

スキーマを発行し、データを移行するには、次の手順を行います。

1. スキーマを公開します。 **Azure SQL Database メタデータ エクスプローラー** の **[データベース]** ノードで、データベースを右クリックします。 次に、 **[データベースと同期する]** を選択します。

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="データベースと同期するオプションを示すスクリーンショット。":::

1. データを移行します。 **Db2 メタデータ エクスプローラー** で、移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、ナビゲーション バーから **[データの移行]** を選択することもできます。 データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、 **[テーブル]** を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="スキーマの選択とデータ移行の選択を示すスクリーンショット。":::

1. Db2 と Azure SQL Database の両方の接続の詳細を指定します。 
1. 移行が完了したら、**データ移行レポート** を表示します。  

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="データ移行レポートを確認する場所を示すスクリーンショット。":::

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、Azure SQL Database のデータベースに接続します。 データとスキーマを確認することによって移行を検証します。

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="SQL Server Management Studio のスキーマの比較を示すスクリーンショット。":::

## <a name="post-migration"></a>移行後 

移行が完了したら、移行後の一連のタスクを実行し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する 

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

テストは、次のアクティビティで構成されます。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。
1. **テスト環境を設定する**: テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

## <a name="advanced-features"></a>高度な機能 

[組み込みの高可用性](../../database/high-availability-sla.md)、[脅威検出](../../database/azure-defender-for-sql.md)、[ワークロードの監視と調整](../../database/monitor-tune-overview.md)など、SQL Database によって提供されるクラウドベースの高度な機能を利用してください。 

SQL Server の一部の機能は、[データベース互換レベル](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)を最新の互換性レベルに変更した場合にのみ使用できます。 

## <a name="migration-assets"></a>移行資産 

詳細については、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

|Asset  |説明  |
|---------|---------|
|[データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。|
|[Db2 zOS データ資産の検出および評価パッケージ](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|データベース上で SQL スクリプトを実行した後、結果をファイル システム上のファイルにエクスポートできます。 スプレッドシートなど、外部ツールで結果をキャプチャできるように、*.csv などの複数のファイル形式がサポートされています。 この方法を使用すると、ワークベンチをインストールしていないチームと結果を簡単に共有することができます。|
|[IBM Db2 LUW インベントリ スクリプトと成果物](https://github.com/microsoft/DataMigrationTeam/blob/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|この資産には、IBM Db2 LUW バージョン 11.1 システム テーブルを照会する SQL クエリが含まれます。このクエリでは、スキーマおよびオブジェクトの種類ごとのオブジェクトの数、各スキーマの "生データ" の概算値、および各スキーマのテーブルのサイズを取得し、結果を CSV 形式で格納します。|
|[Azure 上の Db2 LUW pureScale - 設定ガイド](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|このガイドは、Db2 の実装計画の開始点として役立ちます。 業務要件は違っても、同じ基本パターンが適用されます。 このアーキテクチャ パターンは、Azure 上の OLAP アプリケーションにも使用できます。|

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。



## <a name="next-steps"></a>次の手順

- さまざまなデータベースおよびデータ移行シナリオを支援するために使用できる Microsoft とサードパーティのサービスとツールについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページをご覧ください。

- Azure SQL Database の詳細については、以下を参照してください。
   - [SQL Database の概要](../../database/sql-database-paas-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 

- クラウド移行のためのフレームワークと導入サイクルの詳細については、次を参照してください。
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [クラウド移行リソース](https://azure.microsoft.com/migration/resources) 

- アプリケーション アクセス レイヤーを評価するには、「[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」をご覧ください。
- データ アクセス層の A/B テストの実行方法の詳細については、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) に関するページをご覧ください。
