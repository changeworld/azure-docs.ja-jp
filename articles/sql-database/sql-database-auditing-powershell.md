---
title: "PowerShell: Azure SQL Database 監査を管理する | Microsoft Docs"
description: "PowerShell を使用して、データベース イベントを追跡して Azure Storage アカウントの監査ログに書き込むように Azure SQL Database 監査を構成します。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>PowerShell を使用して SQL Database 監査を構成して管理する

次のセクションで、PowerShell を使用して監査を構成して管理する方法について説明します。 Azure Portal を使用して監査を構成および管理する方法については、[Azure Portal での監査の構成](sql-database-auditing-portal.md)に関するページを参照してください。 REST API を使用して監査を構成および管理する方法については、[REST API を使用した監査の構成](sql-database-auditing-rest.md)に関するページを参照してください。

監査の概要については、[SQL Database 監査](sql-database-auditing.md)に関するページを参照してください。

## <a name="powershell-cmdlets"></a>PowerShell コマンドレット

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>次のステップ

* Azure Portal を使用して監査を構成および管理する方法については、[Azure Portal でのデータベース監査の構成](sql-database-auditing-portal.md)に関するページを参照してください。 
* PowerShell を用いて監査を構成および管理する方法については、[REST API を使用したデータベース監査の構成](sql-database-auditing-rest.md)に関するページを参照してください。
* 監査の概要については、[データベース監査](sql-database-auditing.md)に関するページを参照してください。


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

