---
ms.service: sql-database
ms.subservice: deployment-configuration
ms.topic: include
ms.date: 12/17/2020
author: MashaMSFT
ms.author: mathoma
ms.openlocfilehash: e57370bdceed2b22120985cc68da12152ed7032c
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255216"
---
  次のいずれかのクイックスタートを使用して、データベースを作成し、構成できます。

  | アクション | SQL Database | SQL Managed Instance | Azure VM 上の SQL Server | Azure Synapse Analytics |
  |:--- |:--- |:---|:---|:---|
  | 作成| [ポータル](../database/single-database-create-quickstart.md) | [ポータル](../managed-instance/instance-create-quickstart.md) | [ポータル](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) | [ポータル](../../synapse-analytics/quickstart-create-workspace.md) |
  || [CLI](../database/scripts/create-and-configure-database-cli.md) | | | [CLI](../../synapse-analytics/quickstart-create-workspace-cli.md) |
  || [PowerShell](../database/scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md) | [PowerShell](../../synapse-analytics/quickstart-create-workspace-powershell.md) |
  || | | [デプロイ テンプレート](../virtual-machines/windows/create-sql-vm-resource-manager-template.md) | [デプロイ テンプレート](../../synapse-analytics/quickstart-deployment-template-workspaces.md) | 
  | 構成 | [サーバーレベルの IP ファイアウォール規則](../database/firewall-create-server-level-portal-quickstart.md)| [VM からの接続](../managed-instance/connect-vm-instance-configure.md)| |
  |||[オンプレミスからの接続](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server インスタンスに接続する](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) |
  | 接続情報の取得 | [Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)|[Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)| [SQL VM](../virtual-machines/windows/sql-vm-create-portal-quickstart.md?#connect-to-sql-server)| [Synapse SQL](../../synapse-analytics/sql/connect-overview.md#find-your-server-name)|
  |||||
