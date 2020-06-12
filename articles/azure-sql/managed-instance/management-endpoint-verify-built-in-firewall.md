---
title: ビルトイン ファイアウォールのポートセキュリティを確認する
description: Azure SQL Managed Instance のビルトイン ファイアウォールの保護機能を検証する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 04a630e51a68fab8f6c60262fbd14fad2e4aef1c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84031153"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Azure SQL Managed Instance のビルトイン ファイアウォールを検証する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

SQL Managed Instance を保護するネットワーク セキュリティ グループ (NSG) は、**すべての発信元**に対し、管理ポート 9000、9003、1438、1440、1452 を開放することが Azure SQL Managed Instance の[必須のインバウンド セキュリティ規則](connectivity-architecture-overview.md#mandatory-inbound-security-rules)によって義務付けられています。 これらのポートは NSG レベルでは開放されますが、ネットワーク レベルではビルトインのファイアウォールによって保護されます。

## <a name="verify-firewall"></a>ファイアウォールを検証する

これらのポートを検証するには、任意のセキュリティ スキャナー ツールを使用して、それらのポートをテストします。 次のスクリーンショットは、そうしたツールの 1 つについて、その使い方を示したものです。

![ビルトインのファイアウォールを検証する](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>次のステップ

SQL Managed Instances と接続の詳細については、[Azure SQL Database Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)に関するページを参照してください。
