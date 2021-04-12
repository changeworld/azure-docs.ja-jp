---
title: 'Access から Azure SQL Database: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Acces (SSMA for Acces) を使用して、Microsoft Access データベースを Azure SQL Database に移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e323b1c15d78da4e8c1a82ae8848df7f59b0dd87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657275"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>移行ガイド: Access から Azure SQL Database

この移行ガイドでは、SQL Server Migration Assistant for Acces を使用して、Microsoft Access データベースを Azure SQL Database に移行する方法について説明します。

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件

Access データベースを Azure SQL Database に移行するには、以下が必要です。

- ソース環境がサポートされていることを確認する。 
- [SQL Server Migration Assistant for Access](https://www.microsoft.com/download/details.aspx?id=54255). 

## <a name="pre-migration"></a>移行前

前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。


### <a name="assess"></a>アクセス 

[SQL Server Migration Assistant for Access](https://www.microsoft.com/download/details.aspx?id=54255) を使用して評価を作成します。 

評価を作成するには、次の手順を行います。 

1. SQL Server Migration Assistant for Access を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 移行プロジェクトの名前を指定します。 

   ![[新しいプロジェクト] を選択する](./media/access-to-sql-database-guide/new-project.png)

1. **[データベースの追加]** を選択し、新しいプロジェクトに追加するデータベースを選択します。 

   ![[データベースの追加] を選択する](./media/access-to-sql-database-guide/add-databases.png)

1. **[Access Metadata Explorer]** でデータベースを右クリックし、 **[レポートの作成]** を選択します。 

   ![データベースを右クリックして [レポートの作成] を選択する](./media/access-to-sql-database-guide/create-report.png)

1. サンプル評価を確認します。 次に例を示します。 

   ![レポートのサンプル評価を確認する](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]\(型のマッピング\)** タブを選択します。 

   ![型マッピング](./media/access-to-sql-database-guide/type-mappings.png)

1. **[Access Metadata Explorer]** でテーブルを選択し、各テーブルの型マッピングを変更します。


### <a name="convert-schema"></a>スキーマの変換

データベース オブジェクトを変換するには、次の手順に従います。 

1. **[Azure SQL Database に接続する]** を選択し、接続の詳細を指定します。

   ![Azure SQL Database に接続する](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. **[Access Metadata Explorer]** 内でデータベースを右クリックし、 **[スキーマの変換]** を選択します。 または、お使いのデータベースを選択した後、上部のナビゲーション バーから **[スキーマの変換]** を選択することもできます。

   ![データベースを右クリックして、[スキーマの変換] を選択します](./media/access-to-sql-database-guide/convert-schema.png)

   変換されたクエリを元のクエリと比較する: 

   ![変換されたクエリを元のコードと比較できます](./media/access-to-sql-database-guide/query-comparison.png)

   変換されたオブジェクトを元のオブジェクトと比較する: 

   ![変換されたオブジェクトを元のオブジェクトと比較できます](./media/access-to-sql-database-guide/table-comparison.png)

1. (省略可能) 個々のオブジェクトを変換するには、オブジェクトを右クリックし、 **[スキーマの変換]** を選択します。 変換されたオブジェクトが、 **[Access Metadata Explorer]** に太字で表示されます。 

   ![メタデータ エクスプローラーの太字のオブジェクトが変換されています](./media/access-to-sql-database-guide/converted-items.png)
 
1. [出力] ペインの **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。 


## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。 データの移行は、トランザクション内でデータの行を Azure SQL Database に移動する一括読み込み操作です。 各トランザクションで Azure SQL Database に読み込まれる行数は、プロジェクトの設定で構成されます。

SSMA for Access を使用してデータを移行するには、次の手順に従います。 

1. まだ行っていない場合は、 **[Connect to Azure SQL Database]\(Azure SQL Database への接続\)** を選択し、接続の詳細を指定します。 
1. **[Azure SQL Database Metadata Explorer]** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。 この操作により、MySQL スキーマが Azure SQL Database に公開されます。

   ![データベースと同期する](./media/access-to-sql-database-guide/synchronize-with-database.png)

   自分のソース プロジェクトと自分のターゲット間のマッピングを確認します。

   ![データベースとの同期を確認する](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. **[Access Metadata Explorer]** を使用して、移行する項目の横にあるチェック ボックスをオンにします。 データベース全体を移行する場合は、データベースの横にあるチェック ボックスをオンにします。 
1. 移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 
   データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、[テーブル] を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。

    ![データの移行](./media/access-to-sql-database-guide/migrate-data.png)

    移行されたデータを確認する: 

    ![データ移行の確認](./media/access-to-sql-database-guide/migrate-data-review.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して Azure SQL Database に接続し、データとスキーマを確認して移行を検証します。

   ![SSMA で検証する](./media/access-to-sql-database-guide/validate-data.png)



## <a name="post-migration"></a>移行後 

**移行** 段階が正常に完了したら、移行後の一連のタスクを実行し、すべてが可能な限り円滑かつ効率的に機能していることを保証する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データベース移行のテスト アプローチは、次のアクティビティの実行で構成されています。

  1. **検証テストを作成します**。 データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

  2. **テスト環境を設定します**。 テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。

  3. **検証テストを実行します**。 ソースとターゲットに対して検証テストを実行してから、結果を分析します。

  4. **パフォーマンス テストを実行します**。 ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題を調整するため、完全性を確認するため、およびワークロードのパフォーマンスの問題に対処するために非常に重要です。

これらの問題と、それらを軽減するための具体的な手順に関する追加の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。

## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するための追加のサポートについては、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| **タイトルとリンク**                                                                                                                                          | **説明**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。 |


これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次のステップ

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- Azure SQL Database の詳細については、次の記事を参照してください。
   - [SQL Database の概要](../../database/sql-database-paas-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- アプリケーション アクセス層を評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。

