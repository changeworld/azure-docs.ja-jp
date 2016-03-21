<properties
	pageTitle="サーバー間、サブスクリプション間、および Azure の内外にデータベースを移動します。"
	description="Azure SQL Database でデータとデータベースをコピー、移動、および移行するための簡単な手順。"
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
	ms.date="12/11/2015"
	ms.author="v-shysun"/>

# サーバー間、サブスクリプション間、および Azure の内外にデータベースを移動する
##同じサブスクリプション内の別のサーバーにデータベースを移動するには
- [Azure ポータル](https://portal.azure.com)で **[SQL データベース]** をクリックし、一覧からデータベースを選択してから、**[コピー]** をクリックします。詳細については、「[Azure SQL データベースのコピー](sql-database-copy.md)」を参照してください。

## サブスクリプション間でデータベースを移動するには
- [Azure ポータル](https://portal.azure.com)で **[SQL Server]** をクリックし、一覧からデータベースをホストするサーバーを選択します。**[移動]** をクリックし、移動するリソースと移動先のサブスクリプションを選択します。

## SQL データベースを Azure に移行するには
- データベースの互換性を確認し、ニーズに応じた適切な移行方法を選択します。「SQL Server データベースの移行」のガイドラインとオプションに従います。

## Azure の外部で使用するためにデータベースのコピーを作成するには
- [BACPAC ファイルをエクスポートします。](sql-database-export.md)

<!---HONumber=AcomDC_1217_2015-->