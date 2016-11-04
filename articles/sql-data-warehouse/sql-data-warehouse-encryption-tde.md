---
title: SQL Data Warehouse での Transparent Data Encryption (ポータル) | Microsoft Docs
description: SQL Data Warehouse での Transparent Data Encryption (TDE)
services: sql-data-warehouse
documentationcenter: ''
author: ronortloff
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: rortloff;barbkess;sonyama

---
# SQL Data Warehouse での Transparent Data Encryption (TDE) の概要
> [!div class="op_single_selector"]
> * [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
> * [認証](sql-data-warehouse-authentication.md)
> * [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
> * [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## 必要なアクセス許可
Transparent Data Encryption (TDE) を有効にするには、管理者か dbmanager ロールのメンバーである必要があります。

## 暗号化の有効化
SQL Data Warehouse の TDE を有効にするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)でデータベースを開きます
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[Transparent Data Encryption]** オプションを選択します ![][1]
4. **[ON]** 設定を選択します ![][2]
5. **[保存]** を選択します ![][3]

## 暗号化の無効化
SQL Data Warehouse の TDE を無効にするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)でデータベースを開きます
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[Transparent Data Encryption]** オプションを選択します ![][1]
4. **[OFF]** 設定を選択します ![][4]
5. **[保存]** を選択します ![][5]

## 暗号化の DMV
暗号化は次の DMV を使用して確認することができます。

* [sys.databases]
* [sys.dm\_pdw\_nodes\_database\_encryption\_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->