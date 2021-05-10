---
title: 'Oracle から Azure SQL Database へ: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle のスキーマを Azure SQL Database に移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 9ae7e8c4544d2e8bd9dc4ff846aabb0c7f7f9358
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284257"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>移行ガイド: Oracle から Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、[SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Oracle (SSMA for Oracle) を使用して、Oracle のスキーマを Azure SQL Database に[移行する方法](https://azure.microsoft.com/migration/migration-journey)について説明します。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。

## <a name="prerequisites"></a>前提条件

Oracle スキーマの SQL Database への移行を始める前に:

- ソース環境がサポートされていることを確認する。
- [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) をダウンロードします。
- ターゲットの [SQL Database](../../database/single-database-create-quickstart.md) インスタンスを用意します。
- [SSMA for Oracle に必要なアクセス許可](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)と[プロバイダー](/sql/ssma/oracle/connect-to-oracle-oracletosql)を取得します。
 
## <a name="pre-migration"></a>移行前

前提条件を満たしたら、環境のトポロジを検出し、[Azure Cloud 移行](https://azure.microsoft.com/migration)の実現可能性を評価する準備が整いました。 プロセスのこの部分では、移行する必要があるデータベースのインベントリを実行し、移行に関する潜在的な問題や障害の有無についてそれらのデータベースを評価し、発見していなかったおそれがあるすべての項目を解決します。

### <a name="assess"></a>アクセス

SSMA for Oracle を使用すると、データベース オブジェクトとデータを確認し、移行についてデータベース オブジェクトを評価し、SQL Database にデータベース オブジェクトを移行して、最後にそのデータベースにデータを移行することができます。

評価を作成するには、次のようにします。

1. [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) を開きます。
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。
1. プロジェクト名と、プロジェクトを保存する場所を入力します。 次に、ドロップダウンリストから移行ターゲットとして **[Azure SQL Database]** を選択し、 **[OK]** を選択します。

   ![[Oracle への接続] を示すスクリーンショット。](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. **[Oracle への接続]** を選択します。 **[Oracle への接続]** ダイアログ ボックスで、Oracle への接続に関する詳細な値を入力します。

1. 移行する Oracle スキーマを選択します。

   ![Oracle スキーマの選択を示すスクリーンショット。](./media/oracle-to-sql-database-guide/select-schema.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** で、移行する Oracle スキーマを右クリックし、 **[レポートの作成]** を選択して HTML レポートを生成します。 または、データベースを選択し、 **[レポートの作成]** タブを選択してもかまいません。

   ![[レポートの作成] を示すスクリーンショット。](./media/oracle-to-sql-database-guide/create-report.png)

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Oracle オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。

   例については、「 `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`」を参照してください。

   ![評価レポートを示すスクリーンショット。](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。

1. SSMA for Oracle で、 **[ツール]** を選択し、 **[プロジェクトの設定]** を選択します。
1. **[Type Mapping]\(型のマッピング\)** タブを選択します。

   ![[Type Mapping]\(型のマッピング\) を示すスクリーンショット。](./media/oracle-to-sql-database-guide/type-mappings.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** でテーブルを選択することにより、テーブルごとに型マッピングを変更できます。

### <a name="convert-the-schema"></a>スキーマの変換

スキーマを変換するには、次のようにします。

1. (省略可能) 動的またはアドホック クエリをステートメントに追加します。 ノードを右クリックし、 **[Add statements]\(ステートメントの追加\)** を選択します。
1. **[Azure SQL Database への接続]** タブを選択します。
    1. **[SQL Database]** で、データベースに接続するための接続の詳細を入力します。
    1. ドロップダウン リストからターゲットの SQL Database インスタンスを選択するか、新しい名前を入力します。その場合、データベースがターゲット サーバー上に作成されます。
    1. 認証の詳細を入力し、 **[接続]** を選択します。

    ![[Azure SQL Database への接続] への接続を示すスクリーンショット。](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** で Oracle スキーマを右クリックし、 **[Convert Schema]\(スキーマの変換\)** を選択します。 または、スキーマを選択してから、 **[Convert Schema]\(スキーマの変換\)** タブを選択します。

   ![[スキーマの変換] を示すスクリーンショット。](./media/oracle-to-sql-database-guide/convert-schema.png)

1. 変換が終了した後、変換されたオブジェクトと元のオブジェクトを比較および確認して、潜在的な問題を特定し、推奨事項に基づいてそれらに対処します。

   ![推奨スキーマの確認を示すスクリーンショット。](./media/oracle-to-sql-database-guide/table-mapping.png)

1. 変換された Transact-SQL テキストを元のストアド プロシージャと比較し、推奨事項を確認します。

   ![推奨の確認を示すスクリーンショット。](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. [出力] ペインで **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューの **[プロジェクトの保存]** を選択します。 このステップでは、スキーマを SQL Database に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行することができます。

## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了した後、次の手順で移行プロセスを実行します。 移行には、スキーマの発行とデータの移行という 2 つのステップが含まれます。

スキーマを発行し、データを移行するには:

1. **[Azure SQL Database Metadata Explorer]\(Azure SQL Database メタデータ エクスプローラー\)** の **[データベース]** ノードでデータベースを右クリックし、 **[Synchronize with Database]\(データベースと同期\)** を選択して、スキーマを発行します。

   ![[データベースとの同期] を示すスクリーンショット。](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. 自分のソース プロジェクトとターゲット間のマッピングを確認します。

   ![[Synchronize with the Database]\(データベースとの同期\) のレビューを示すスクリーンショット。](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** で移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択して、データを移行します。 または、 **[データの移行]** タブを選択することもできます。データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベース、 **[テーブル]** の順に展開してから、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。

   ![[データの移行] を示すスクリーンショット。](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Oracle と SQL Database 両方の接続の詳細を入力します。
1. 移行が完了したら、**データ移行レポート** を表示します。

   ![[データ移行レポート] を示すスクリーンショット。](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してお使いの SQL Database インスタンスに接続し、データとスキーマを確認して移行を検証します。

   ![SQL Server Management Studio での検証を示すスクリーンショット。](./media/oracle-to-sql-database-guide/validate-data.png)

または、SQL Server Integration Services を使用して移行を実行することもできます。 詳細については、次を参照してください。

- [SQL Server Integration Services の概要](/sql/integration-services/sql-server-integration-services)
- [Azure とハイブリッド データ移動のための SQL Server Integration Services](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>移行後

*移行* 段階が正常に完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 このタスクを実現するために、場合によってはアプリケーションの変更が必要になります。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) は、Java ソース コードを分析し、データ アクセス API の呼び出しとクエリを検出できるようにする Visual Studio Code の拡張機能です。 このツールキットを使用すると、新しいデータベース バックエンドをサポートするために対処する必要があることが、単一ペインのビューに表示されます。 詳細については、[Oracle からの Java アプリケーションの移行](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)に関するブログ記事を参照してください。

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。
1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題の調整、完全性の確認、およびワークロードのパフォーマンスの問題への対処のために非常に重要です。

> [!NOTE]
> これらの問題と、それらを軽減するための具体的な手順の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。

## <a name="migration-assets"></a>移行資産

この移行シナリオの詳細については、次のリソースを参照してください。 これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| **タイトルとリンク**                                                                                                                                          | **説明**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションまたはデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。                                                          |
| [Oracle インベントリ スクリプト成果物](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | この資産には、Oracle システム テーブルにヒットし、スキーマの種類、オブジェクトの種類、および状態別にオブジェクトの数を提供する PL/SQL クエリが含まれています。 また、各スキーマの生データの概算値と、各スキーマ内のテーブルのサイズ設定も提供されます。結果は CSV 形式で格納されます。                                                                                                               |
| [SSMA Oracle 評価コレクションと統合の自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | このリソースのセットでは、エントリとして .csv ファイル (プロジェクトのフォルダー内の sources.csv) を使用して、コンソール モードで SSMA 評価を実行するために必要な xml ファイルが生成されます。 source.csv は、既存の Oracle インスタンスのインベントリに基づいて、顧客によって提供されます。 出力ファイルは、AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml、および VariableValueFile.xml です。|
| [SSMA for Oracle の一般的なエラーとその修正方法](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle では、WHERE 句に非スカラー条件を割り当てることができます。 しかし、SQL Server ではこの種類の条件はサポートされていません。 その結果、SSMA for Oracle によって WHERE 句に非スカラー条件が含まれるクエリは変換されません。 代わりに、エラー O2SS0001 が生成されます。 このホワイト ペーパーでは、この問題とその解決方法について詳しく説明しています。          |
| [Oracle から SQL Server への移行に関するハンドブック](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | このドキュメントでは、Oracle スキーマを最新バージョンの SQL Server データベースに移行する場合に関連するタスクに焦点が当てられています。 移行によって機能の変更が必要な場合は、そのデータベースを使用するアプリケーションでの各変更によって生じる可能性のある影響について、慎重に検討する必要があります。                                                     |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次のステップ

- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- SQL Database の詳細については、以下を参照してください。
  - [Azure SQL Database の概要](../../database/sql-database-paas-overview.md)
  - [Azure 総保有コスト (TCO) 計算ツール](https://azure.microsoft.com/pricing/tco/calculator/)

- クラウド移行のためのフレームワークと導入サイクルの詳細については、次を参照してください。
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [クラウド移行リソース](https://azure.microsoft.com/migration/resources)

- ビデオ コンテンツについては、以下をご覧ください。
    - [移行の行程の概要、および評価と移行を実行するために推奨されるツールとサービス](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
