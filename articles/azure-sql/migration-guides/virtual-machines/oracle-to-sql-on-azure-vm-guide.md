---
title: 'Oracle から Azure VM 上の SQL Server: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle スキーマを Azure VM 上の SQL Server に移行する方法について説明します。
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 1767f1f990326e513393b8ce47e1ed8485f73849
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656478"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>移行ガイド: Oracle から Azure VM 上の SQL Server
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、SQL Server Migration Assistant for Oracle を使用して、Oracle スキーマを Azure VM 上の SQL Server に移行する方法について説明します。 

その他のシナリオについては、[データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。

## <a name="prerequisites"></a>前提条件 

Oracle スキーマを Azure VM 上の SQL Server に移行するには、以下が必要です。

- ソース環境がサポートされていることを確認する。
- [SQL Server Migration Assistant (SSMA) for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) をダウンロードする。
- ターゲットの [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md)。
- [SSMA for Oracle に必要なアクセス許可](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)と[プロバイダー](/sql/ssma/oracle/connect-to-oracle-oracletosql)。

## <a name="pre-migration"></a>移行前

クラウドへの移行の準備をする場合には、ソース環境がサポートされていること、および前提条件に対応していることを確認します。 これは、確実に効率よく移行を成功させるのに役立ちます。

プロセスのこの部分では、移行する必要があるデータベースのインベントリを実行し、移行に関する潜在的な問題や障害の有無についてそれらのデータベースを評価し、発見していなかったおそれがあるすべての項目を解決します。 

### <a name="discover"></a>発見

[MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) を使用して、既存のデータソースを特定し、ビジネスで使用されている機能の詳細を確認して、移行について理解を深め、移行を計画します。 このプロセスには、ネットワークをスキャンして、組織内のすべての Oracle インスタンスと共に、使用しているバージョンと機能を特定することが含まれます。

MAP Toolkit を使用してインベントリ スキャンを実行するには、次の手順に従います。 

1. [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) を開きます。
1. **[Create/Select database]\(データベースの作成/選択\)** を選択します。

   ![データベースの選択](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. **[インベントリ データベースの作成]** を選択し、新しく作成するインベントリ データベースの名前を入力し、簡単な説明を入力して、 **[OK]** を選択します。 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="インベントリ データベースを作成する":::

1. **[インベントリ データの収集]** を選択して、**インベントリと評価ウィザード** を開きます。 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="インベントリ データを収集する":::

1. **インベントリと評価ウィザード** で、 **[Oracle]** 、 **[次へ]** の順に選択します。 

   ![Oracle を選択する](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. ビジネス ニーズおよびご使用の環境に最も適したコンピューター検索オプションを選択し、 **[次へ]** を選択します。 

   ![ビジネス ニーズに最適なコンピューター検索オプションを選択する](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. 探索するシステムの資格情報を入力するか、新しい資格情報を作成し、 **[次へ]** を選択します。

    ![資格情報を入力する](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)

1. 資格情報の順序を設定し、 **[次へ]** を選択します。 

   ![資格情報の順序を設定する](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  

1. 検出する各コンピューターの資格情報を指定します。 コンピューターまたはマシンごとに固有の資格情報を使用するか、 **[すべてのコンピューターの資格情報]** の一覧を使用することを選択できます。  


   ![検出する各コンピューターの資格情報を指定する](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. 選択の概要を確認し、 **[完了]** を選択します。

   ![概要の確認](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)

1. スキャンが完了したら、 **[データ収集]** 概要レポートを表示します。 スキャンには数分かかる場合がありますが、時間はデータベースの数によって異なります。 完了したら、 **[閉じる]** を選択します。 

   ![コレクションの概要レポート](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. **[オプション]** を選択して、Oracle の評価とデータベースの詳細に関するレポートを生成します。 両方のレポートを生成するには、オプションを 1 つずつ選択します。


### <a name="assess"></a>アクセス

データ ソースを特定したら、[SQL Server Migration Assistant (SSMA) for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) を使用して、2 つの間のギャップを理解するために、SQL Server VM に移行する Oracle インスタンスを評価します。 Migration Assistant を使用すると、データベース オブジェクトとデータを確認し、データベースの移行の評価を行った上で、データベース オブジェクトを SQL Server に移行した後、データを SQL Server に移行することができます。

評価を作成するには、次の手順を行います。 

1. [SQL Server Migration Assistant (SSMA) for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名とプロジェクトを保存する場所を指定し、ドロップダウンから SQL Server 移行ターゲットを選択します。 **[OK]** を選択します。 

   ![新しいプロジェクト](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)

1. **[Oracle への接続]** を選択します。 **[Oracle への接続]** ダイアログ ボックスで、Oracle 接続の詳細の値を入力します。

   ![Oracle への接続](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   移行する Oracle スキーマを選択します。 

   ![Oracle スキーマを選択する](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)

1. **Oracle メタデータ エクスプローラー** で、移行する Oracle スキーマを右クリックし、 **[レポートの作成]** を選択します。 これにより、HTML レポートが生成されます。 または、データベースを選択した後、ナビゲーション バーの **[レポートの作成]** を選択することもできます。

   ![レポートの作成](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. **Oracle メタデータ エクスプローラー** で、Oracle スキーマを選択し、 **[レポートの作成]** を選択して、変換の統計とエラーまたは警告 (ある場合) を含む HTML レポートを生成します。
1. HTML レポートで変換の統計、およびエラーと警告を確認します。 これを分析して、変換の問題点と解決策を把握します。

   このレポートには、最初の画面で選択した SSMA プロジェクトのフォルダーからもアクセスできます。 上記の例では、report.xml ファイルを次の場所から見つけます。 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

    これを Excel で開き、Oracle オブジェクトのインベントリと、スキーマ変換の実行に必要な作業を把握します。

   ![変換レポート](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)



### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]\(型のマッピング\)** タブを選択します。 

   ![型マッピング](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. 各テーブルの型マッピングを変更するには、**Oracle メタデータ エクスプローラー** でテーブルを選択します。 



### <a name="convert-schema"></a>スキーマの変換

スキーマを変換するには、次の手順を行います。 

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。
1. 一番上の行のナビゲーション バーから **[SQL Server への接続]** を選択し、Azure VM 上の SQL Server の接続の詳細を指定します。 既存のデータベースへの接続を選択することも、新しい名前を指定することもできます。後者の場合、データベースはターゲット サーバー上に作成されます。

   ![SQL に接続する](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. **[Oracle メタデータ エクスプローラー]** で Oracle スキーマを右クリックし、 **[スキーマの変換]** を選択します。

   ![スキーマの変換](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)

1. スキーマの変換が完了したら、スキーマの構造を確認し、潜在的な問題を特定します。

   ![推奨事項の確認](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   変換された Transact-SQL テキストを元のストアド プロシージャと比較し、推奨事項を確認します。 

   ![推奨事項の確認 - コード](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存することができます。 このためには、 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。


## <a name="migrate"></a>Migrate

必要な前提条件を満たし、**移行前** 段階に関連するタスクを完了すると、スキーマとデータの移行を実行する準備が整います。 移行には、スキーマの公開とデータの移行という 2 つの手順が必要です。 


スキーマを発行し、データを移行するには、次の手順に従います。 

1. **SQL Server メタデータ エクスプローラー** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。 この操作により、Oracle スキーマが Azure VM 上の SQL Server に公開されます。 

   ![データベースと同期する](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   同期の状態を確認する: 

   ![同期の状態を確認する](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)


1. **Oracle メタデータ エクスプローラー** で Oracle スキーマを右クリックし、 **[データの移行]** を選択します。 または、一番上の行のナビゲーションから [データの移行] を選択することもできます。

   ![データの移行](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. ダイアログ ボックスで Oracle と Azure VM 上の SQL Server の接続の詳細を指定します。
1. 移行が完了したら、データ移行レポートを表示します。

    ![データ移行レポート](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して Azure VM 上の SQL Server に接続し、SQL Server インスタンス上のデータとスキーマを確認します。 

   ![SSMA で検証する](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)




SSMA を使用するだけでなく、SQL Server Integration Services (SSIS) を使用してデータを移行することもできます。 詳細については、次を参照してください。 
- [SQL Server Integration Services の概要](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services)に関する記事。
- ホワイト ペーパー「[SQL Server Integration Services: Azure の SSIS とハイブリッド データ移動](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)」。



## <a name="post-migration"></a>移行後 

**移行** 段階が正常に完了したら、移行後の一連のタスクを実行し、すべてが可能な限り円滑かつ効率的に機能していることを保証する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) は、Java ソース コードを分析し、データ アクセス API の呼び出しとクエリを検出できるようにする Visual Studio Code の拡張機能であり、新しいデータベース バックエンドをサポートするために対処する必要があるものを 1 つのペイン ビューで提供します。 詳細については、[Oracle からの Java アプリケーションの移行](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)に関するブログを参照してください。 

### <a name="perform-tests"></a>テストを実行する

データベース移行のテスト アプローチは、次のアクティビティの実行で構成されています。

1. **検証テストを作成します**。 データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

2. **テスト環境を設定します**。 テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。

3. **検証テストを実行します**。 ソースとターゲットに対して検証テストを実行してから、結果を分析します。

4. **パフォーマンス テストを実行します**。 ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題を調整するため、完全性を確認するため、およびワークロードのパフォーマンスの問題に対処するために非常に重要です。

> [!Note]
> これらの問題と、それらを軽減するための具体的な手順に関する追加の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。


## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| **タイトルとリンク**                                                                                                                                          | **説明**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。                                                          |
| [Oracle インベントリ スクリプト成果物](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | この資産には、Oracle システム テーブルにヒットし、スキーマの種類、オブジェクトの種類、および状態別にオブジェクトの数を提供する PL/SQL クエリが含まれています。 また、各スキーマの "生データ" の概算値と、各スキーマ内のテーブルのサイズ設定も提供します。結果は CSV 形式で格納されます。                                                                                                               |
| [SSMA Oracle 評価コレクションと統合の自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | このリソースのセットは、エントリとして .csv ファイル (プロジェクト フォルダー内の sources.csv) を使用して、コンソール モードで SSMA 評価を実行するために必要な xml ファイルを生成します。 source.csv は、既存の Oracle インスタンスのインベントリに基づいて、顧客によって提供されます。 出力ファイルは、AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml、および VariableValueFile.xml です。|
| [SSMA for Oracle の一般的なエラーとその修正方法](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle では、WHERE 句に非スカラー条件を割り当てることができます。 しかし、SQL Server ではこの種類の条件がサポートされていません。 その結果、SQL Server Migration Assistant (SSMA) for Oracle では、WHERE 句に非スカラー条件を含むクエリは変換されず、エラー O2SS0001 が生成されます。 このホワイト ペーパーでは、この問題とその解決方法について詳しく説明しています。          |
| [Oracle から SQL Server への移行に関するハンドブック](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | このドキュメントは、Oracle スキーマを最新バージョンの SQL Server に移行する場合に関連するタスクに焦点を当てています。 移行によって機能の変更が必要な場合は、そのデータベースを使用するアプリケーションに対する各変更によって生じる可能性のある影響について慎重に検討する必要があります。                                                     |

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次のステップ

- SQL Server に適用可能なサービスの可用性を確認するには、[Azure グローバル インフラストラクチャ センター](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)を参照してください

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関する記事を参照してください。

- Azure SQL の詳細については、以下を参照してください。
   - [デプロイ オプション](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM での SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- ライセンスの詳細については、以下を参照してください
   - [Azure ハイブリッド特典を使用するライセンス持ち込み](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 および SQL Server 2008 R2 の延長サポートの無料利用](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- アプリケーション アクセス レイヤーを評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。

