---
title: Transparent Data Encryption (ポータル)
description: SQL Data Warehouse での Transparent Data Encryption (TDE)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 4435c5f246d6dd20fd0b2c560634f6980d6aa2ff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685769"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>SQL Data Warehouse での Transparent Data Encryption (TDE) の概要
> [!div class="op_single_selector"]
> * [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
> * [認証](sql-data-warehouse-authentication.md)
> * [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
> * [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>必要なアクセス許可
Transparent Data Encryption (TDE) を有効にするには、管理者か dbmanager ロールのメンバーである必要があります。

## <a name="enabling-encryption"></a>暗号化の有効化
SQL Data Warehouse の TDE を有効にするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[透過的なデータ暗号化]** オプションを選択します ![][1]
4. **[ON]** 設定を選択します ![][2]
5. **[保存]** を選択します
   ![][3]  

## <a name="disabling-encryption"></a>暗号化の無効化
SQL Data Warehouse の TDE を無効にするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[透過的なデータ暗号化]** オプションを選択します ![][1]
4. **[OFF]** 設定を選択します ![][4]
5. **[保存]** を選択します
   ![][5]  

## <a name="encryption-dmvs"></a>暗号化の DMV
暗号化は次の DMV を使用して確認することができます。

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
