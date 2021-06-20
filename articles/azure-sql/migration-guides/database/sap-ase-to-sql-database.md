---
title: 'SAP ASE から Azure SQL Database: 移行ガイド'
description: このガイドでは、SAP Adapter Server Enterprise 用の SQL Server Migration Assistant を使用して、SAP ASE データベースを Azure SQL データベースに移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: c60d6ba1f4d3628f57b8149779318c3e049a9e24
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284240"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>移行ガイド: SAP ASE から Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、SAP Adapter Server Enterprise 用の [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant を使用して、SAP Adapter Server Enterprise (ASE) データベースを Azure SQL Database に[移行する方法](https://azure.microsoft.com/migration/migration-journey)について説明します。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。 

## <a name="prerequisites"></a>前提条件 

SAP SE データベースの SQL データベースへの移行を開始する前に、次を実行します。

- ソース環境がサポートされていることを確認します。 
- [SAP Adaptive Server Enterprise (以前の SAP Sybase ASE) 用の SQL Server Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=54256) をダウンロードしてインストールします。
- ソースとターゲットの両方にアクセスするための接続と十分なアクセス許可があることを確認します。

## <a name="pre-migration"></a>移行前

前提条件を満たしたら、環境のトポロジを検出し、[Azure Cloud 移行](https://azure.microsoft.com/migration)の実現可能性を評価する準備が整いました。

### <a name="assess"></a>アクセス

[SAP Adaptive Server Enterprise (以前の SAP Sybase ASE) 用の SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) を使用すると、データベース オブジェクトとデータを確認し、移行のためのデータベースを評価し、Sybase データベース オブジェクトを SQL データベースに移行した後、データを SQL データベースに移行することができます。 詳細については、「[SQL Server Migration Assistant for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)」を参照してください。

評価を作成するには、次の手順を実行します。 

1. SSMA for Sybaseを開きます。 
1. **[ファイル]** を選択してから、 **[新しいプロジェクト]** を選択します。 
1. **[新しいプロジェクト]** ペインで、プロジェクトの名前と場所を入力した後、 **[移行先]** ドロップダウン リストで **[Azure SQL Database]** を選択します。 
1. **[OK]** を選択します。
1. **[Sybase への接続]** ペインで、SAP 接続の詳細を入力します。 
1. 移行する SAP データベースを右クリックして **[レポートの作成]** を選択します。 これにより、HTML レポートが生成されます。 あるいは、右上にある **[レポートの作成]** タブを選択することもできます。
1. HTML レポートを確認して、変換の統計情報のほか、エラーや警告がないかどうかを把握します。 また、そのレポートを Excel で開いて、SAP ASE オブジェクトのインベントリや、スキーマ変換を実行するために必要な作業を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。 次に例を示します。

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>型マッピングを検証する

スキーマ変換を実行する前に、既定のデータ型マッピングを検証するか、またはそれらを要件に基づいて変更します。 それを **[ツール]**  >  **[プロジェクトの設定]** の順に選択して行うか、または **SAP ASE メタデータ エクスプローラー** でテーブルを選択してテーブルごとの型マッピングを変更することもできます。

### <a name="convert-the-schema"></a>スキーマの変換

スキーマを変換するには、次の手順を実行します。

1. (省略可能) 動的クエリまたは特殊なクエリを変換するには、ノードを右クリックして **[ステートメントの追加]** を選択します。 
1. **[Azure SQL Database に接続する]** タブを選択し、SQL データベースの詳細を入力します。 既存のデータベースへの接続を選択することも、新しい名前を指定することもできます。後者の場合、データベースはターゲット サーバー上に作成されます。
1. **[Sybase メタデータ エクスプローラー]** ペインで、操作している SAP ASE スキーマを右クリックして **[スキーマの変換]** を選択します。 
1. スキーマが変換されたら、変換された構造を元の構造と比較して潜在的な問題を識別します。 

   スキーマ変換の後、オフライン スキーマ修復の演習のために、このプロジェクトをローカルに保存できます。 それを行うには、 **[ファイル]**  >  **[プロジェクトの保存]** の順に選択します。 これにより、スキーマを SQL データベースに発行する前にソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が与えられます。

1. **[出力]** ペインで **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーがないかどうかを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 それを行うには、 **[ファイル]**  >  **[プロジェクトの保存]** の順に選択します。 これにより、スキーマを SQL データベースに発行する前にソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が与えられます。

## <a name="migrate-the-databases"></a>データベースを移行する 

必要な前提条件が揃い、*移行前* の段階に関連したタスクを完了したら、スキーマとデータの移行を実行する準備が整いました。

スキーマを発行し、データを移行するには、次の手順を実行します。 

1. スキーマを発行します。 **[Azure SQL Database メタデータ エクスプローラー]** ペインで、データベースを右クリックして **[データベースと同期する]** を選択します。 このアクションによって、SAP ASE スキーマが SQL データベースに発行されます。

1. データを移行します。 **[SAP ASE メタデータ エクスプローラー]** ペインで、移行する SAP ASE データベースまたはオブジェクトを右クリックして **[データの移行]** を選択します。 あるいは、右上にある **[データの移行]** タブを選択することもできます。 

   データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、 **[テーブル]** を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。 
1. 移行が完了したら、**データ移行レポート** を表示します。 
1. データとスキーマを確認することによって移行を検証します。 それを行うには、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して SQL データベースに接続します。

## <a name="post-migration"></a>移行後 

*移行* 段階が正常に完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソースとターゲットの両方のデータベースに対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。

1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。

1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。


### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題の調整、完全性の確認、およびワークロードのパフォーマンスの問題への対処のために非常に重要です。

これらの問題とそれを軽減するための手順の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。


## <a name="next-steps"></a>次のステップ

- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- Azure SQL Database の詳細については、以下を参照してください。
   - [SQL Database の概要](../../database/sql-database-paas-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/)  

- クラウド移行のためのフレームワークと導入サイクルの詳細については、次を参照してください。
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [クラウド移行リソース](https://azure.microsoft.com/migration/resources)

- アプリケーション アクセス レイヤーを評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。
