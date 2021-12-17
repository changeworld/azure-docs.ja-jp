---
title: Advanced Threat Protection の構成
titleSuffix: Azure SQL Managed Instance
description: Advanced Threat Protection では、Azure SQL Managed Instance のデータベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティが検出されます。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: b6f03129076ea4c87b7cbd176681cd1323d9d453
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345721"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance で Advanced Threat Protection を構成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) の [Advanced Threat Protection](../database/threat-detection-overview.md) では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。 Advanced Threat Protection によって、**SQL インジェクションの可能性**、**通常とは異なる場所またはデータ センターからのアクセス**、**通常とは異なるプリンシパルまたは有害な可能性があるアプリケーションからのアクセス**、**SQL 資格情報に対するブルート フォース攻撃** を特定できます。詳細については、[Advanced Threat Protectionアラート](../database/threat-detection-overview.md#alerts)に関する記事を参照してください。

検出された脅威に関する通知は、[電子メールの通知](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event)または [Azure portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal) で受け取ることができます。

[Advanced Threat Protection](../database/threat-detection-overview.md) は、高度な SQL セキュリティ機能の統合パッケージである [Microsoft Defender for SQL](../database/azure-defender-for-sql.md) オファリングの一部です。 Advanced Threat Protection は、中央の Microsoft Defender for SQL ポータル経由でアクセスし、管理できます。

##  <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. 保護する SQL Managed Instance のインスタンスの構成ページに移動します。 **[セキュリティ]** で **[Defender for SQL]** を選択します。
3. Microsoft Defender for SQL の構成ページで、以下の操作を行います。
   - Microsoft Defender for SQL を **オン** にします。
   - 異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取る **[アラートの送信先]** メール アドレスを構成します。
   - 異常な脅威監査レコードの保存先となる **Azure ストレージ アカウント** を選択します。
   - 構成する **[Advanced Threat Protection の種類]** を選択します。 詳しくは、[Advanced Threat Protection のアラート](../database/threat-detection-overview.md)に関する記事をご覧ください。
4. **[保存]** をクリックし、新しい、または更新された Microsoft Defender for SQL ポリシーを保存します。

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="Advanced Threat Protection の設定":::

## <a name="next-steps"></a>次のステップ

- [Advanced Threat Protection](../database/threat-detection-overview.md) の詳細について学習します。
- マネージド インスタンスの詳細については、[Azure SQL Managed Instance の概要](sql-managed-instance-paas-overview.md)に関するページをご覧ください。
- 詳しくは、[Azure SQL Database の Advanced Threat Protection](../database/threat-detection-configure.md) に関するページをご覧ください。
- 詳しくは、[SQL Managed Instance の監査](./auditing-configure.md)に関するページをご覧ください。
- [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) の詳細をご確認ください。
