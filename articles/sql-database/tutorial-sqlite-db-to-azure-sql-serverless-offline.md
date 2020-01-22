---
title: チュートリアル:SQLite データベースを Azure SQL Database サーバーレスに移行する
description: Azure Data Factory を使用して、SQLite から Azure SQL Database サーバーレスへのオフライン移行を実行する方法について説明します。
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780755"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>SQLite データベースを Azure SQL Database サーバーレスに移行する
SQLite は、多くのユーザーにとって初めて触れるデータベースと SQL プログラミングです。 多くのオペレーティング システムに組み込まれており、一般的なアプリケーションにおいて、SQLite は、世界中で最も広く導入され使用されているデータベース エンジンとなっています。 また、多くの開発者にとって初めてのデータベース エンジンである可能性が高いため、多くの場合、プロジェクトまたはアプリケーションの中心部となることがあります。 プロジェクトまたはアプリケーション規模が初期の SQLite 実装を超えた場合、開発者はデータを信頼性の高い一元化されたデータ ストアに移行することが必要になる場合があります。

Azure SQL Database サーバーレスは、ワークロードの需要に基づいてコンピューティングが自動的にスケーリングされ、1 秒あたりのコンピューティング使用量に対して請求されるコンピューティング レベルです。 またサーバーレス コンピューティング レベルでは、アイドル期間にデータベースを自動的に一時停止します。このときはストレージのみに課金され、再びアクティブになると自動的にデータベースが再開されます。

以下の手順を実行すると、データベースは Azure SQL Database サーバーレスに移行されます。これにより、データベースをクラウド内の他のユーザーまたはアプリケーションで使用できるようになり、アプリケーション コードの変更を最小限に抑え、使用した分だけが課金されるようになります。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション
- 移行する SQLite2 または SQLite3 データベース
- Windows 環境
  - ローカル Windows 環境がない場合は、移行に Azure の Windows VM を使用できます。 Azure Files と Storage Explorer を使用して、SQLite データベース ファイルを移動して VM で使用できるようにします。

## <a name="steps"></a>手順

1. サーバーレス コンピューティング層で新しい Azure SQL Database をプロビジョニングします。

    ![Azure SQL database サーバーレスのプロビジョニング例を示す Azure portal のスクリーンショット](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Windows 環境で使用できる SQLite データベース ファイルがあることを確認します。 SQLite ODBC ドライバーをまだインストールしていない場合はインストールします (たとえば、 http://www.ch-werner.de/sqliteodbc/) のように、オープンソースに多数のファイルが用意されています。

3. データベースのシステム DSN を作成します。 システム アーキテクチャ (32 ビットと 64 ビット) に一致するデータ ソース管理者アプリケーションを使用していることを確認します。 システム設定で実行されているバージョンは確認することができます。

    - 使用している環境で ODBC データ ソース アドミニストレーターを開きます。
    - [システム DSN] タブをクリックし、[追加] をクリックします
    - インストールした SQLite ODBC コネクタを選択し、わかりやすい接続名を付けます (たとえば、sqlitemigrationsource)
    - データベース名を .db ファイルに設定します
    - 保存して終了

4. セルフホステッド統合ランタイムのダウンロードとインストール。 これを行う最も簡単な方法は、ドキュメントで詳しく説明されているように、簡易インストール オプションを選択することです。 手動インストールを選択した場合は、アプリケーションに認証キーを提供する必要があります。認証キーは、次の方法で Data Factory インスタンスで検索します。

    - ADF を起動する (Azure portal でサービスから作成および監視する)
    - 左側の [作成] タブ (青い鉛筆) をクリックします
    - [接続] (左下)、[統合ランタイム] の順にクリックします
    - 新しいセルフホステッド統合ランタイムを追加し、名前を付けてから *[オプション 2]* を選択します。

5. Data Factory にソース SQLite データベース用の新しいリンクされたサービスを作成します。

    ![Azure Data Factory の空の [リンクされたサービス] ブレードを示すスクリーンショット](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. [接続] の [リンクされたサービス] で、[新規] をクリックします

7. "ODBC" コネクタを検索して選択します


    ![Azure Data Factory の [リンクされたサービス] ブレードの ODBC コネクタのロゴを示すスクリーンショット](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. リンクされたサービスにわかりやすい名前 (たとえば、"sqlite_odbc" など) を付けます。 [Connect via integration runtime]\(統合ランタイム経由で接続\) ドロップダウンから使用する統合ランタイムを選択します。 接続文字列に次を入力し、初期カタログ変数を .db ファイルのファイル パスに、DSN をシステム DSN 接続の名前に置き換えます。 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. 認証の種類を匿名に設定する

10. 接続をテストする

    ![正常に接続された Azure Data Factory を示すスクリーンショット](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. サーバーレス SQL ターゲットに対して、別のリンクされたサービスを作成します。 [リンクされたサービス] ウィザードを使用してデータベースを選択し、SQL 認証資格情報を指定します。

    ![Azure Data Factory で選択された Azure SQL Database を示すスクリーンショット](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. SQLite データベースから CREATE TABLE ステートメントを抽出します。 これを行うには、データベース ファイルで次の Python スクリプトを実行します。

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. CreateTables.sql ファイルから CREATE table ステートメントをコピーし、Azure portal のクエリ エディターで SQL ステートメントを実行して、サーバーレス SQL ターゲット環境にランディング テーブルを作成します。

14. Data Factory のホーム画面に戻り、[データ コピー] をクリックしてジョブ作成ウィザードを実行します。

    ![Azure Data Factory の [データ コピー] ウィザード ロゴを示すスクリーンショット](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. チェック ボックスを使用してソース SQLite データベースのすべてのテーブルを選択し、それらを Azure SQL のターゲット テーブルにマップします。 ジョブが実行されると、SQLite から Azure SQL にデータが正常に移行されました。

## <a name="next-steps"></a>次のステップ

- 使用を開始するには、「[クイック スタート: Azure portal を使用して Azure SQL Database で単一データベースを作成する](sql-database-single-database-get-started.md)」をご覧ください。
- リソースの制限については、[サーバーレス コンピューティング レベルのリソース制限](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)に関する記事をご覧ください。
