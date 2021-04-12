---
title: 'MySQL から Azure SQL Database: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for MySQL (SSMA for MySQL) を使用して、MySQL データベースを Azure SQL Database に移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: ff7b2115b396bf42cdeffa9c58bffb1802e980d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721887"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>移行ガイド: MySQL から Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、SQL Server Migration Assistant for MySQL (SSMA for MySQL) を使用して、MySQL データベースを Azure SQL Database に移行する方法について説明します。 

その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件

MySQL データベースを Azure SQL Database に移行するには、以下が必要です。

- ソース環境がサポートされていることを確認する。 現時点では、MySQL 5.6 および 5.7 がサポートされています。 
- [SQL Server Migration Assistant for MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>移行前 

前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。

### <a name="assess"></a>アクセス 

[SQL Server Migration Assistant for MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257) を使用すると、データベース オブジェクトとデータを確認し、移行についてデータベースを評価できます。

評価を作成するには、次の手順を実行します。

1. SQL Server Migration Assistant for MySQL を開きます。 
1. メニューの **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 プロジェクト名と、プロジェクトを保存する場所を指定します。 移行ターゲットとして **[Azure SQL Database]** を選択します。 

   ![[新しいプロジェクト]](./media/mysql-to-sql-database-guide/new-project.png)

1. **[Connect to MySQL]\(MySQL への接続\)** を選択し、MySQL サーバーに接続するための接続の詳細を指定します。 

   ![MySQL への接続](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. **[MySQL Metadata Explorer]** で MySQL スキーマを右クリックし、 **[ﾚポートの作成]** を選択します。 または、一番上の行のナビゲーション バーから **[レポートの作成]** を選択することもできます。 

   ![レポートの作成](./media/mysql-to-sql-database-guide/create-report.png)

1. HTML レポートで変換の統計、およびエラーと警告を確認します。 これを分析して、変換の問題点と解決策を把握します。 

   このレポートには、最初の画面で選択した SSMA プロジェクトのフォルダーからもアクセスできます。 上記の例では、report.xml ファイルを次の場所から見つけます。
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   これを Excel で開き、MySQL オブジェクトのインベントリとスキーマ変換の実行に必要な作業を把握します。

    ![変換レポート](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]\(型のマッピング\)** タブを選択します。 

   ![型マッピング](./media/mysql-to-sql-database-guide/type-mappings.png)

1. **MySQL メタデータ エクスプローラー** でテーブルを選択することにより、各テーブルの型マッピングを変更できます。 

### <a name="convert-schema"></a>スキーマの変換 

スキーマを変換するには、次の手順を行います。 

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。 
1. 一番上の行のナビゲーション バーの **[Connect to Azure SQL Database]\(Azure SQL Database への接続\)** を選択し、接続の詳細を指定します。 既存のデータベースへの接続を選択することも、新しい名前を指定することもできます。後者の場合、データベースはターゲット サーバー上に作成されます。

   ![SQL に接続する](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. スキーマを右クリックして、 **[Convert schema]\(スキーマの変換\)** を選択します。 

   ![スキーマの変換](./media/mysql-to-sql-database-guide/convert-schema.png)

1. スキーマの変換が完了したら、変換されたコードを元のコードと比較して潜在的な問題を特定します。 

   変換されたオブジェクトを元のオブジェクトと比較する: 

   ![ オブジェクトの比較と確認 ](./media/mysql-to-sql-database-guide/table-comparison.png)

   変換されたプロシージャを元のプロシージャと比較する: 

   ![オブジェクト コードの比較と確認](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate"></a>Migrate 

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。 移行には、スキーマの公開とデータの移行という 2 つの手順が必要です。 

スキーマを発行し、データを移行するには、次の手順に従います。 

1. **[Azure SQL Database Metadata Explorer]** でデータベースを右クリックし、 **[データベースとの同期]** を選択します。 この操作により、MySQL スキーマが Azure SQL Database に公開されます。

   ![データベースと同期する](./media/mysql-to-sql-database-guide/synchronize-database.png)

   自分のソース プロジェクトと自分のターゲット間のマッピングを確認します。

   ![データベースとの同期の確認](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. **MySQL Metadata Explorer** で MySQL スキーマを右クリックし、 **[データの移行]** を選択します。 または、一番上の行のナビゲーションから **[データの移行]** を選択することもできます。 

   ![データの移行](./media/mysql-to-sql-database-guide/migrate-data.png)

1. 移行が完了したら、**データ移行** レポートを表示します。 

   ![データ移行レポート](./media/mysql-to-sql-database-guide/data-migration-report.png)

1.  SQL Server Management Studio (SSMS) を使用して Azure SQL Database 上のデータとスキーマを確認することで、移行を検証します。

    ![SSMA で検証する](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



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

| タイトルとリンク     | Description    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に非常に役立ちます。 |

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。

## <a name="next-steps"></a>次のステップ 

- 必ず「[Azure 総保有コスト (TCO) 計算ツール](https://aka.ms/azure-tco)」を確認して、ワークロードを Azure に移行することで実現できるコスト削減額を見積もることに役立てください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関する記事を参照してください。

- その他の移行ガイドについては、[データベースの移行](https://datamigration.microsoft.com/)に関するページを参照してください。 

ビデオについては、以下を参照してください。 
- [移行の行程の概要と、評価および移行を実行するために推奨されるツールとサービス](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
