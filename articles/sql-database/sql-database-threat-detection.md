---
title: 脅威の検出 - Azure SQL Database | Microsoft Docs
description: 脅威検出では、単一データベースまたはエラスティック プール内のデータベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティを検出します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 3f4a120e2aaf2925805bec26f721d5cfb4194bf1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041545"
---
# <a name="azure-sql-database-threat-detection-for-single-database"></a>Single Database 用の Azure SQL Database の脅威検出

[SQL Database](sql-database-technical-overview.md) Single Database 用の Azure SQL [脅威検出](sql-database-threat-detection-overview.md)では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティを検出します。 脅威検出によって、**SQL インジェクションの可能性**、**通常とは異なる場所またはデータ センターからのアクセス**、**通常とは異なるプリンシパルまたは有害な可能性があるアプリケーションからのアクセス**、**SQL 資格情報に対するブルート フォース攻撃**を特定できます。詳細については、[脅威検出アラート](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts)に関する記事を参照してください。

検出された脅威に関する通知は、[電子メールの通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)または [Azure portal](sql-database-threat-detection.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal) で受け取ることができます。

[脅威検出](sql-database-threat-detection-overview.md)は、高度な SQL セキュリティ機能の統合パッケージである [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (ATP) の一部です。 脅威検出は、SQL ATP ポータルを使って一元的にアクセスおよび管理できます。 脅威検出サービスの料金は論理サーバーあたり毎月 15 ドルです (最初の 30 日間は無料です)。

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal でデータベースの脅威検出を設定する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. 保護する Azure SQL Database サーバーの構成ページに移動します。 セキュリティの設定で、**[Advanced Threat Protection]** を選びます。
3. **[Advanced Threat Protection]** の構成ページで次のようにします。

   - サーバーでの Advanced Threat Protection を有効にします
   - **[脅威検出の設定]** の **[アラートの送信先]** テキスト ボックスで、異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取るメール アドレスの一覧を指定します。
  
   ![脅威検出の設定](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell を使用して脅威検出を設定する

スクリプトの例については、[PowerShell を使用した監査と脅威検出の構成](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [脅威の検出](sql-database-threat-detection-overview.md)の詳細について学習します。
- [Managed Instance の脅威検出](sql-database-managed-instance-threat-detection.md)の詳細について学習します。  
- [Azure SQL Database の Advanced Threat Protection](sql-advanced-threat-protection.md) についてさらに詳しく学習します。
- [Azure SQL Database 監査](sql-database-auditing.md)の詳細について参照してください
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について参照してください
- 価格の詳細については、[SQL Database の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください  
