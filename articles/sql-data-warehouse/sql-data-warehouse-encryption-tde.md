<properties 
   pageTitle="SQL Data Warehouse での Transparent Data Encryption (TDE) の概要| Microsoft Azure" 
   description="SQL Data Warehouse での Transparent Data Encryption (TDE) の概要" 
   services="sql-data-warehouse" 
   documentationCenter="" 
   authors="twounder" 
   manager="barbkess" 
   editor=""/>

<tags 
   ms.service="sql-data-warehouse" 
   ms.workload="data-management" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="01/04/2016" 
   ms.author="twounder"/>
 
# SQL Data Warehouse での Transparent Data Encryption (TDE) の概要
> [AZURE.SELECTOR]
- [Azure Classic Portal](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

Azure SQL Data Warehouse の Transparent Data Encryption (TDE) を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティの脅威からデータを保護できます。アプリケーションを変更する必要はありません。

TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。SQL Database では、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。組み込みのサーバー証明書は、SQL Database サーバーごとに一意です。Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。TDE の一般的な説明については、「[透過的なデータ暗号化 (TDE)]」を参照してください。

##暗号化の有効化

SQL Data Warehouse の TDE を有効にするには、次の手順を実行します。

1. [Azure クラシック ポータル](https://portal.azure.com)でデータベースを開きます
2. データベース ブレードで **[設定]** ボタンをクリックします。	
3. **[Transparent Data Encryption]** オプションを選択します ![][1] 
4. **[ON]** 設定を選択します ![][2] 
5. **[保存]** を選択します ![][3]  

##暗号化の無効化

SQL Data Warehouse の TDE を無効にするには、次の手順を実行します。

1. [Azure クラシック ポータル](https://portal.azure.com)でデータベースを開きます
2. データベース ブレードで **[設定]** ボタンをクリックします。	
3. **[Transparent Data Encryption]** オプションを選択します ![][1] 
4. **[OFF]** 設定を選択します ![][4] 
5. **[保存]** を選択します ![][5]  




<!--Anchors-->
[透過的なデータ暗号化 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0107_2016-->