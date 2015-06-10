<properties 
	pageTitle="概要: SQL データベースの管理ツール" 
	description="Azure SQL データベースを管理するためのツールとオプションを比較します。" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="vinsonyu"/>

# 概要: SQL データベースの管理ツール

このトピックでは、仕事や分野、ユーザーに合ったツールを選択できるように、SQL データベースを管理するためのツールとオプションを紹介し、比較します。適切なツールを選択するには、管理対象のデータベースの数、タスク、タスクの実行頻度を考慮します。



## Azure ポータル


[Azure ポータル](http://portal.azure.com)は、Azure SQL データベースや論理サーバーを作成、更新、削除したり、データベース リソースを監視したりできる、Web ベースの管理ポータルです。このツールは、Azure を使い始めたばかりの場合、管理対象のデータベースの数が少ない場合、すばやく作業を行う必要がある場合に便利です。

ポータルの使用法の詳細については、「[Azure の管理ポータルを使用した SQL データベースの管理](sql-database-manage-portal.md)」を参照してください。

## SQL Server Management Studio と Visual Studio の SQL Server Data Tools


SQL Server Management Studio (SSMS) と Visual Studio の SQL Server Data Tools (SSDT) は、クラウドのデータベースに接続し、管理や開発を行うための、コンピューター上で実行するクライアント ツールです。Visual Studio やその他の統合開発環境 (IDE) を使い慣れているアプリケーション開発者には、Visual Studio の SSDT の使用をお勧めします。ハイブリッド環境での SQL Server データベースの管理など、SSDT では対応できない高度な SQL 機能が必要な場合は、SSMS を使用してください。

SSMS と SSDT を使用して Azure SQL データベースを管理する方法の詳細については、[SSMS を使用した SQL データベースの管理](sql-database-manage-azure-ssms.md)に関するページを参照してください。


## コマンド ライン ツール

PowerShell などのコマンド ライン ツールを使って、Azure SQL データベースを管理したり、Azure のリソースのデプロイを自動化したりできます。Microsoft では、多数の Azure SQL データベースを管理する場合や運用環境にリソースの変更をデプロイする場合にこのツールをお勧めします。

コマンド ライン ツールを使った Azure SQL データベースの管理の詳細については、[ここをクリック](sql-database-command-line-tools.md)してください。

<!---HONumber=58-->