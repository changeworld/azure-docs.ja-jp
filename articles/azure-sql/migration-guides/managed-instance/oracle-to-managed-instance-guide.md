---
title: 'Oracle から Azure SQL Managed Instance へ: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle のスキーマを Azure SQL Managed Instance に移行する方法について説明します。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2bb019a692178c5b44c3589d401d3b2b34c3dccb
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553908"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>移行ガイド: Oracle から Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

このガイドでは、SQL Server Migration Assistant for Oracle (SSMA for Oracle) を使用して、Oracle のスキーマを Azure SQL Managed Instance に移行する方法について説明します。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。

## <a name="prerequisites"></a>前提条件

Oracle スキーマの SQL Managed Instance への移行を始める前に:

- お使いのソース環境のサポートを確認します。
- [SSMA for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) をダウンロードします。
- [SQL Managed Instance](../../managed-instance/instance-create-quickstart.md) ターゲットを準備します。
- [SSMA for Oracle に必要なアクセス許可](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)と[プロバイダー](/sql/ssma/oracle/connect-to-oracle-oracletosql)を取得します。
 
## <a name="pre-migration"></a>移行前

前提条件を満たしたら、環境のトポロジを検出し、移行の実現可能性を評価する準備が整いました。 プロセスのこの部分では、移行する必要があるデータベースのインベントリを実行し、移行に関する潜在的な問題や障害の有無についてそれらのデータベースを評価し、発見していなかったおそれがあるすべての項目を解決します。

### <a name="assess"></a>アクセス

SSMA for Oracle を使用すると、データベース オブジェクトとデータを確認し、移行についてデータベース オブジェクトを評価し、SQL Managed Instance にデータベース オブジェクトを移行して、最後にそのデータベースにデータを移行することができます。

評価を作成するには:

1. [SSMA for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) を開きます。
1. **[ファイル]** を選択してから、 **[新しいプロジェクト]** を選択します。
1. プロジェクト名と、プロジェクトを保存する場所を入力します。 次に、ドロップダウンリストから移行ターゲットとして **[Azure SQL Managed Instance]** を選択し、 **[OK]** を選択します。

   ![[新しいプロジェクト] を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/new-project.png)

1. **[Oracle への接続]** を選択します。 **[Oracle への接続]** ダイアログ ボックスで、Oracle への接続に関する詳細な値を入力します。

   ![[Oracle への接続] を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

1. 移行する Oracle スキーマを選択します。

   ![Oracle スキーマの選択を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/select-schema.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** で、移行する Oracle スキーマを右クリックし、 **[レポートの作成]** を選択して HTML レポートを生成します。 または、データベースを選択し、 **[レポートの作成]** タブを選択してもかまいません。

   ![[レポートの作成] を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/create-report.png)

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Oracle オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。

   例については、「 `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`」を参照してください。

   ![評価レポートを示すスクリーンショット。](./media/oracle-to-managed-instance-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。

1. SSMA for Oracle で、 **[ツール]** を選択し、 **[プロジェクトの設定]** を選択します。
1. **[Type Mapping]\(型のマッピング\)** タブを選択します。

   ![[Type Mapping]\(型のマッピング\) を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** でテーブルを選択することにより、テーブルごとに型マッピングを変更できます。

### <a name="convert-the-schema"></a>スキーマの変換

スキーマを変換するには:

1. (省略可能) 動的またはアドホック クエリをステートメントに追加します。 ノードを右クリックし、 **[Add statements]\(ステートメントの追加\)** を選択します。
1. **[Azure SQL Managed Instance への接続]** タブを選択します。
    1. **SQL Database Managed Instance** でデータベースに接続するための接続の詳細を入力します。
    1. ドロップダウン リストから自分のターゲット データベースを選択するか、新しい名前を入力します。これにより、データベースはターゲット サーバー上に作成されます。
    1. 認証の詳細を入力し、 **[接続]** を選択します。

    ![[Azure SQL Managed Instance への接続] への接続を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** で Oracle スキーマを右クリックし、 **[Convert Schema]\(スキーマの変換\)** を選択します。 または、スキーマを選択してから、 **[Convert Schema]\(スキーマの変換\)** タブを選択します。

   ![[スキーマの変換] を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. 変換が終了した後、変換されたオブジェクトと元のオブジェクトを比較および確認して、潜在的な問題を特定し、推奨事項に基づいてそれらに対処します。

   ![テーブルの推奨事項の比較を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/table-comparison.png)

1. 変換された Transact-SQL テキストを元のコードと比較し、推奨事項を確認します。

   ![プロシージャの推奨事項の比較を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. [出力] ペインで **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューの **[プロジェクトの保存]** を選択します。 このステップでは、スキーマを SQL Managed Instance に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行することができます。

## <a name="migrate"></a>移行

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。 移行には、スキーマの発行とデータの移行という 2 つのステップが含まれます。

スキーマを発行し、データを移行するには:
1. **[Azure SQL Managed Instance Metadata Explorer]\(Azure SQL Managed Instance メタデータ エクスプローラー\)** の **[データベース]** ノードでデータベースを右クリックし、 **[Synchronize with Database]\(データベースと同期\)** を選択して、スキーマを発行します。

   ![[Synchronize with Database]\(データベースと同期\) を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)
   

1. ソース プロジェクトとターゲットの間のマッピングを確認します。

   ![[Synchronize with the Database]\(データベースとの同期\) のレビューを示すスクリーンショット。](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** で移行するスキーマまたはオブジェクトを右クリックし、 **[データの移行]** を選択して、データを移行します。 または、 **[データの移行]** タブを選択することもできます。データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベース、 **[テーブル]** の順に展開してから、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、チェック ボックスをオフにします。

   ![[データの移行] を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Oracle と SQL Managed Instance 両方の接続の詳細を入力します。
1. 移行が完了したら、**データ移行レポート** を表示します。

   ![[Data Migration Report]\(データ移行レポート\) を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して SQL Managed Instance のインスタンスに接続し、データとスキーマを確認して移行を検証します。

   ![SSMA for Oracle での検証を示すスクリーンショット。](./media/oracle-to-managed-instance-guide/validate-data.png)

または、SQL Server Integration Services を使用して移行を実行することもできます。 詳細については、次を参照してください。

- [SQL Server Integration Services の概要](/sql/integration-services/sql-server-integration-services)
- [Azure とハイブリッド データ移動のための SQL Server Integration Services](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>移行後

*移行* の段階を正常に完了したら、移行後の一連のタスクを完了して、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 このステップを実行するには、場合によってはアプリケーションの変更が必要になります。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) は、Java ソース コードを分析し、データ アクセス API の呼び出しとクエリを検出できるようにする Visual Studio Code の拡張機能です。 このツールキットを使用すると、新しいデータベース バックエンドをサポートするために対処する必要があることが、単一ペインのビューに表示されます。 詳細については、[Oracle からの Java アプリケーションの移行](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)に関するブログ記事を参照してください。

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されます。

1. **検証テストを作成する**: データベース移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 その検証クエリでは、定義されているスコープに対応する必要があります。
2. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。
3. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行した後、結果を分析します。
4. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行した後、結果を分析および比較します。

### <a name="optimize"></a>最適化

移行後の段階は、発生したデータの精度の問題を調整したり、完全性を検証したり、ワークロードでのパフォーマンスの問題に対処したりするために非常に重要です。

> [!NOTE]
> これらの問題とそれを軽減するための手順の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。

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

## <a name="next-steps"></a>次の手順

- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- SQL Managed Instance の詳細については、以下を参照してください。
  - [Azure SQL Managed Instance の概要](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure 総保有コスト (TCO) 計算ツール](https://azure.microsoft.com/en-us/pricing/tco/calculator/)

- クラウド移行のためのフレームワークと導入サイクルの詳細については、次を参照してください。
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- ビデオ コンテンツについては、以下をご覧ください。
    - [移行の行程の概要、および評価と移行を実行するために推奨されるツールとサービス](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
