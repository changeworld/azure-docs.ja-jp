---
title: 'Oracle から SQL Database: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Oracle (SSMA for Oracle) を使用して、Oracle スキーマを Azure SQL Database に移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: f00740de5a327858fd250a0cb561b07c32f3b726
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655490"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>移行ガイド: Oracle から Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle スキーマを Azure SQL Database に移行する方法について説明します。

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件

Oracle スキーマを SQL Database に移行するには、以下が必要です。 

- ソース環境がサポートされていることを確認する。 
- [SQL Server Migration Assistant (SSMA) for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) をダウンロードする。 
- ターゲットの [Azure SQL Database](../../database/single-database-create-quickstart.md)。 
- [SSMA for Oracle に必要なアクセス許可](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)と[プロバイダー](/sql/ssma/oracle/connect-to-oracle-oracletosql)。
 

## <a name="pre-migration"></a>移行前

前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。 プロセスのこの部分では、移行する必要があるデータベースのインベントリを実行し、移行に関する潜在的な問題や障害の有無についてそれらのデータベースを評価し、発見していなかったおそれがあるすべての項目を解決します。



### <a name="assess"></a>アクセス 


SQL Server Migration Assistant (SSMA) for Oracle を使用して、データベース オブジェクトとデータを確認し、移行についてデータベース オブジェクトを評価し、Azure SQL Database にデータベース オブジェクトを移行し、最後にそのデータベースにデータを移行します。 


評価を作成するには、次の手順を行います。 


1. [SQL Server Migration Assistant for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名とプロジェクトを保存する場所を指定し、ドロップダウンから移行ターゲットとして [Azure SQL Database] を選択します。 **[OK]** を選択します。

   ![[新しいプロジェクト]](./media/oracle-to-sql-database-guide/new-project.png)


1. **[Oracle への接続]** を選択します。 **[Oracle への接続]** ダイアログ ボックスで、Oracle 接続の詳細の値を入力します。

   ![Oracle への接続](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

   移行する Oracle スキーマを選択します。 

   ![Oracle スキーマを選択する](./media/oracle-to-sql-database-guide/select-schema.png)

1. **Oracle メタデータ エクスプローラー** で、移行する Oracle スキーマを右クリックし、 **[レポートの作成]** を選択します。 これにより、HTML レポートが生成されます。 または、データベースを選択した後、ナビゲーション バーの **[レポートの作成]** を選択することもできます。

   ![レポートの作成](./media/oracle-to-sql-database-guide/create-report.png)

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Oracle オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。

   例: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![評価レポート](./media/oracle-to-sql-database-guide/assessment-report.png) 



### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]\(型のマッピング\)** タブを選択します。 

   ![型マッピング](./media/oracle-to-sql-database-guide/type-mappings.png)

1. **[Oracle Metadata Explorer]** でテーブルを選択して、各テーブルの型マッピングを変更できます。

### <a name="convert-schema"></a>スキーマの変換

スキーマを変換するには、次の手順を行います。 

1. (省略可能) 動的またはアドホック クエリをステートメントに追加します。 ノードを右クリックし、 **[Add statements]\(ステートメントの追加\)** を選択します。
1. **[Azure SQL Database に接続する]** を選択します。 
    1. Azure SQL Database でデータベースに接続するための接続の詳細を入力します。
    1. ドロップダウンからターゲットの SQL Database を選択します。
    1. **[接続]** を選択します。

    ![SQL Database への接続](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)


1. **[Oracle メタデータ エクスプローラー]** で Oracle スキーマを右クリックし、 **[スキーマの変換]** を選択します。 または、スキーマを選択した後、上部のナビゲーション バーから **[スキーマの変換]** を選択することもできます。

   ![スキーマの変換](./media/oracle-to-sql-database-guide/convert-schema.png)

1. 変換が完了したら、変換されたオブジェクトと元のオブジェクトを比較および確認して、潜在的な問題を特定し、推奨事項に基づいて対処します。

   ![推奨事項の確認 - スキーマ](./media/oracle-to-sql-database-guide/table-mapping.png)

   変換された Transact-SQL テキストを元のストアド プロシージャと比較し、推奨事項を確認します。 

   ![推奨事項の確認](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。

## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。 移行には、スキーマの公開とデータの移行という 2 つの手順が必要です。 

スキーマを発行し、データを移行するには、次の手順を行います。

1. スキーマを発行する: **Azure SQL Database メタデータ エクスプローラー** の **[データベース]** ノードからデータベースを右クリックし、 **[データベースとの同期]** を選択します。

   ![データベースと同期する](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

   自分のソース プロジェクトと自分のターゲット間のマッピングを確認します。

   ![データベースとの同期の確認](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)


1. データを移行する: **[Oracle Metadata Explorer]** でスキーマを右クリックし、 **[データの移行]** を選択します。 または、スキーマの選択後、一番上の行のナビゲーション バーから **[データの移行]** を選択することができます。 

   ![データの移行](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Oracle と Azure SQL Database の両方の接続の詳細を指定します。
1. **データ移行レポート** を確認します。

   ![データ移行レポート](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して Azure SQL Database に接続し、データとスキーマを確認して移行を検証します。

   ![SSMA で検証する](./media/oracle-to-sql-database-guide/validate-data.png)

または、SQL Server Integration Services (SSIS) を使用して移行を実行することもできます。 詳細については、次を参照してください。 

- [SQL Server Migration Assistant: Microsoft 以外のデータ プラットフォームのデータを評価して SQL Server に移行する方法](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)
- [SQL Server Integration Services の概要](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services: Azure の SSIS とハイブリッド データ移動](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)


## <a name="post-migration"></a>移行後 

**移行** 段階が正常に完了したら、移行後の一連のタスクを実行し、すべてが可能な限り円滑かつ効率的に機能していることを保証する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) は、Java ソース コードを分析し、データ アクセス API の呼び出しとクエリを検出できるようにする Visual Studio Code の拡張機能であり、新しいデータベース バックエンドをサポートするために対処する必要があるものを 1 つのペイン ビューで提供します。 詳細については、[Oracle からの Java アプリケーションの移行](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)に関するブログを参照してください。 



### <a name="perform-tests"></a>テストを実行する

データベース移行のテスト アプローチは、次のアクティビティの実行で構成されています。

1.  **検証テストを作成します**。 データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

2.  **テスト環境を設定します**。 テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。

3.  **検証テストを実行します**。 ソースとターゲットに対して検証テストを実行してから、結果を分析します。

4.  **パフォーマンス テストを実行します**。 ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。


### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題を調整するため、完全性を確認するため、およびワークロードのパフォーマンスの問題に対処するために非常に重要です。

> [!NOTE]
> これらの問題と、それらを軽減するための具体的な手順に関する追加の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。


## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| **タイトルとリンク**                                                                                                                                          | **説明**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。                                                          |
| [Oracle インベントリ スクリプト成果物](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | この資産には、Oracle システム テーブルにヒットし、スキーマの種類、オブジェクトの種類、および状態別にオブジェクトの数を提供する PL/SQL クエリが含まれています。 また、各スキーマの "生データ" の概算値と、各スキーマ内のテーブルのサイズ設定も提供します。結果は CSV 形式で格納されます。                                                                                                               |
| [SSMA Oracle 評価コレクションと統合の自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | このリソースのセットは、エントリとして .csv ファイル (プロジェクト フォルダー内の sources.csv) を使用して、コンソール モードで SSMA 評価を実行するために必要な xml ファイルを生成します。 source.csv は、既存の Oracle インスタンスのインベントリに基づいて、顧客によって提供されます。 出力ファイルは、AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml、および VariableValueFile.xml です。|
| [SSMA for Oracle の一般的なエラーとその修正方法](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle では、WHERE 句に非スカラー条件を割り当てることができます。 しかし、SQL Server ではこの種類の条件がサポートされていません。 その結果、SQL Server Migration Assistant (SSMA) for Oracle では、WHERE 句に非スカラー条件を含むクエリは変換されず、エラー O2SS0001 が生成されます。 このホワイト ペーパーでは、この問題とその解決方法について詳しく説明しています。          |
| [Oracle から SQL Server への移行に関するハンドブック](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | このドキュメントは、Oracle スキーマを最新バージョンの SQL Server ベースに移行する場合に関連するタスクに焦点を当てています。 移行によって機能の変更が必要な場合は、そのデータベースを使用するアプリケーションに対する各変更によって生じる可能性のある影響について慎重に検討する必要があります。                                                     |

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次のステップ

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関する記事を参照してください。

- Azure SQL Database の詳細については、以下を参照してください。 
  - [Azure SQL Database の概要](../../database/sql-database-paas-overview.md)
  - [Azure 総保有コスト (TCO) 計算ツール](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- ビデオ コンテンツについては、以下をご覧ください。 
    - [移行の行程の概要、および評価と移行を実行するために推奨されるツールとサービス](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)


