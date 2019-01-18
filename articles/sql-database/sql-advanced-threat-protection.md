---
title: Advanced Threat Protection - Azure SQL Database | Microsoft Docs
description: 機密データの探索と分類、データベースの脆弱性の管理、Azure SQL Database への脅威を示す可能性がある異常なアクティビティの検出などの機能について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 12/16/2018
ms.openlocfilehash: 40f6b6effa509fd2cfa40ecfc758ac37e0a66778
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538618"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL Database の Advanced Threat Protection

SQL Advanced Threat Protection は、高度な SQL セキュリティ機能のための統合パッケージです。 機密データの検出と分類、データベースの潜在的な脆弱性の検出と軽減、データベースへの脅威を示す可能性がある異常なアクティビティの検出を行う機能が含まれています。 これらの機能を 1 つの場所で有効にして管理できます。 

## <a name="overview"></a>概要

SQL Advanced Threat Protection (ATP) は、データの探索と分類、脆弱性の評価、脅威の検出などの高度な SQL セキュリティ機能のセットを提供します。 

- [データの検出と分類](sql-database-data-discovery-and-classification.md) (現在プレビュー段階) では、Azure SQL Database に組み込まれる、データベースの機微なデータの検出、分類、ラベル付と保護を行う機能が用意されています。 データベースの分類の状態を把握し、データベース内やその境界を越えて機密データへのアクセスを追跡するために使用できます。
- [脆弱性評価](sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティの状態を表示することができ、セキュリティの問題を解決して、データベースのセキュリティを強化するために実行可能な手順が含まれます。
- [脅威検出](sql-database-threat-detection-overview.md)では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかるとすぐにセキュリティ通知を提供します。 脅威の検出によるアラートは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法を提供します。

SQL ATP を一度有効にすれば、含まれるすべての機能が有効になります。 1 回のクリックでデータベース サーバー全体に対して ATP を有効にし、サーバー上のすべてのデータベースに適用できます。 ATP の設定を有効にしたり、管理したりするには、[SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) ロール、SQL データベース管理者ロール、または SQL サーバー管理者ロールに属している必要があります。 

ATP の価格は Azure Security Center Standard レベルと同じです。ここで保護対象の各 SQL Database サーバーは 1 つのノードとしてカウントされます。 新しく保護されたリソースは、Security Center Standard レベルの無料試用版として使用できます。 詳しくは、[Azure Security Center の価格ページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。


## <a name="getting-started-with-atp"></a>ATP の使用開始 
ATP の使用を始める手順は以下のとおりです。 

## <a name="1-enable-atp"></a>1.ATP を有効にする

[Azure SQL Database] ウィンドウの **[セキュリティ]** で、**[Advanced Threat Protection]** に移動することにより、ATP を有効にします。 サーバー上のすべてのデータベースについて ATP を有効にするには、**[サーバーで Advanced Threat Protection を有効にする]** をクリックします。

![ATP を有効にする](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> ATP のコストは、ノード全体が SQL 論理サーバーである場合の、ノードあたりの Azure Security Center Standard レベルの料金に揃えられています。 したがって、1 回支払えば、サーバー上のすべてのデータベースが ATP で保護されます。 最初は無料試用版で ATP を試すことができます。

## <a name="2-configure-vulnerability-assessment"></a>2.脆弱性評価を構成する

脆弱性評価を使い始めるには、スキャン結果を保存するストレージ アカウントを構成する必要があります。 そのためには、[脆弱性評価] カードをクリックします。

![VA を構成する](./media/sql-advanced-protection/configure_va.png) 

スキャン結果を保存するためのストレージ アカウントを選択または作成します。 定期的な反復スキャンを有効にして、脆弱性評価が毎週 1 回自動的に実行するように構成することもできます。 スキャン結果の概要は、指定したメール アドレスに送信されます。

![VA の設定](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>手順 3.データの分類、脆弱性の追跡、脅威アラートの調査を開始する

**[データの検出と分類]** カードをクリックして、分類が推奨される機密性の高い列を表示し、永続的な機密ラベルを使ってデータを分類します。 **[脆弱性評価]** カードをクリックして、脆弱性のスキャンとレポートを表示および管理し、セキュリティ水準を追跡します。 セキュリティ通知を受け取った場合は、**[脅威検出]** カードをクリックして、通知の詳細を表示し、Azure Security Center のセキュリティ通知ページを使って Azure サブスクリプション内のすべての通知の統合レポートを表示します。

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4.SQL Server 上で ATP の設定を管理する

Advanced Threat Protection の設定を表示するには、[SQL Server] ウィンドウの **[セキュリティ]** で **[Advanced Threat Protection]** に移動します。 このページでは、ATP を有効または無効にしたり、SQL Server 全体に対する Threat Detection の設定を変更したりできます。

![サーバーの設定](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5.SQL Database の ATP の設定を管理する

特定のデータベースに対する ATP の脅威検出の設定をオーバーライドするには、**[データベース レベルで Advanced Threat Protection を有効にします]** チェック ボックスをオンにします。 このオプションは、サーバー上の全データベースについて受け取る通知の代わりに、またはそれに加えて、個別のデータベースに対する脅威検出通知を別に受け取る特定の要件がある場合にのみ使います。 

このチェック ボックスをオンにした後、**[このデータベースの脅威検出の設定]** をクリックして、そのデータベースに対する関連設定を構成します。

![データベースと脅威検出の設定](./media/sql-advanced-protection/database_threat_detection_settings.png) 

サーバーの Advanced Threat Protection の設定には、ATP データベース ウィンドウからアクセスすることもできます。 メインの ATP ウィンドウで **[設定]** をクリックし、**[Advanced Threat Protection サーバー設定の表示]** をクリックします。 

![データベース設定](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>次の手順 

- [データの検出と分類](sql-database-data-discovery-and-classification.md)についてさらに詳しく学習する 
- [脆弱性評価](sql-vulnerability-assessment.md)についてさらに詳しく学習する 
- [脅威の検出](sql-database-threat-detection.md)についてさらに詳しく学習する
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について参照してください
