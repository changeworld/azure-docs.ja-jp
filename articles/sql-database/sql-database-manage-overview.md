<properties
	pageTitle="概要: SQL Database の管理ツール"
	description="Azure SQL Database を管理するためのツールとオプションを比較します。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="sstein"/>

# 概要: SQL Database の管理ツール

このトピックでは、仕事や分野、ユーザーに合ったツールを選択できるように、Azure SQL データベースを管理するためのツールとオプションを紹介し、比較します。適切なツールを選択するには、管理対象のデータベースの数、タスク、タスクの実行頻度を考慮します。

## Azure ポータル

[Azure ポータル](https://portal.azure.com)は、データベースや論理サーバーを作成、更新、削除したり、データベース アクティビティを監視したりできる、Web ベースのアプリケーションです。このツールは、Azure を使い始めたばかりの場合、管理対象のデータベースの数が少ない場合、すばやく作業を行う必要がある場合に便利です。

ポータルの使用法の詳細については、「[Azure クラシック ポータルを使用した SQL データベースの管理](sql-database-manage-portal.md)」を参照してください。

## SQL Server Management Studio と Visual Studio の SQL Server Data Tools

SQL Server Management Studio (SSMS) と Visual Studio の SQL Server Data Tools (SSDT) は、クラウドのデータベースに接続し、管理や開発を行うための、コンピューター上で実行するクライアント ツールです。Visual Studio やその他の統合開発環境 (IDE) を使い慣れているアプリケーション開発者には、[Visual Studio の SSDT の使用をお勧めします](https://msdn.microsoft.com/library/mt204009.aspx)。多くのデータベース管理者は、Azure SQL データベースで使用できる SSMS に精通しています。[SSMS の最新バージョンをダウンロード](https://msdn.microsoft.com/library/mt238290)し、Azure SQL Database で作業する場合は常に最新のリリースを使用してください。SSMS を使用して Azure SQL データベースを管理する方法の詳細については、「[SSMS を使用した SQL データベースの管理](sql-database-manage-azure-ssms.md)」を参照してください。

> [AZURE.IMPORTANT] 最新バージョンの SQL Server Management Studio と SQL Server Data Tools for Visual Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持する必要があります。こうしたツールのバージョンが古い場合、SQL Database と適切に連携しません。

## コマンド ライン ツール

PowerShell などのコマンド ライン ツールを使って、データベースやエラスティック データベース プールを管理したり、Azure のリソースのデプロイを自動化したりできます。Microsoft は、多数のデータベースを管理する場合や運用環境でリソースの変更およびデプロイを自動化する場合にこのツールをお勧めします。

コマンド ライン ツールを使った Azure SQL Database の管理の詳細については、「[PowerShell を使用した SQL Database の管理](sql-database-command-line-tools.md)」を参照してください。

## Elastic Database ツール
エラスティック データベース ツールを使用して、次の操作を行います。

* [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)を使用して、一連のデータベースに対して T-SQL スクリプトを実行する
* [split-merge ツール](sql-database-elastic-scale-overview-split-and-merge.md)で、マルチテナント モデル データベースをシングルテナント モデルに移動する
* [エラスティック スケール クライアント ライブラリ](sql-database-elastic-database-client-library.md)を使用して、シングルテナント モデルまたはマルチテナント モデルでデータベースを管理する。
 

<!---HONumber=AcomDC_0706_2016-->