<properties
   pageTitle="Azure での SQL Server Stretch Database に対する Transparent Data Encryption (TDE) の有効化 | Microsoft Azure"
   description="Azure での SQL Server Stretch Database に対する Transparent Data Encryption (TDE) の有効化"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# Azure での Stretch Database に対する Transparent Data Encryption (TDE) の有効化
> [AZURE.SELECTOR]
- [Azure ポータル](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Transparent Data Encryption (TDE) を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティの脅威からデータを保護できます。アプリケーションを変更する必要はありません。

TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。組み込みのサーバー証明書は、Azure サーバーごとに一意です。Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。TDE の一般的な説明については、「[透過的なデータ暗号化 (TDE)]」を参照してください。

##暗号化の有効化

Stretch 対応 SQL Server データベースから移行したデータを格納している Azure データベースの TDE を有効にするには、次の操作を行います。

1. [Azure ポータル](https://portal.azure.com)でデータベースを開きます
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[Transparent Data Encryption]** オプションを選択します ![][1]
4. **[オン]**、**[保存]** の順に選択します ![][2]


##暗号化の無効化

Stretch 対応 SQL Server データベースから移行したデータを格納している Azure データベースの TDE を無効にするには、次の操作を行います。

1. [Azure ポータル](https://portal.azure.com)でデータベースを開きます
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[透過的なデータ暗号化]** オプションを選択します
4. **[オフ]**、**[保存]** の順に選択します




<!--Anchors-->
[透過的なデータ暗号化 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->

<!---HONumber=AcomDC_0810_2016-->