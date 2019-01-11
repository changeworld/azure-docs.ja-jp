---
title: Azure SQL Database Managed Instance の監査 | Microsoft Docs
description: T-SQL を使って Azure SQL Database Managed Instance の監査を始める方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: b295f7a2a454e3987e8639814f785b7457dd452b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973096"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Azure SQL Database Managed Instance の監査の概要

[Azure SQL Database Managed Instance](sql-database-managed-instance.md) の監査は、データベース イベントを追跡し、Azure ストレージ アカウントの監査ログにイベントを書き込みます。 また、監査によって以下を行うことができます。
- 規定コンプライアンスの維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。
- コンプライアンスを保証するものではありませんが、標準へのコンプライアンスを強化します。 標準準拠をサポートする Azure プログラムの詳細については、 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/compliance/)のページを参照してください。


## <a name="set-up-auditing-for-your-server"></a>サーバーの監査を設定する

以下のセクションでは、マネージド インスタンスの監査の構成について説明します。
1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 次の手順では、監査ログが格納される Azure Storage **コンテナー**を作成します。

   - 監査ログを格納する Azure Storage に移動します。

     > [!IMPORTANT]
     > リージョンをまたいで読み取り/書き込みが行われないように、マネージド インスタンス サーバーと同じリージョンのストレージ アカウントを使います。

   - ストレージ アカウントで **[概要]** に移動し、**[BLOB]** をクリックします。

     ![ナビゲーション ウィンドウ][1]

   - 上部のメニューで、**[+ コンテナー]** をクリックして新しいコンテナーを作成します。

     ![ナビゲーション ウィンドウ][2]

   - コンテナーの **[名前]** を指定し、パブリック アクセス レベルを **[プライベート]** に設定して、**[OK]** をクリックします。

     ![ナビゲーション ウィンドウ][3]

   - コンテナーの一覧で新しく作成されたコンテナーをクリックし、**[コンテナーのプロパティ]** をクリックします。

     ![ナビゲーション ウィンドウ][4]

   - コピー アイコンをクリックしてコンテナーの URL をコピーし、後で使えるように (メモ帳などに) URL を保存します。 コンテナー URL は、`https://<StorageName>.blob.core.windows.net/<ContainerName>` という形式になっている必要があります。

     ![ナビゲーション ウィンドウ][5]

3. 次の手順では、マネージド インスタンスの監査アクセス権をストレージ アカウントに付与するために使われる Azure Storage の **SAS トークン**を生成します。

   - 前の手順でコンテナーを作成した Azure ストレージ アカウントに移動します。

   - [ストレージの設定] メニューの **[Shared Access Signature]** をクリックします。

     ![ナビゲーション ウィンドウ][6]

   - 次に示すように SAS を構成します。
     - **使用できるサービス**:BLOB
     - **開始日**: タイム ゾーンに関連する問題を回避するため、前日の日付を使うことをお勧めします。
     - **終了日**: この SAS トークンの有効期限が切れる日付を選びます。 

       > [!NOTE]
       > 監査の失敗を避けるため、トークンの期限が切れたら更新します。

     - **[SAS の生成]** をクリックします。

       ![ナビゲーション ウィンドウ][7]

   - [SAS の生成] をクリックすると、SAS トークンが下部に表示されます。 コピー アイコンをクリックしてトークンをコピーし、後で使えるように (メモ帳などに) 保存します。

     > [!IMPORTANT]
     > トークンの先頭にある疑問符 ("?") を削除します。

     ![ナビゲーション ウィンドウ][8]

4. SQL Server Management Studio (SSMS) を使ってマネージド インスタンスに接続します。

5. 次の T-SQL ステートメントを実行し、前の手順で作成したコンテナー URL と SAS トークンを使って、**新しい資格情報を作成**します。

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. 次の T-SQL ステートメントを実行し、新しいサーバー監査を作成します (独自の監査名を選び、前の手順で作成したコンテナー URL を使います)。

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    `RETENTION_DAYS` を指定しないと、既定値の 0 (無制限のリテンション期間) になります。

    以下の追加情報をご覧ください。
    - [マネージド インスタンス、Azure SQL データベース、SQL Server での監査の相違点](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
    - [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. SQL Server の場合と同様に、サーバー監査仕様またはデータベース監査仕様を作成します。
    - [サーバー監査仕様の作成 T-SQL ガイド](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [データベース監査仕様の作成 T-SQL ガイド](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. 手順 6 で作成したサーバー監査を有効にします。

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>監査ログを分析する
BLOB 監査ログを表示するには、いくつかの方法が使用できます。

- システム関数 `sys.fn_get_audit_file` (T-SQL) を使って、表形式で監査ログ データを返します。 この関数の使用方法の詳細については、[sys.fn_get_audit_file のドキュメント](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)を参照してください。

- 監査ログの使い方の完全な一覧については、「[SQL Database 監査の使用](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing)」をご覧ください。

> [!IMPORTANT]
> 現在、Azure Portal ([監査レコード] ウィンドウ) から監査レコードを表示する方法は、マネージド インスタンスについては利用できません。

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>マネージド インスタンス、Azure SQL Database、SQL Server での監査の相違点

マネージド インスタンス、Azure SQL Database、オンプレミスの SQL Server の SQL 監査の主な相違点は次のとおりです。

- マネージド インスタンスでは、SQL 監査はサーバー レベルで機能し、Azure BLOB ストレージ アカウントに `.xel` ログ ファイルが保存されます。
- Azure SQL Database では、SQL 監査はデータベース レベルで機能します。
- オンプレミス/仮想マシンの SQL Server では、SQL 監査はサーバー レベルで機能しますが、イベントはファイル システム/Windows イベント ログに保存されます。

マネージド インスタンスの XEvent 監査では、対象として Azure Blob Storage をサポートしています。 ファイル ログと Windows ログは**サポートされていません**。

Azure Blob Storage の監査の `CREATE AUDIT` 構文の主な相違点は次のとおりです。
- 新しい `TO URL` 構文が用意されています。この構文を使って、`.xel` ファイルを配置する Azure Blob Storage コンテナーの URL を指定できます。
- マネージド インスタンスは Windows ファイル共有にアクセスできないため、`TO FILE` 構文は**サポートされていません**。
- Shutdown オプションは**サポートされていません**。
- `queue_delay` の値として 0 は**サポートされていません**。


## <a name="next-steps"></a>次の手順

- 監査ログの使い方の完全な一覧については、「[SQL Database 監査の使用](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)」をご覧ください。
- 標準準拠をサポートする Azure プログラムの詳細については、 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/compliance/)のページを参照してください。


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
