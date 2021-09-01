---
title: 'Oracle から Azure SQL Database へ: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle のスキーマを Azure SQL Database に移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: cawrites
ms.author: chadam
ms.date: 08/25/2020
ms.openlocfilehash: 0a6f7bca2e6a3a1f0ac10f868ddeffe72d082c28
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746676"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>移行ガイド: Oracle から Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、[SQL Server Migration](https://azure.microsoft.com/migration/sql-server/) Assistant for Oracle (SSMA for Oracle) を使用して、Oracle スキーマを Azure SQL Database に[移行する](https://azure.microsoft.com/migration/migration-journey)方法について説明します。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](/data-migration)」を参照してください。

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

評価を作成するには:

1. [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) を開きます。
1. **[ファイル]** を選択してから、 **[新しいプロジェクト]** を選択します。
1. プロジェクト名と、プロジェクトを保存する場所を入力します。 次に、ドロップダウンリストから移行ターゲットとして **[Azure SQL Database]** を選択し、 **[OK]** を選択します。

   ![[Oracle への接続] を示すスクリーンショット。](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. **[Oracle への接続]** を選択します。 **[Oracle への接続]** ダイアログ ボックスで、Oracle への接続に関する詳細な値を入力します。

1. 移行する Oracle スキーマを選択します。

   ![Oracle スキーマの選択を示すスクリーンショット。](./media/oracle-to-sql-database-guide/select-schema.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** で、移行する Oracle スキーマを右クリックし、 **[レポートの作成]** を選択して HTML レポートを生成します。 代わりに、データベースを選択し、 **[レポートの作成]** タブを選択してもかまいません。

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

スキーマを変換するには:

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

   ![[Synchronize with Database]\(データベースと同期\) を示すスクリーンショット。](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. ソース プロジェクトとターゲットの間のマッピングを確認します。

   ![[Synchronize with the Database]\(データベースとの同期\) のレビューを示すスクリーンショット。](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. **[Oracle Metadata Explorer]\(Oracle メタデータ エクスプローラー\)** で移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択して、データを移行します。 または、 **[データの移行]** タブを選択することもできます。データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベース、 **[テーブル]** の順に展開してから、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。

   ![[データの移行] を示すスクリーンショット。](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Oracle と SQL Database 両方の接続の詳細を入力します。
1. 移行が完了したら、**データ移行レポート** を表示します。

   ![[Data Migration Report]\(データ移行レポート\) を示すスクリーンショット。](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してお使いの SQL Database インスタンスに接続し、データとスキーマを確認して移行を検証します。

   ![SQL Server Management Studio での検証を示すスクリーンショット。](./media/oracle-to-sql-database-guide/validate-data.png)

または、SQL Server Integration Services を使用して移行を実行することもできます。 詳細については、次を参照してください。

- [SQL Server Integration Services の概要](/sql/integration-services/sql-server-integration-services)
- [Azure とハイブリッド データ移動のための SQL Server Integration Services](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>移行後

*移行* 段階が正常に完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 このタスクを実行するには、場合によってはアプリケーションの変更が必要になります。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) は、Java ソース コードを分析し、データ アクセス API の呼び出しとクエリを検出できるようにする Visual Studio Code の拡張機能です。 このツールキットを使用すると、新しいデータベース バックエンドをサポートするために対処する必要があることが、単一ペインのビューに表示されます。 詳細については、[Oracle からの Java アプリケーションの移行](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)に関するブログ記事を参照してください。

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されます。

1. **検証テストを作成する**: データベース移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 その検証クエリでは、定義されているスコープに対応する必要があります。
1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行した後、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行した後、結果を分析および比較します。

### <a name="validate-migrated-objects"></a>移行されたオブジェクトを検証する

Microsoft SQL Server Migration Assistant for Oracle Tester (SSMA Tester) を使用すると、移行されたデータベース オブジェクトをテストできます。 SSMA Tester は、変換されたオブジェクトが同じように動作することを検証するために使用されます。

#### <a name="create-test-case"></a>テスト ケースの作成

1. SSMA for Oracle を開き、 **[Tester]\(Tester\)** 、 **[新しいテストケース]** の順に選択します。
   ![新しいテスト ケースの作成を示すスクリーンショット。](./media/oracle-to-sql-database-guide/ssma-tester-new.png)

1. 新しいテスト ケースに関して、次の情報を指定します。

   **[名前]:** テスト ケースを識別する名前を入力します。

   **[作成日]:** 今日の日付が自動的に定義されます。

   **[最終更新日]:** 自動的に入力され、変更することはできません。

   **[説明]:** テスト ケースの目的を示すための任意の追加情報を入力します。

   ![テスト ケースの初期化手順を示すスクリーンショット。](./media/oracle-to-sql-database-guide/tester-init-test-case.png)

1. 左側にある Oracle のオブジェクト ツリーから、テスト ケースの一部であるオブジェクトを選択します。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-select-configure-objects.png" alt-text="オブジェクトを選択して構成するステップを示すスクリーンショット。":::

   この例では、ストアド プロシージャ `ADD_REGION` とテーブル `REGION` が選択されています。

     詳細については、「[テストするオブジェクトの選択と構成](/sql/ssma/oracle/selecting-and-configuring-objects-to-test-oracletosql)」を参照してください。

1. 次に、左側のウィンドウの Oracle のオブジェクト ツリーから、テーブル、外部キー、その他の依存オブジェクトを選択します。

   :::image type="content" source="./media//oracle-to-sql-database-guide/tester-select-configure-affected.png" alt-text="影響を受けるオブジェクトを選択して構成するステップを示すスクリーンショット。":::

   詳細については、「[影響を受けるオブジェクトの選択と構成](/sql/ssma/oracle/selecting-and-configuring-affected-objects-oracletosql)」を参照してください。

1. オブジェクトの評価シーケンスを確認します。 順序を変更するにはグリッド内のボタンをクリックします。

   :::image type="content" source="./media/oracle-to-sql-database-guide/test-call-ordering.png" alt-text="テスト オブジェクトの実行シーケンスを指定するステップを示すスクリーンショット。":::

1. 前の手順で指定した情報を確認して、テスト ケースを最終処理します。 テスト シナリオに基づいて、テストの実行オプションを構成します。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-finalize-case.png" alt-text="オブジェクトを最終処理するステップを示すスクリーンショット。":::

   テスト ケースの設定の詳細については、「[テスト ケースの準備の終了](/sql/ssma/oracle/finishing-test-case-preparation-oracletosql)」を参照してください

1. [完了] をクリックして、テスト ケースを作成します。

   :::image type="content" source="./media//oracle-to-sql-database-guide/tester-test-repo.png" alt-text="リポジトリをテストするステップを示すスクリーンショット。":::

#### <a name="run-test-case"></a>テスト ケースを実行する

SSMA Tester でテスト ケースが実行されると、テスト対象に選択したオブジェクトがテスト エンジンによって実行されて、検証レポートが生成されます。

1. テスト リポジトリからテスト ケースを選択し、[実行] をクリックします。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-repo-run.png" alt-text="テスト リポジトリの確認を示すスクリーンショット。":::

1. 起動テスト ケースを確認し、[実行] をクリックします。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-run-test-case.png" alt-text="テスト ケースを実行するステップを示すスクリーンショット":::

1. 次に、Oracle ソースの資格情報を指定します。 資格情報を入力した後、[接続] をクリックします。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-oracle-connect.png" alt-text="Oracle ソースに接続するステップを示すスクリーンショット":::

1. 対象の SQL Server の資格情報を入力し、[接続] をクリックします。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-sql-connect.png" alt-text="SQL ターゲットに接続するステップを示すスクリーンショット。":::

   成功すると、テスト ケースは初期化ステージに移動します。

1. リアルタイムの進行状況バーに、テスト実行の実行状態が示されます。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-run-status.png" alt-text="テスト担当者にテストの進行状況を示すスクリーンショット。":::

1. テストの完了後にレポートを確認します。 レポートには、統計、テストの実行中に発生したエラー、および詳細レポートが表示されます。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-result.png" alt-text="サンプル テスターのテスト レポートを示すスクリーンショット":::

1. [詳細] をクリックすると、詳細情報が表示されます。

   合格したデータ検証の例。
   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-success.png" alt-text="サンプル テスターの合格レポートを示すスクリーンショット。":::

   失敗したデータ検証の例。

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-failed.png" alt-text="テスターの不合格レポートを示すスクリーンショット。":::

### <a name="optimize"></a>最適化

移行後の段階は、発生したデータの精度の問題を調整したり、完全性を検証したり、ワークロードでのパフォーマンスの問題に対処したりするために非常に重要です。

> [!NOTE]
> これらの問題と、それらを軽減するための具体的な手順の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。

## <a name="migration-assets"></a>移行資産

この移行シナリオの詳細については、次のリソースを参照してください。 これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| **タイトルとリンク**                                                                                                                                          | **説明**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://www.microsoft.com/download/details.aspx?id=103130) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションまたはデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。                                                          |
| [Oracle インベントリ スクリプト成果物](https://www.microsoft.com/download/details.aspx?id=103121)                 | この資産には、Oracle システム テーブルにヒットし、スキーマの種類、オブジェクトの種類、および状態別にオブジェクトの数を提供する PL/SQL クエリが含まれています。 また、各スキーマの生データの概算値と、各スキーマ内のテーブルのサイズ設定も提供されます。結果は CSV 形式で格納されます。                                                                                                               |
| [SSMA Oracle 評価コレクションと統合の自動化](https://www.microsoft.com/download/details.aspx?id=103120)                                             | このリソースのセットでは、エントリとして .csv ファイル (プロジェクトのフォルダー内の sources.csv) を使用して、コンソール モードで SSMA 評価を実行するために必要な xml ファイルが生成されます。 source.csv は、既存の Oracle インスタンスのインベントリに基づいて、顧客によって提供されます。 出力ファイルは、AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml、および VariableValueFile.xml です。|
| [Oracle から SQL DB - データベース比較ユーティリティ](https://www.microsoft.com/download/details.aspx?id=103016)|SSMA for Oracle Tester は、データベース オブジェクトの変換とデータ移行を自動的に検証するために推奨されるツールであり、データベース比較機能のスーパーセットです。<br /><br />別のデータ検証オプションを探している場合は、データベース比較ユーティリティを使用して、すべてのまたは選択したテーブル、行、および列の行または列レベルまでデータを比較できます。|

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
