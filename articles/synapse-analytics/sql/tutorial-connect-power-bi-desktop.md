---
title: チュートリアル:SQL オンデマンド (プレビュー) を Power BI Desktop に接続してレポートを作成する
description: このチュートリアルでは、Azure Synapse Analytics の SQL オンデマンド (プレビュー) を Power BI Desktop に接続し、ビューに基づいてデモ レポートを作成する方法について説明します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0df8ac495b6aca81e46dffc248019483b1c82202
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425171"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>チュートリアル:SQL オンデマンド (プレビュー) を Power BI Desktop に接続してレポートを作成する

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - デモ データベースの作成
> - レポートに使用するビューの作成
> - Power BI Desktop への接続
> - ビューに基づくレポートの作成

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のソフトウェアが必要です。

- [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) や [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) などの SQL クエリ ツール。
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/)。

次のパラメーターの値を指定します。

| パラメーター                                 | 説明                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL オンデマンド サービス エンドポイント アドレス    | サーバー名として使用されます                                   |
| SQL オンデマンド サービス エンドポイント リージョン     | サンプルで使用されるストレージを決定するために使用されます |
| エンドポイント アクセスのユーザー名とパスワード | エンドポイントへのアクセスに使用されます                               |
| ビューの作成に使用するデータベース     | サンプルの開始点として使用されるデータベース       |

## <a name="1---create-database"></a>1 - データベースの作成

デモ環境用に独自のデモ データベースを作成します。 このデータベースは、実際のデータを格納するのではなくメタデータを表示するために使用します。

次の Transact-SQL (T-SQL) スクリプトを実行して、デモ データベースを作成します (必要に応じて既存のデータベースを削除します)。

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - 資格情報の作成

SQL オンデマンド サービスがストレージ内のファイルにアクセスには資格情報が必要です。 エンドポイントと同じリージョンにあるストレージ アカウントの資格情報を作成します。 SQL オンデマンドは異なるリージョンからストレージ アカウントにアクセスできますが、ストレージとエンドポイントを同じリージョンに配置するとパフォーマンスが向上します。

次の Transact-SQL (T-SQL) スクリプトを実行して、資格情報を作成します。

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3 - ビューの準備

次の Transact-SQL (T-SQL) スクリプトを実行して、Power BI で使用する外部デモ データに基づいてビューを作成します。

次のクエリを使用して、データベース `Demo` 内にビュー `usPopulationView` を作成します。

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

デモ データには次のデータセットが含まれています。

2000 年と 2010 年に実施された 10 年ごとの国勢調査から Parquet 形式で取り込んだ米国の各郡の性別および人種別の米国人口。

| フォルダー パス                                                  | 説明                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | デモ ストレージ アカウント内のデータの親フォルダー               |
| /release/us_population_county/                               | Parquet 形式で示した米国人口のデータ ファイル。Hive/Hadoop パーティション構成を使用して年度別に分割されています。 |

## <a name="4---create-power-bi-report"></a>4 - Power BI レポートの作成

次の手順に従って、Power BI Desktop のレポートを作成します。

1. Power BI Desktop アプリケーションを開き、 **[データの取得]** を選択します。

   ![Power BI Desktop アプリケーションを開き、[データの取得] を選択する。](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. **[Azure]**  >  **[Azure SQL Database]** を選択します。 

   ![データ ソースを選択する。](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. データベースが配置されているサーバーの名前を **[サーバー]** フィールドに入力し、データベース名に「`Demo`」と入力します。 **[インポート]** オプションを選択し、 **[OK]** を選択します。 

   ![エンドポイントでデータベースを選択する。](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. 推奨される認証方法を選択します。

    - AAD の例 
  
    ![[サインイン] をクリックする。](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - SQL ログインの例 - ユーザー名とパスワードを入力します。

    ![SQL ログインを使用する。](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. ビュー `usPopulationView` を選択し、 **[読み込み]** を選択します。 

   ![選択されているデータベースのビューを選択する。](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. 操作が完了するまで待機すると、`There are pending changes in your queries that haven't been applied` と記載されたポップアップが表示されます。 **[変更の適用]** を選択します。 

   ![[変更の適用] をクリックする。](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. **[クエリの変更の適用]** ダイアログ ボックスが消えるまで待ちます。これには数分かかる場合があります。 

   ![クエリが終了するまで待機する。](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. 読み込みが完了したら、次の列をこの順序で選択してレポートを作成します。
   - countyName
   - 作成 (population)
   - stateName

   ![目的の列を選択してマップ レポートを生成する。](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このレポートの使用が終了したら、次の手順でリソースを削除します。

1. ストレージ アカウントの資格情報を削除します。

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. ビューを削除します。

   ```sql
   DROP VIEW usPopulationView;
   ```

3. データベースを削除します。

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>次のステップ

Synapse SQL を使用してストレージ ファイルにクエリを実行する方法については、[ストレージ ファイルに対するクエリの実行](develop-storage-files-overview.md)に関する記事に進んでください。
