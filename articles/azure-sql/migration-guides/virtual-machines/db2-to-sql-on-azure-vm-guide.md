---
title: 'Db2 から Azure VM 上の SQL Server: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Db2 を使用して、IBM Db2 データベースを Azure VM 上の SQL Server に移行する方法について説明します。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 43eff2bea6f6d95291e9ba9650ff42187e39fc70
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600167"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>移行ガイド: IBM Db2 から Azure VM 上の SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

このガイドでは、SQL Server Migration Assistant for Db2 を使用して、ユーザー データベースを IBM Db2 から Azure VM 上の SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。 

## <a name="prerequisites"></a>前提条件

お使いの Db2 データベースを SQL Server に移行するには、以下が必要です。

- [ソース環境がサポートされている](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)ことを確認する。
- [SQL Server Migration Assistant (SSMA) for Db2](https://www.microsoft.com/download/details.aspx?id=54254)。
- ソース環境と Azure 内の SQL Server VM 間の[接続](../../virtual-machines/windows/ways-to-connect-to-sql.md)。 
- [Azure VM 上のターゲット SQL Server](../../virtual-machines/windows/create-sql-vm-portal.md)。 

## <a name="pre-migration"></a>移行前

前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。 

### <a name="assess"></a>アクセス 

SSMA for Db2 を使用して、データベース オブジェクトとデータを確認し、データベースの移行を評価します。 

評価を作成するには、次の手順を行います。

1. [SSMA for Db2](https://www.microsoft.com/download/details.aspx?id=54254) を開きます。 
1. **[ファイル]**  >  **[新しいプロジェクト]** の順に選択します。 
1. プロジェクト名とプロジェクトを保存する場所を指定します。 その後、ドロップダウン リストから SQL Server の移行ターゲットを選び、 **[OK]** を選択します。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="指定するプロジェクトの詳細を示すスクリーンショット。":::


1. **[Db2 への接続]** で、Db2 接続の詳細の値を入力します。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Db2 インスタンスに接続するためのオプションを示すスクリーンショット。":::


1. 移行する Db2 スキーマを右クリックし、 **[レポートの作成]** を選択します。 これにより、HTML レポートが生成されます。 また、スキーマを選択した後、ナビゲーション バーから **[レポートの作成]** を選択することもできます。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="レポートの作成方法を示すスクリーンショット。":::

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Db2 オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 このレポートの既定の場所は、*SSMAProjects* 内のレポート フォルダーです。

   (例: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`)。 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="エラーまたは警告を特定するために確認するレポートのスクリーンショット。":::


### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 これを行うには、次のステップに従います。 

1. メニューから **[ツール]** を選択します。 
1. **[プロジェクトの設定]** を選択します。 
1. **[Type mappings]\(型のマッピング\)** タブを選択します。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="スキーマと型のマッピングの選択を示すスクリーンショット。":::

1. **Db2 メタデータ エクスプローラー** でテーブルを選択し、各テーブルの型マッピングを変更します。 

### <a name="convert-schema"></a>スキーマの変換 

スキーマを変換するには、次の手順を行います。

1. (省略可能) 動的またはアドホック クエリをステートメントに追加します。 ノードを右クリックし、 **[Add statements]\(ステートメントの追加\)** を選択します。 
1. **[SQL Server への接続]** を選択します。 
    1. 接続の詳細を入力して、Azure VM 上の SQL Server インスタンスに接続します。 
    1. ターゲット サーバー上の既存のデータベースに接続するか、新しい名前を指定してターゲット サーバー上に新しいデータベースを作成するかを選択します。 
    1. 認証の詳細を指定します。 
    1. **[接続]** を選択します。

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Azure VM 上の SQL Server への接続に必要な詳細を示すスクリーンショット。":::

1. スキーマを右クリックして、 **[スキーマの変換]** を選択します。 または、スキーマを選択した後、上部のナビゲーション バーから **[スキーマの変換]** を選択することもできます。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="スキーマの選択と変換を示すスクリーンショット。":::

1. 変換が完了したら、スキーマの構造を比較、確認し、潜在的な問題を特定します。 推奨事項に基づいて問題に対処します。 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="スキーマの構造を比較、確認し、潜在的な問題を特定する方法を示すスクリーンショット。":::

1. **[出力]** ペインで、 **[結果の確認]** を選択します。 **[エラー一覧]** ペイン、エラーを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューから **[プロジェクトの保存]** を選択します。 これにより、スキーマを Azure VM 上の SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。

## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。

スキーマを発行し、データを移行するには、次の手順を行います。

1. スキーマを発行します。 **SQL Server メタデータ エクスプローラー** の **[データベース]** ノードで、データベースを右クリックします。 次に、 **[データベースと同期する]** を選択します。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="データベースと同期するオプションを示すスクリーンショット。":::

1. データを移行します。 **Db2 メタデータ エクスプローラー** で、移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、ナビゲーション バーから **[データの移行]** を選択することもできます。 データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、 **[テーブル]** を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="スキーマの選択とデータ移行の選択を示すスクリーンショット。":::

1. Db2 と SQL Server インスタンスの両方の接続の詳細を指定します。 
1. 移行が終了した後、 **[データ移行レポート]** を表示します。  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="データ移行レポートを確認する場所を示すスクリーンショット。":::

1.  [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して Azure VM 上の SQL Server のインスタンスに接続します。 データとスキーマを確認することによって移行を検証します。

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="SQL Server Management Studio のスキーマの比較を示すスクリーンショット。":::

## <a name="post-migration"></a>移行後 

移行が完了したら、移行後の一連のタスクを実行し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する 

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

テストは、次のアクティビティで構成されます。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。
1. **テスト環境を設定する**: テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行した後、結果を分析および比較します。

## <a name="migration-assets"></a>移行資産 

詳細については、次のリソースを参照してください。これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

|Asset  |説明  |
|---------|---------|
|[データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。|
|[Db2 zOS データ資産の検出および評価パッケージ](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|データベース上で SQL スクリプトを実行した後、結果をファイル システム上のファイルにエクスポートできます。 スプレッドシートなど、外部ツールで結果をキャプチャできるように、*.csv などの複数のファイル形式がサポートされています。 この方法を使用すると、ワークベンチをインストールしていないチームと結果を簡単に共有することができます。|
|[IBM Db2 LUW インベントリ スクリプトと成果物](https://github.com/microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|この資産には、IBM Db2 LUW バージョン 11.1 システム テーブルを照会する SQL クエリが含まれます。このクエリでは、スキーマおよびオブジェクトの種類ごとのオブジェクトの数、各スキーマの "生データ" の概算値、および各スキーマのテーブルのサイズを取得し、結果を CSV 形式で格納します。|
|[Azure 上の Db2 LUW pureScale - 設定ガイド](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|このガイドは、Db2 の実装計画の開始点として役立ちます。 業務要件は違っても、同じ基本パターンが適用されます。 このアーキテクチャ パターンは、Azure 上の OLAP アプリケーションにも使用できます。|

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次の手順

移行後は、[移行後の検証と最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)を確認してください。 

さまざまなデータベースおよびデータ移行シナリオを支援するために使用できる Microsoft とサードパーティのサービスとツールについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

ビデオ コンテンツについては、[移行行程の概要](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)に関するページを参照してください。
