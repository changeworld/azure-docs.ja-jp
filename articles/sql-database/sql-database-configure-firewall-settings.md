---
title: "SQL Database のサーバー レベルのファイアウォール規則の構成 | Microsoft Docs"
description: "Azure SQL サーバーにアクセスする IP アドレス用のファイアウォールの構成方法を説明します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: ad72adf626fb5f59f239f57ff0158db101017a1b
ms.openlocfilehash: 7925663bd4233e368f54b38c94888ac1b0f5dcab


---
# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する
> [!div class="op_single_selector"]
> * [概要](sql-database-firewall-configure.md)
> * [Azure ポータル](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Azure SQL Server では、サーバーとデータベースの接続許可に、ファイアウォール規則を使用します。 データベースへのアクセスを選択的に許可するには、Azure SQL Server 論理サーバーのマスター データベースまたはユーザー データベースに、サーバーレベルおよびデータベースレベルのファイアウォール設定を定義します。 このトピックでは、サーバーレベルのファイアウォール規則について説明します。

> [!IMPORTANT]
> Azure のアプリケーションから Azure SQL Server に接続を許可するには、Azure の接続を有効にする必要があります。 ファイアウォール規則の働きを理解するには、[Azure SQL サーバーのファイアウォールの構成方法\-の概要](sql-database-firewall-configure.md)に関するページを参照してください。 Azure クラウド境界内で接続を行う場合、追加の TCP ポートを開くことが必要な場合があります。 詳細については、「 **ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート** 」の「 [SQL Database V12: 外部と内部](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

**推奨:** アクセス要件が同じデータベースが多数存在し、それぞれのデータベースの設定に時間を費やしたくない場合は、管理者向けのサーバーレベルのファイアウォール規則を使用してください。 ただし、セキュリティを強化しデータベースの移植性を高めるため、可能な限りデータベースレベルのファイアウォール規則を使用することをお勧めします。

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure ポータルで既存のサーバー レベルのファイアウォール規則を管理する
サーバーレベルのファイアウォール規則を管理する手順を繰り返します。

* 現在のコンピューターを追加するには、[クライアント IP の追加] をクリックします。
* さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。
* 既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。
* 既存の規則を削除するには、行の最後に X が表示されるまで、規則上にポインターを置きます。 [X] をクリックして、規則を削除します。

**[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ
Transact-SQL を使用して、サーバー レベルとデータベース レベルのファイアウォール規則を作成する方法については、「 [T-SQL を使用して Azure SQL Database のサーバー レベルとデータベース レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-tsql.md)」をご覧ください。 

他の方法でサーバー レベルのファイアウォール規則を作成する方法については、次の記事をご覧ください。 

* [PowerShell を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-powershell.md)
* [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md)

データベース作成のチュートリアルについては、「 [SQL Database チュートリアル: Azure ポータルを使用して数分で SQL データベースを作成する](sql-database-get-started.md)」をご覧ください。   

データベースに接続可能な追加ユーザーの作成方法については、「[SQL Database 認証および承認： アクセス権を付与する](https://msdn.microsoft.com/library/azure/ee336235.aspx)」を参照してください。

## <a name="additional-resources"></a>その他のリソース
* [データベースの保護](sql-database-security.md)   
* [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Nov16_HO5-->


