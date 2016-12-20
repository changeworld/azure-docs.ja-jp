---
title: "概要: SQL Database の管理ツール | Microsoft Docs"
description: "Azure SQL Database を管理するためのツールとオプションを比較します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5adb83a7b8e19ca78dd4d837b497e1ceb4444f86


---
# <a name="overview-management-tools-for-sql-database"></a>概要: SQL Database の管理ツール
このトピックでは、仕事や分野、ユーザーに合ったツールを選択できるように、Azure SQL データベースを管理するためのツールとオプションを紹介し、比較します。 適切なツールを選択するには、管理対象のデータベースの数、タスク、タスクの実行頻度を考慮します。

## <a name="azure-portal"></a>Azure ポータル
[Azure ポータル](https://portal.azure.com) は、データベースや論理サーバーを作成、更新、削除したり、データベース アクティビティを監視したりできる、Web ベースのアプリケーションです。 このツールは、Azure を使い始めたばかりの場合、管理対象のデータベースの数が少ない場合、すばやく作業を行う必要がある場合に便利です。

ポータルの使用法の詳細については、「[Azure ポータルを使用した Azure SQL データベースの管理](sql-database-manage-portal.md)」を参照してください。

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio と Visual Studio の SQL Server Data Tools
SQL Server Management Studio (SSMS) と SQL Server Data Tools (SSDT) は、クラウドのデータベースの管理や開発を行うための、コンピューター上で実行するクライアント ツールです。 Visual Studio やその他の統合開発環境 (IDE) を使い慣れているアプリケーション開発者には、 [Visual Studio の SSDT の使用をお勧めします](https://msdn.microsoft.com/library/mt204009.aspx)。 多くのデータベース管理者は、Azure SQL データベースで使用できる SSMS に精通しています。 [SSMS の最新バージョンをダウンロード](https://msdn.microsoft.com/library/mt238290) し、Azure SQL Database で作業する場合は常に最新のリリースを使用してください。 SSMS を使用して Azure SQL データベースを管理する方法の詳細については、「 [SSMS を使用した SQL データベースの管理](sql-database-manage-azure-ssms.md)」を参照してください。

> [!IMPORTANT]
> 最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) と [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持する必要があります。
>  

## <a name="powershell"></a>PowerShell
PowerShell を使って、データベースやエラスティック データベース プールを管理したり、Azure のリソースのデプロイを自動化したりできます。 Microsoft は、多数のデータベースを管理する場合や運用環境でリソースの変更およびデプロイを自動化する場合にこのツールをお勧めします。

詳しくは、[PowerShell を使用した SQL Database の管理](sql-database-manage-powershell.md)に関するページをご覧ください。

## <a name="elastic-database-tools"></a>Elastic Database ツール
エラスティック データベース ツールを使用して、次の操作を行います。 

*  [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)
*  [split-merge ツール](sql-database-elastic-scale-overview-split-and-merge.md)
* [エラスティック スケール クライアント ライブラリ](sql-database-elastic-database-client-library.md)を使用して、シングルテナント モデルまたはマルチテナント モデルでデータベースを管理する。

## <a name="additional-resources"></a>その他のリソース
* [Azure リソース マネージャー](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Nov16_HO3-->


