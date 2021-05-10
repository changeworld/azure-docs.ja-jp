---
title: 'Access から Azure SQL Database: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Acces (SSMA for Acces) を使用して、Microsoft Access データベースを Azure SQL データベースに移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: f9fa2426e371ab9fd99e88979cbcbbb34adb00d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643597"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>移行ガイド: Access から Azure SQL Database

このガイドでは、SQL Server Migration Assistant for Acces (SSMA for Acces) を使用して、Microsoft Access データベースを Azure SQL データベースに移行する方法について説明します。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。 

## <a name="prerequisites"></a>前提条件

Access データベースから SQL データベースへの移行を始める前に、次のことを行います。

- ソース環境がサポートされていることを確認する。 
- [SQL Server Migration Assistant for Access](https://www.microsoft.com/download/details.aspx?id=54255) をダウンロードしてインストールします。
- ソースとターゲットの両方にアクセスするための接続と十分なアクセス許可があることを確認します。

## <a name="pre-migration"></a>移行前

前提条件を満たしたら、環境のトポロジを検出し、移行の実現可能性を評価する準備が整いました。


### <a name="assess"></a>アクセス 

SSMA for Access を使用して、データベース オブジェクトとデータを確認し、データベースの移行を評価します。 

評価を作成するには、次の手順を実行します。 

1. [SSMA for Access](https://www.microsoft.com/download/details.aspx?id=54255) を開きます。 
1. **[ファイル]** を選択してから、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名とプロジェクトの場所を指定し、ドロップダウン リストから移行ターゲットとして **[Azure SQL Database]** を選択します。 
1. **[OK]** を選択します。 

   ![移行プロジェクトの名前と場所を入力するための [新しいプロジェクト] ペインのスクリーンショット。](./media/access-to-sql-database-guide/new-project.png)

1. **[データベースの追加]** を選択し、新しいプロジェクトに追加するデータベースを選択します。 

   ![SSMA for Access の [データベースの追加] タブのスクリーンショット。](./media/access-to-sql-database-guide/add-databases.png)

1. **[Access Metadata Explorer]** ペインで、データベースを右クリックし、 **[レポートの作成]** を選択します。 または、右上にある **[レポートの作成]** タブを選択することもできます。

   ![Access Metadata Explorer の [レポートの作成] コマンドのスクリーンショット。](./media/access-to-sql-database-guide/create-report.png)

1. HTML レポートを確認して、変換の統計情報のほか、エラーや警告がないかどうかを把握します。 また、Excel でレポートを開き、Access オブジェクトのインベントリを取得し、スキーマ変換の実行に必要な作業量を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。 次に例を示します。

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![SSMA のデータベース レポート評価の例のスクリーンショット。](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じてご自分の要件に基づいて変更します。 そのためには次を行います。

1. SSMA for Access で、 **[ツール]** を選択し、 **[プロジェクトの設定]** を選択します。 
1. **[Type Mapping]\(型のマッピング\)** タブを選択します。 

   ![SSMA for Access の[Type Mapping]\(型のマッピング\) ペインのスクリーンショット。](./media/access-to-sql-database-guide/type-mappings.png)

1. **[Access Metadata Explorer]** ペインでテーブル名を選択すると、各テーブルの型のマッピングを変更できます。


### <a name="convert-the-schema"></a>スキーマの変換

データベース オブジェクトを変換するには、次の操作を行います。 

1. **[Connect to Azure SQL Database]\(Azure SQL Database への接続\)** タブを選択し、次の操作を行います。

   a. SQL データベースへの接続に関する詳細を入力します。  
   b. ドロップダウン リストで、ターゲットの SQL データベースを選択します。 または、新しい名前を入力することもできます。その場合、データベースはターゲット サーバー上に作成されます。  
   c. 認証の詳細を指定します。   
   d. **[接続]** を選択します。

   ![接続の詳細を入力するための [Azure SQL Database に接続する] ペインのスクリーンショット。](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. **[Access Metadata Explorer]** ペインで、データベースを右クリックし、 **[スキーマの変換]** を選択します。 また、データベースを選択してから、 **[スキーマの変換]** タブを選択することもできます。

   ![[Access Metadata Explorer] ペインの [スキーマの変換] コマンドのスクリーンショット。](./media/access-to-sql-database-guide/convert-schema.png)

1. 変換が完了したら、変換されたオブジェクトと元のオブジェクトを比較して、潜在的な問題を特定し、推奨事項に基づいてそれらの問題に対処します。

   ![変換されたオブジェクトとソース オブジェクトの比較を示すスクリーンショット。](./media/access-to-sql-database-guide/table-comparison.png)

    変換された Transact-SQL テキストを元のコードと比較し、推奨事項を確認します。

   ![変換されたクエリとソース コードとの比較を示すスクリーンショット。](./media/access-to-sql-database-guide/query-comparison.png) 

1. (省略可能) 個々のオブジェクトを変換するには、そのオブジェクトを右クリックし、 **[スキーマの変換]** を選択します。 変換されたオブジェクトが、 **[Access Metadata Explorer]** に太字で表示されます。 

   ![Access Metadata Explorer 内のオブジェクトが変換されたことを示すスクリーンショット。](./media/access-to-sql-database-guide/converted-items.png)
 
1. **[出力]** ペインで **[結果の確認]** アイコンを選択し、 **[エラー一覧]** ペインでエラーを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 それを行うには、 **[ファイル]**  >  **[プロジェクトの保存]** の順に選択します。 これにより、ソースとターゲットのスキーマを SQL データベースに公開する前にオフラインで評価し、修復を実行できます。

## <a name="migrate-the-databases"></a>データベースを移行する

データベースを評価し、不整合に対処した後は、移行プロセスを実行できます。 データの移行は、トランザクション内でデータの行を Azure SQL Database に移動する一括読み込み操作です。 各トランザクションで SQL データベースに読み込まれる行数は、プロジェクトの設定で構成されます。

SSMA for Access を使用してスキーマを公開し、データを移行するには、次の操作を行います。 

1. まだ行っていない場合は、 **[Azure SQL Database に接続する]** を選択し、接続の詳細を指定します。 

1. スキーマを公開します。 **[Azure SQL Database Metadata Explorer]** ペインで、操作するデータベースを右クリックし、 **[データベースと同期する]** を選択します。 この操作により、MySQL スキーマが SQL データベースに公開されます。

1. **[Synchronize with the Database]\(データベースとの同期\)** ペインで、ソース プロジェクトとターゲットの間のマッピングを確認します。

   ![データベースとの同期を確認するための [Synchronize with the Database]\(データベースとの同期\) ペインのスクリーンショット。](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. **[Access Metadata Explorer]** ペインで、移行する項目の横にあるチェック ボックスをオンにします。 データベース全体を移行するには、そのデータベースの横にあるチェック ボックスをオンにします。 

1. データを移行します。 移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、右上にある **[データの移行]** タブを選択することもできます。  

   データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、 **[テーブル]** を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。

    ![[Access Metadata Explorer] ペインの [データの移行] コマンドのスクリーンショット。](./media/access-to-sql-database-guide/migrate-data.png)

1. 移行が完了した後、 **[データ移行レポート]** を表示します。  

    ![確認用のレポートの例が表示されている [データ移行レポート] ペインのスクリーンショット。](./media/access-to-sql-database-guide/migrate-data-review.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して Azure SQL データベースに接続し、データとスキーマを確認して移行を検証します。

   ![SSMA での移行を検証するための SQL Server Management Studio オブジェクト エクスプローラーのスクリーンショット。](./media/access-to-sql-database-guide/validate-data.png)

## <a name="post-migration"></a>移行後 

*移行* 段階が正常に完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソースとターゲットの両方のデータベースに対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。

1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。

1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行した後、結果を分析および比較します。


### <a name="optimize"></a>最適化

移行後の段階は、発生したデータの精度の問題を調整したり、完全性を検証したり、ワークロードでのパフォーマンスの問題に対処したりするために非常に重要です。

これらの問題と、それらを軽減するための具体的な手順の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。

## <a name="migration-assets"></a>移行資産 

この移行シナリオを完了するためのその他の支援については、次のリソースを参照してください。 これは、実世界の移行プロジェクトの取り組みをサポートするために開発されたものです。

| タイトル | 説明 |
| --- | --- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルを提供します。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に役立ちます。 |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次のステップ

- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- Azure SQL Database の詳細については、次の記事を参照してください。
   - [SQL Database の概要](../../database/sql-database-paas-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、次を参照してください。
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- アプリケーション アクセス レイヤーを評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法の詳細については、「[Database Experimentation Assistant の概要](/sql/dea/database-experimentation-assistant-overview)」を参照してください。
