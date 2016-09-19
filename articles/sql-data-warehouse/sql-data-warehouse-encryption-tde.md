<properties
   pageTitle="SQL Data Warehouse での Transparent Data Encryption (TDE) の概要| Microsoft Azure"
   description="SQL Data Warehouse での Transparent Data Encryption (TDE) の概要"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/29/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# SQL Data Warehouse での Transparent Data Encryption (TDE) の概要

> [AZURE.SELECTOR]
- [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
- [脅威の検出](sql-data-warehouse-security-threat-detection.md)
- [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
- [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [監査の概要](sql-data-warehouse-auditing-overview.md)
- [ダウンレベル クライアントの監査](sql-data-warehouse-auditing-downlevel-clients.md)


Azure SQL Data Warehouse の Transparent Data Encryption (TDE) を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティの脅威からデータを保護できます。アプリケーションを変更する必要はありません。

TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。SQL Database では、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。組み込みのサーバー証明書は、SQL Database サーバーごとに一意です。Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。SQL Data Warehouse で使用される暗号化アルゴリズムは AES-256 です。TDE の一般的な説明については、「[透過的なデータ暗号化 (TDE)]」を参照してください。

##暗号化の有効化

SQL Data Warehouse の TDE を有効にするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)でデータベースを開きます
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[Transparent Data Encryption]** オプションを選択します ![][1]
4. **[ON]** 設定を選択します ![][2]
5. **[保存]** を選択します ![][3]

##暗号化の無効化

SQL Data Warehouse の TDE を無効にするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)でデータベースを開きます
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[Transparent Data Encryption]** オプションを選択します ![][1]
4. **[OFF]** 設定を選択します ![][4]
5. **[保存]** を選択します ![][5]

##暗号化の DMV

暗号化は次の DMV を使用して確認することができます。

- [sys.databases]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys]

<!--MSDN references-->
[透過的なデータ暗号化 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0907_2016-->