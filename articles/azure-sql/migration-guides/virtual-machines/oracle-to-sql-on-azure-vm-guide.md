---
title: 'Oracle から Azure Virtual Machines 上の SQL Server へ: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle スキーマを Azure Virtual Machines 上の SQL Server に移行する方法について説明します。
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961253"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>移行ガイド: Oracle から Azure Virtual Machines 上の SQL Serverへ
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle スキーマを Azure Virtual Machines 上の SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、[データベースの移行](https://docs.microsoft.com/data-migration)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件 

Oracle スキーマを Azure Virtual Machines 上の SQL Server に移行するには、以下が必要です。

- サポートされているソース環境。
- [SQL Server Migration Assistant (SSMA) for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258)。
- ターゲットの [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md)。
- [SSMA for Oracle の必要なアクセス許可](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)と[プロバイダー](/sql/ssma/oracle/connect-to-oracle-oracletosql)。
- ソースとターゲットにアクセスするための接続と十分なアクセス許可。 


## <a name="pre-migration"></a>移行前

クラウドへの移行を準備するために、ソース環境がサポートされていること、および前提条件を満たしていることを確認します。 そうすることで、確実に効率よく移行を成功させるのに役立ちます。

このプロセスの一部として、以下が含まれます。 
- 移行する必要があるデータベースのインベントリを実行する。
- それらのデータベースに、潜在的な移行の問題や阻害要因がないか評価する。 
- 発見した問題を解決する。 

### <a name="discover"></a>発見

[MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) を使用して、既存のデータ ソースと、業務で使用している機能の詳細を特定します。 これを行うことで、移行についての理解が深まり、その計画に役立ちます。 このプロセスには、ネットワークをスキャンして、組織の Oracle インスタンスと使用しているバージョンおよび機能を特定することが含まれます。

MAP Toolkit を使用してインベントリ スキャンを実行するには、こちらの手順に従います。 


1. [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) を開きます。


1. **[Create/Select database]** \(データベースの作成/選択\) を選択します。

   ![[Create/Select database]\(データベースの作成/選択\) オプションを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. **[インベントリ データベースの作成]** を選択します。 新しく作成するインベントリ データベースの名前を入力し、簡単な説明を入力し、 **[OK]** を選択します。 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="インベントリ データベースを作成するためのインターフェイスを示すスクリーンショット。":::

1. **[インベントリ データの収集]** を選択して、**インベントリと評価ウィザード** を開きます。

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="[インベントリ データの収集] リンクを示すスクリーンショット。":::


1. **インベントリと評価ウィザード** で、 **[Oracle]** を選択し、 **[次へ]** を選択します。

   ![インベントリと評価ウィザードの [Inventory Scenarios]\(インベントリ シナリオ\) ページを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. ビジネス ニーズおよびご使用の環境に最も適したコンピューター検索オプションを選択し、 **[次へ]** を選択します。 

   ![インベントリと評価ウィザードの [Discovery Methods]\(探索方法\) ページを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. 探索するシステムの資格情報を入力するか、新しい資格情報を作成し、 **[次へ]** を選択します。

    ![インベントリと評価ウィザードの [All Computers Credentials]\(すべてのコンピューターの資格情報\) ページを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. 資格情報の順序を設定し、 **[次へ]** を選択します。 

   ![インベントリと評価ウィザードの [Credentials Order]\(資格情報の順序\) ページを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. 検出する各コンピューターの資格情報を入力します。 コンピューターまたはマシンごとに固有の資格情報を使用するか、[All Computers credentials]\(すべてのコンピューターの資格情報\) の一覧を使用できます。  

   ![インベントリと評価ウィザードの [Specify Computers and Credentials]\(コンピューターと資格情報の指定\) ページを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. 選択内容を確認し、 **[完了]** を選択します。

   ![インベントリと評価ウィザードの [概要] ページを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. スキャンが完了したら、 **[データ収集]** の概要を確認します。 スキャンには数分かかる場合がありますが、時間はデータベースの数によって異なります。 完了したら **[閉じる]** を選択します。 

   ![[データ収集] の概要を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. **[オプション]** を選択して、Oracle の評価とデータベースの詳細に関するレポートを生成します。 両方のオプションを、一度に 1 つずつ選択してレポートを生成します。


### <a name="assess"></a>アクセス

データ ソースを特定したら、[SQL Server Migration Assistant for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) を使用して、SQL Server VM に移行する Oracle インスタンスを評価します。 このアシスタントの支援により、移行元データベースと移行先データベース間の違いを理解できます。 データベース オブジェクトとデータを確認して、移行についてデータベースを評価し、データベース オブジェクトを SQL Server に移行してから、データを SQL Server に移行できます。

評価を作成するには、次の手順を行います。 


1. [SQL Server Migration Assistant for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) を開きます。 
1. **[ファイル]** メニューの **[新しいプロジェクト]** を選択します。 
1. プロジェクト名とプロジェクトの場所を指定し、一覧から SQL Server 移行ターゲットを選択します。 **[OK]** を選択します。 

   ![[新しいプロジェクト] ダイアログ ボックスを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. **[Oracle への接続]** を選択します。 **[Oracle への接続]** ダイアログ ボックスで、Oracle 接続のための値を入力します。

   ![[Oracle への接続] ダイアログ ボックスを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   移行する Oracle スキーマを選択します。 

   ![移行可能な Oracle スキーマの一覧を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. **[Oracle メタデータ エクスプローラー]** で、移行する Oracle スキーマを右クリックし、 **[レポートの作成]** を選択します。 これにより、HTML レポートが生成されます。 また、データベースを選択し、上部のメニューで **[レポートの作成]** を選択することもできます。

   ![レポートの作成方法を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. HTML レポートで、変換の統計情報、エラー、警告を確認します。 これを分析して、変換の問題点と解決策を把握します。

    また、Excel でレポートを開き、Oracle オブジェクトのインベントリと、スキーマ変換の実行に必要な作業量を確認することもできます。 このレポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。 

   例: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![変換レポートを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて、要件に基づき変更します。 これを行うには、次のステップに従います。 


1. **[ツール]** メニューの **[プロジェクトの設定]** を選択します。 
1. **[Type Mappings]\(型のマッピング\)** タブを選択します。 

   ![[Type Mapping]\(型のマッピング\) タブを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. **[Oracle メタデータ エクスプローラー]** でテーブルを選択することにより、テーブルごとに型のマッピングを変更できます。 

### <a name="convert-the-schema"></a>スキーマの変換

スキーマを変換するには、次の手順を行います。 

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。

1. 上部のメニューで **[SQL Server への接続]** を選択します。 
     1. Azure VM 上の SQL Server の接続詳細を入力します。 
     1. 一覧からターゲット データベースを選択するか、新しい名前を指定します。 新しい名前を指定すると、データベースがターゲット サーバーに作成されます。 
     1. 認証の詳細を指定します。 
     1. **[接続]** を選択します。 


   ![SQL Server への接続方法を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. **[Oracle メタデータ エクスプローラー]** で Oracle スキーマを右クリックし、 **[スキーマの変換]** を選択します。 また、上部のメニューで **[スキーマの変換]** を選択することもできます。

   ![スキーマの変換方法を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. スキーマ変換が完了したら、変換されたオブジェクトを確認し、それらを元のオブジェクトと比較して潜在的ｎ問題がないか判別します。 推奨事項を使用して、問題を解決します。

   ![2 つのスキーマの比較を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   変換された Transact-SQL テキストを元のストアド プロシージャと比較し、推奨事項を確認します。 

   ![Transact-SQL、ストアド プロシージャ、警告を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存することができます。 これを行うには、 **[ファイル]** メニューの **[プロジェクトの保存]** を選択します。 プロジェクトをローカルに保存することで、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行できます。

1. **[出力]** ペインで **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューの **[プロジェクトの保存]** を選択します。 これにより、スキーマを Azure Virtual Machines 上の SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行できます。


## <a name="migrate"></a>Migrate

必要な前提条件を満たし、移行前の段階に関連するタスクを完了したら、スキーマとデータの移行を開始する準備が整ったことになります。 移行には、スキーマの発行とデータの移行という 2 つのステップが含まれます。 


自分のスキーマを発行し、データを移行するには、次の手順を行います。 

1. スキーマを発行する: **[SQL Server メタデータ エクスプローラー]** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。 これにより、Oracle スキーマが Azure Virtual Machines 上の SQL Server に発行されます。 

   ![[データベースとの同期] コマンドを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   自分のソース プロジェクトと自分のターゲット間のマッピングを確認します。

   ![同期の状態を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. データを移行する: **[Oracle メタデータ エクスプローラー]** で、移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 また、上部のメニューの **[データの移行]** を選択することもできます。

   データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、 **[テーブル]** を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、該当するチェック ボックスをオフにします。

   ![[データの移行] コマンドを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. ダイアログ ボックスで Oracle と Azure Virtual Machines 上の SQL Server の接続詳細を指定します。
1. 移行が終了した後、 **[データ移行レポート]** を確認します。

    ![[データ移行レポート] を示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、Azure Virtual Machines インスタンス上の SQL Server に接続します。 データとスキーマを確認して移行を検証します。


   ![SSMA での SQL Server インスタンスを示すスクリーンショット。](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

SSMA を使用する代わりに、SQL Server Integration Services (SSIS) を使用してデータを移行することもできます。 詳細については、次を参照してください。 
- 記事「[SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services)」。
- ホワイト ペーパー「[SSIS による Azure およびハイブリッド データ移行](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)」。


## <a name="post-migration"></a>移行後 

移行段階が完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これらの変更を行うには、アプリケーションの変更が必要になることがあります。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) は、Visual Studio Code の拡張機能です。 これを使用すると、Java ソース コードを分析し、データ アクセス API の呼び出しとクエリを検出できます。 このツールキットでは、新しいデータベース バックエンドをサポートするために対処する必要があることが、単一ペインのビューに表示されます。 詳細については、[Oracle からの Java アプリケーションの移行](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)に関するページを参照してください。 

### <a name="perform-tests"></a>テストを実行する

データベースの移行をテストするには、こちらのアクティビティを実行します。

1. **検証テストを作成します**。 データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソースとターゲット両方のデータベースに対して実行する検証クエリを作成します。 検証クエリには、定義したスコープが含まれている必要があります。

2. **テスト環境を設定します**。 テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。

3. **検証テストを実行します**。 ソースとターゲットに対して検証テストを実行してから、結果を分析します。

4. **パフォーマンス テストを実行します**。 ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題を調整するため、および完全性を検証するために重要です。 また、ワークロードのパフォーマンスの問題に対処するうえでも重要です。

> [!Note]
> これらの問題について、およびそれらを軽減するための特定の手順について詳しくは、[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)に関するページを参照してください。


## <a name="migration-resources"></a>移行リソース 

この移行シナリオを完了するための追加の支援については、次のリソースを参照してださい。これらは、実際の移行プロジェクトをサポートするために開発されました。

| **タイトル/リンク**                                                                                                                                          | **説明**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールにより、特定のワークロードに対して、推奨される最適なターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルが提示されます。 シンプルなワンクリックの計算とレポート生成の機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているため、大規模な資産評価を素早く行うことができます。                                                          |
| [Oracle インベントリ スクリプト成果物](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | この資産には、Oracle システム テーブルをターゲットとし、スキーマの種類、オブジェクトの種類、および状態別にオブジェクトの数を提示する PL/SQL クエリが含まれます。 また、各スキーマの生データの概算値と、各スキーマ内のテーブルのサイズ設定も提示されます。結果は CSV 形式で保存されます。                                                                                                               |
| [SSMA Oracle 評価コレクションと統合の自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | このリソースのセットでは、エントリとして .csv ファイル (プロジェクト フォルダー内の sources.csv) を使用して、コンソール モードで SSMA 評価を実行するために必要な XML ファイルが生成されます。 既存の Oracle インスタンスのインベントリを取得することによって、source.csv ファイルを用意します。 出力ファイルは、AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml、および VariableValueFile.xml です。|
| [Oracle データベースの移行時の SSMA の問題と考えられる解決方法](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle では、WHERE 句に非スカラー条件を割り当てることができます。 SQL Server では、この種類の条件はサポートされません。 そのため、SSMA for Oracle では、WHERE 句に非スカラー条件が含まれるクエリは変換されません。 代わりに、エラー O2SS0001 が生成されます。 このホワイト ペーパーでは、この問題とその解決方法について詳しく説明しています。          |
| [Oracle から SQL Server への移行に関するハンドブック](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | このドキュメントは、Oracle スキーマを最新バージョンの SQL Server に移行する場合に関連するタスクに焦点を当てています。 移行で機能の変更が必要になる場合、データベースを使用するアプリケーションを変更したときに発生する影響について慎重に考慮する必要があります。                                                     |


データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主な目的は、Microsoft Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトのために、障害を取り除き、複雑な最新化を加速することです。


## <a name="next-steps"></a>次のステップ

- SQL Server に適用可能なサービスを利用できるかどうか確認するには、[Azure グローバル インフラストラクチャ センター](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)を参照してください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- Azure SQL の詳細については、以下を参照してください。
   - [デプロイ オプション](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure Virtual Machines における SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/)


- クラウド移行のためのフレームワークと導入サイクルの詳細については、次を参照してください。
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードのコストとサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- ライセンスの詳細については、以下を参照してください。
   - [Azure ハイブリッド特典を使用するライセンス持ち込み](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 および SQL Server 2008 R2 の延長サポートの無料利用](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- アプリケーション アクセス層を評価するには、[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)を使用してください。
- データ アクセス層の A/B テストの実行方法について詳しくは、「[Database Experimentation Assistant の概要](/sql/dea/database-experimentation-assistant-overview)」を参照してください。


