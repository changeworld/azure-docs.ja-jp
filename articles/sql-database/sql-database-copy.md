---
title: "Azure SQL Database のコピーの作成 | Microsoft Docs"
description: "Azure SQL Database のコピーの作成"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sstein; sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: c4f0b7eab07aa6a23bbb5489130d5cb92e18e5e2


---
# <a name="copy-an-azure-sql-database"></a>Azure SQL Database のコピー
> [!div class="op_single_selector"]
> * [概要](sql-database-copy.md)
> * [Azure ポータル](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Azure の[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)機能を使用して、SQL Database のコピーを作成できます。 ただし、geo レプリケーションとは異なり、シード処理の段階が完了するとレプリケーション リンクが切断されます。 そのため、データベースのコピーとは、コピーが要求された時点でのソース データベースのスナップショットのことです。  
同じサーバーまたは別のサーバーのいずれかで、データベースのコピーを作成できます。 データベース コピーのサービス レベルとパフォーマンス レベル (価格レベル) は、既定でコピー元のデータベースと同じになります。 API を使用している場合は、同じサービス レベル (エディション) 内のさまざまなパフォーマンス レベルを選択できます。 コピーの完了後、コピーは完全に機能する独立したデータベースになります。 この時点では、任意のエディションにアップグレードまたはダウン グレードできます。 ログイン、ユーザー、アクセス許可は非依存で管理できます。  

データベースを同じ論理サーバーにコピーすると、両方のデータベースで同じログインを利用できます。 データベースをコピーするために使用するセキュリティ プリンシパルが、新しいデータベースのデータベース所有者 (DBO) になります。 すべてのデータベース ユーザー、アクセス許可、セキュリティ識別子 (SID) がデータベースのコピーにコピーされます。  

別の論理サーバーにデータベースをコピーする場合は、新しいサーバーのセキュリティ プリンシパルが新しいデータベースのデータベース所有者になります。 データのアクセスに[包含データベース ユーザー](sql-database-manage-logins.md)を使用する場合は、プライマリとセカンダリの両方のデータベースが常に同じユーザー資格情報を持つようにします。そうすることで、コピーの完了後すぐに同じ資格情報でアクセスできるようになります。 [Azure Active Directory](../active-directory/active-directory-whatis.md) を使用すると、コピーで資格情報を管理する必要がなくなります。 ただし、新しいサーバーにデータベースをコピーする場合、ログインに基づくアクセスは通常は動作しません。これは、そのログインが新しいサーバーに存在しないためです。 別の論理サーバーにデータベースをコピーする場合のログイン管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。 

SQL Database をコピーするには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合、このページの上部の **無料評価版** をクリックしてからこの記事に戻り、最後まで完了してください。
* コピーする SQL データベース。 SQL Database がない場合は、「 [最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。

## <a name="next-steps"></a>次のステップ
* Azure ポータルを使用してデータベースをコピーするには、「 [Azure ポータルを使用した Azure SQL Database のコピー](sql-database-copy-portal.md) 」を参照してください。
* PowerShell を使用してデータベースをコピーするには、「 [PowerShell を使用した Azure SQL Database のコピー](sql-database-copy-powershell.md) 」をご覧ください。
* Transact-SQL を使用してデータベースをコピーするには、「 [T-SQL を使用した Azure SQL Database のコピー](sql-database-copy-transact-sql.md) 」をご覧ください。
* 別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「 [障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md) 」をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [ログインの管理](sql-database-manage-logins.md)
* [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)
* [データベースを BACPAC にエクスポートする](sql-database-export.md)
* [ビジネス継続性の概要](sql-database-business-continuity.md)
* [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Dec16_HO2-->


