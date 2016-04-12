<properties
	pageTitle="[Stretch Database を有効にする] ウィザードを実行する方法の概要 | Microsoft Azure"
	description="[Stretch Database を有効にする] ウィザードを実行し、Stretch Database のデータベースを設定する方法について説明します。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# [Stretch Database を有効にする] ウィザードを実行する方法の概要

Stretch Database のデータベースを設定するには、[Stretch Database を有効にする] ウィザードを実行します。このトピックでは、ウィザードで入力し、選択する情報について説明します。

Stretch Database の詳細については、「[Stretch Database](sql-server-stretch-database-overview.md)」を参照してください。

## ウィザードを起動する

1.  SQL Server Management Studio のオブジェクト エクスプローラーで、Stretch を有効にするデータベースを選択します。

2.  右クリックして **[タスク]** を選択し、**[Stretch]** を選択し、**[有効化]** を選択してウィザードを起動します。

## <a name="Intro"></a>はじめに
ウィザードの目的と前提条件を確認します。

![Stretch Database ウィザードの概要ページ][StretchWizardImage1]

## <a name="Tables"></a>テーブルを選択する
Stretch を有効にするテーブルを選択します。

![Stretch データベース ウィザードの [テーブルの選択] ページ][StretchWizardImage2]

|分割|説明|
|----------|---------------|
|(タイトルなし)|選択したテーブルの Stretch を有効にするにはこの列のチェック ボックスを選択します。|
|**名前**|テーブルの列の名前を指定します。|
|(タイトルなし)|この列の記号は、一般的に、ブロック問題に起因し、選択したテーブルの Stretch を有効にできないことを示します。非対応のデータ型をテーブルで使用していることが原因の可能性があります。記号の上にマウス カーソルを合わせると、ヒント形式で詳細が表示されます。詳細については、「[Stretch Database のセキュリティ制約とブロック問題](sql-server-stretch-database-limitations.md)」を参照してください。|
|**ストレッチ済み**|テーブルが既に有効になっているかどうかを示します。|
|**行**|テーブルの行数を指定します。|
|**サイズ (KB)**|テーブルのサイズを KB 単位で指定します。|
|**移行**|CTP 3.1 ～ RC2 では、ウィザードを使用してテーブル全体を移行することだけが可能です。行を選択する述語を指定し、履歴データと現行データの両方を含むテーブルから移行する場合、ALTER TABLE ステートメントを実行し、ウィザードの終了後に述語を指定します。詳細については、「[データベースの Stretch Database を有効にする](sql-server-stretch-database-enable-table.md)」または「[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)」を参照してください。|

## <a name="Configure"></a>Azure デプロイを構成する

1.  Microsoft アカウントで Microsoft Azure にサインインします。

    ![Azure へのサインイン - Stretch Database ウィザード][StretchWizardImage3]

2.  Stretch Database に使用する Azure サブスクリプションを選択します。

3.  Azure リージョンを選択します。新しいサーバーを作成すると、このリージョンにサーバーが作成されます。

    待機時間を最小限に抑えるために、SQL Server が配置されている Azure リージョンを選択してください。リージョンの詳細については、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。

4.  既存のサーバーを使用するか、新しい Azure サーバーを作成するように指定します。

    SQL Server の Active Directory が Azure Active Directory と連合している場合、SQL Server のフェデレーション サービス アカウントを利用し、リモート Azure サーバーと通信することもできます。このオプションの要件に関する詳細については、「[ALTER DATABASE SET オプション (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)」を参照してください。

	-   **新しいサーバーを作成する**

        1.  サーバー管理者のログイン名とパスワードを作成します。

        2.  SQL Server のフェデレーション サービス アカウントを利用し、リモート Azure サーバーと通信することもできます。

		![新しい Azure サーバーの作成 - Stretch Database ウィザード][StretchWizardImage4]

    -   **既存サーバー**

        1.  既存の Azure サーバーの名前を選択するか、入力します。

        2.  認証方法を選択します。

            -   **[SQL Server 認証]** を選択した場合、新しいログインとパスワードを作成します。

            -   SQL Server のフェデレーション サービス アカウントを利用し、リモート Azure サーバーと通信するには、**[Active Directory 統合認証]** を選択します。

		![既存の Azure サーバーの選択 - Stretch Database ウィザード][StretchWizardImage5]

## <a name="Credentials"></a>資格情報を守る
強いパスワードを入力し、データベース マスター キーを作成します。あるいは、データベース マスター キーが既に存在する場合、そのパスワードを入力します。

Stretch Database がリモート データベースの接続に使用する資格情報が守るためには、データベース マスター キーを用意する必要があります。

![Stretch Database ウィザードのセキュリティで保護された資格情報ページ][StretchWizardImage6]

データベース マスター キーに関する詳細については、「[CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx)」と「[データベース マスター キーの作成](https://msdn.microsoft.com/library/aa337551.aspx)」を参照してください。ウィザードにより作成される資格情報の詳細については、「[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx)」を参照してください。

## <a name="Network"></a>IP アドレスを選択する
SQL Server のパブリック IP アドレスを使用するか、IP アドレスの範囲を入力し、SQL Server とリモート Azure サーバーの通信を可能にするファイアウォール ルールを Azure で作成します。

このページで指定した IP アドレスにより、SQL Server からの受信データ、クエリ、および管理操作が Azure ファイアウォールを通過することを許可するよう Azure サーバーに指示します。このウィザードによって、SQL Server のファイアウォール設定が変更されることはありません。

![Stretch Database ウィザードの IP アドレスの選択ページ][StretchWizardImage7]

## <a name="Summary"></a>概要
入力した値とウィザードで選択したオプションを確認します。**[完了]** を選択して、Stretch を有効にします。

![Stretch Database ウィザードのまとめのページ][StretchWizardImage8]

## <a name="Results"></a>結果
結果を確認します。

必要に応じて **[監視]** を選択して、Stretch Database Monitor のデータ移行の状態の監視を開始します。詳細については、「[データ移行の監視とトラブルシューティング (Stretch Database)](sql-server-stretch-database-monitor.md)」を参照してください。

## <a name="KnownIssues"></a>ウィザードのトラブルシューティング
**Stretch Database ウィザードが失敗しました。** Stretch Database がサーバー レベルで有効になっていないとき、システム管理者の許可なしでウィザードを実行し、有効にしようとすると、ウィザードは失敗します。ローカル サーバー インスタンスで Stretch Database を有効にするようにシステム管理者に依頼し、その後、ウィザードをもう一度実行します。詳細については、「[前提条件: サーバーで Stretch Database を有効にするためのアクセス許可](sql-server-stretch-database-enable-database.md#EnableTSQLServer)」を参照してください。

## 次のステップ
Stretch Database の追加テーブルを有効にします。データ移行を監視し、Stretch 対応のデータベースとテーブルを管理します。

-   [テーブルの Stretch Database を有効にして](sql-server-stretch-database-enable-table.md)追加テーブルを有効にします。

-   [Stretch Database を監視して](sql-server-stretch-database-monitor.md)データ移行の状態を確認します。

-   [Stretch Database を一時停止し、再開します。](sql-server-stretch-database-pause.md)

-   [Stretch Database を管理し、問題を解決します。](sql-server-stretch-database-manage.md)

-   [Stretch 対応データベースをバックアップし、復元します。](sql-server-stretch-database-backup.md)

## 関連項目

[データベースの Stretch Database を有効にする](sql-server-stretch-database-enable-database.md)

[テーブルの Stretch Database を有効にする](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png

<!---HONumber=AcomDC_0406_2016-->