<properties
	pageTitle="Azure SQL データベースのアクセス許可およびアクセスのトラブルシューティング"
	description="共通のアクセス許可、アクセス、ユーザー、およびログインの問題をトラブルシューティングするための簡単な手順"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="v-shysun"/>

#一般的な Azure SQL Database のアクセス許可およびアクセスの問題のトラブルシューティング
Azure SQL Database へのアクセスの付与および削除を行う簡単な手順については、このトピックをご覧ください。より包括的な情報については、次を参照してください。

- [Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)
- [SQL Database の保護](sql-database-security.md)
- [SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

##論理サーバーの管理パスワードを変更するには
- [Azure ポータル](https://portal.azure.com)で **[SQL Server]** をクリックし、一覧からサーバーを選択して、**[パスワードのリセット]** をクリックします。
##権限のある IP アドレスのみがサーバーへのアクセスを許可されるようにするには
- 「[方法: ファイアウォール設定を構成する (SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。

##ユーザー データベースに包含データベース ユーザーを作成するには
- [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) ステートメントを使用し、「[包含データベース ユーザー - データベースの移植性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

## Azure Active Directory を使用して包含データベース ユーザーを認証するには
- 「[Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)」を参照してください。

## 仮想マスター データベースで高い特権を持つユーザーの追加ログインを作成するには
- [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) ステートメントを使用し、「[Azure SQL Database におけるデータベースとログインを管理する](sql-database-manage-logins.md)」の「ログイン管理」のセクションで詳細を確認してください。

<!---HONumber=AcomDC_0330_2016------>