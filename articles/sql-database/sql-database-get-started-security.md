<properties
	pageTitle="SQL Database チュートリアル: セキュリティの概要"
	description="ユーザー アカウントを作成してアクセスし、データベースを管理する方法について説明します。"
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/13/2016"
	ms.author="carlrab"/>

# SQL Database チュートリアル: Azure ポータルを使用してデータベースにアクセスして管理するための SQL Database ユーザー アカウントを作成する

このチュートリアルでは、Azure ポータルの使用方法について説明します。

- サーバー レベルのプリンシパル ログインを使用して SQL Database にログインする
- SQL Database ユーザー アカウントを作成する
- SQL Database ユーザー アカウントにユーザー データベースでの dbo アクセス許可を付与する
- サーバー レベルのプリンシパルではないユーザー アカウントを使用して SQL Database に接続する 

[AZURE.INCLUDE [ログイン](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SQL Database 論理サーバーを作成する](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[AZURE.INCLUDE [SQL Database データベースを作成する](../../includes/sql-database-create-new-database-user.md)]

[AZURE.INCLUDE [SQL Database データベースを作成する](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[AZURE.INCLUDE [SQL Database データベースを作成する](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## 次のステップ
この SQL Database チュートリアルを完了して、ユーザー アカウントを作成し、ユーザー アカウントに dbo アクセス許可を付与した後は、[SQL Database のセキュリティ](sql-database-manage-logins.md)についてさらに詳しく学習できます。

<!---HONumber=AcomDC_0420_2016-->