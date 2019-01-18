---
title: 脅威検出の構成 - Azure SQL Database Managed Instance | Microsoft Docs
description: 脅威検出では、マネージド インスタンスのデータベースに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティを検出します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: f87b7093a732639db8a53ebec68fcae8f29735f3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158671"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance で脅威検出 (プレビュー) を構成する

[SQL Database Managed Instance](sql-database-managed-instance-index.yml) 用の Azure SQL [脅威検出](sql-database-threat-detection-overview.md)では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティを検出します。 脅威検出によって、**SQL インジェクションの可能性**、**通常とは異なる場所またはデータ センターからのアクセス**、**通常とは異なるプリンシパルまたは有害な可能性があるアプリケーションからのアクセス**、**SQL 資格情報に対するブルート フォース攻撃**を特定できます。詳細については、[脅威検出アラート](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts)に関する記事を参照してください。

検出された脅威に関する通知は、[電子メールの通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)または [Azure portal](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal) で受け取ることができます。

[脅威検出](sql-database-threat-detection-overview.md)は、高度な SQL セキュリティ機能の統合パッケージである [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (ATP) の一部です。 脅威検出は、SQL ATP ポータルを使って一元的にアクセスおよび管理できます。 脅威検出サービスの料金はマネージド インスタンスあたり毎月 15 ドルです (最初の 30 日間は無料です)。

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Azure Portal でマネージド インスタンスの脅威検出を設定する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. 保護するマネージド インスタンスの構成ページに移動します。 **[設定]** ページで **[脅威検出]** を選択します。
3. [脅威検出] 構成ページで、次の操作を実行します。
   - 脅威の検出を **[ON]** にします。
   - 異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取る**電子メールのリスト**を構成します。
   - 異常な脅威監査レコードの保存先となる **Azure ストレージ アカウント**を選択します。
4. **[保存]** をクリックして、新しい脅威検出ポリシーまたは更新された脅威検出ポリシーを保存します。

   ![脅威検出](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>次の手順

- [脅威の検出](sql-database-threat-detection-overview.md)の詳細について学習します。
- [Managed Instance の概要](sql-database-managed-instance.md)に関する記事でマネージド インスタンスの詳細について学習します。
- [Single Database の脅威検出](sql-database-threat-detection.md)の詳細について学習します。
- [Managed Instance の監査](https://go.microsoft.com/fwlink/?linkid=869430)の詳細について学習します。
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について学習します。
