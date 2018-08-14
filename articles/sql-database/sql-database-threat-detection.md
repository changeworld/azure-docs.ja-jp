---
title: 脅威の検出 - Azure SQL Database | Microsoft Docs
description: 脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.subservice: advanced-threat-protection
ms.custom: security
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: c82692525f06fda93f94a8d856eb65254e5fd211
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003945"
---
# <a name="azure-sql-database-threat-detection"></a>Azure SQL Database の脅威の検出

Azure SQL Database の脅威検出は、データベースにアクセスしたりデータベースを悪用したりしようとする、異常で有害な可能性がある不自然なアクティビティを検出します。

脅威検出は、高度な SQL セキュリティ機能の統合パッケージである [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (ATP) の一部です。 脅威検出は、SQL ATP ポータルを使って一元的にアクセスおよび管理できます。

## <a name="what-is-threat-detection"></a>脅威検出とは

SQL の脅威の検出で提供される新しいセキュリティ階層は、異常なアクティビティに対するセキュリティ アラートを提供することによって、潜在的な脅威をユーザーが検出し、それが発生したときに対応できるようにします。 不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 SQL の脅威検出は、アラートを [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合します。これには、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が含まれます。 SQL の脅威検出を使うと、データベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。 

完全な調査エクスペリエンスを実現するために、データベース イベントを Azure ストレージ アカウントの監査ログに書き込む、[SQL Database Auditing](sql-database-auditing.md) を有効にすることをお勧めします。  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal でデータベースの脅威検出を設定する
1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. 保護する Azure SQL Database サーバーの構成ページに移動します。 セキュリティの設定で、**[Advanced Threat Protection]** を選びます。
3. **[Advanced Threat Protection]** の構成ページで次のようにします。

   - サーバーでの Advanced Threat Protection を有効にします
   - **[脅威検出の設定]** の **[アラートの送信先]** テキスト ボックスで、異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取るメール アドレスの一覧を指定します。
  
   ![脅威検出の設定](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell を使用して脅威検出を設定する

スクリプトの例については、[PowerShell を使用した監査と脅威検出の構成](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページを参照してください。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>疑わしいイベントが検出されたときの異常なデータベース アクティビティの調査

異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、アプリケーション名、イベントの時刻など、疑わしいセキュリティ イベントについての情報が提供されます。 さらに、データベースへの潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての情報も提供されます。

![異常アクティビティ レポート](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. 電子メールの **[View recent SQL alerts]\(最近の SQL アラートの表示\)** リンクをクリックして Azure Portal を起動し、Azure Security Center のアラート ページを表示します。このページには、SQL データベースで検出されたアクティブな脅威の概要が示されます。

   ![アクティビティの脅威](./media/sql-database-threat-detection/active_threats.png)

2. 特定のアラートをクリックすると、さらに詳細な情報と、この脅威を調査し、今後の脅威に対処するためのアクションが表示されます。

   たとえば、SQL インジェクションはインターネットにおける Web アプリケーションの最も一般的なセキュリティ問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。 SQL インジェクションのアラートの場合、アラートの詳細に、悪用された脆弱な SQL ステートメントが含まれています。

   ![特定のアラート](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Azure Portal でデータベースの脅威検出のアラートを調査する

SQL Database の脅威の検出では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されています。 Azure portal のデータベースおよび SQL ATP ブレード内のライブ SQL 脅威検出タイルでは、アクティブな脅威の状態が追跡されます。

**[脅威検出アラート]** をクリックすると、Azure Security Center のアラート ページが起動され、データベースに対して検出されたアクティブな SQL 脅威の概要が表示されます。

   ![脅威検出アラート](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![脅威検出アラート 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Azure SQL Database の脅威検出アラート 
Azure SQL Database の脅威検出では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティを検出し、次のアラートをトリガーします。
- **Vulnerability to SQL Injection (SQL インジェクションの脆弱性)**: このアラートは、アプリケーションがデータベースにエラーのある SQL 文を生成したときにトリガーされます。 これは、SQL インジェクション攻撃に対する脆弱性が存在する可能性を示すものです。 エラーのあるステートメントが生成される理由として、次の 2 つが考えられます。
   - アプリケーション コードの欠陥により、エラーのある SQL 文が作成される
   - アプリケーション コードまたはストアド プロシージャが、SQL インジェクションに悪用される可能性があるエラーのある SQL ステートメントを作成するときにユーザー入力をサニタイズしない
- **Potential SQL injection (SQL インジェクションの可能性)**: このアラートは、SQL インジェクションに対する特定されたアプリケーションの脆弱性に対してアクティブな悪用が発生したときにトリガーされます。 これは、攻撃者が脆弱なアプリケーション コードまたはストアド プロシージャを使用して悪意のある SQL 文を挿入しようとしていることを意味します。
- **Access from unusual location (通常とは異なる場所からのアクセス)**: このアラートは、だれかが通常とは異なる地理的な場所から SQL Server にログオンしたことで SQL Server へのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from unusual Azure data center (通常とは異なる Azure データ センターからのアクセス)**: このアラートは、だれかが最近このサーバーで記録された通常とは異なる Azure データ センターから SQL Server にログオンしたことで SQL Server へのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (Azure、Power BI、Azure SQL クエリ エディターの新しいアプリケーション) が検出されることがあります。 別のケースでは、このアラートによって Azure リソース/サービス (元従業員、外部の攻撃者) からの悪意のあるアクションが検出されます。
- **Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)**: このアラートは、だれかが通常とは異なるプリンシパル (SQL ユーザー) を使用して SQL Server にログオンしたことで SQL Server へのアクセス パターンに変化が生じたときにトリガーされます。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)**: このアラートは、データベースにアクセスするために潜在的に有害なアプリケーションが使用されたときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的な攻撃ツールを使用した攻撃が検出されます。
- **Brute force SQL credentials (SQL 資格情報に対するブルート フォース攻撃)**: このアラートは、異なる資格情報でログインに失敗した回数が異常に多いときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートでブルート フォース攻撃が検出されます。

## <a name="next-steps"></a>次の手順

* [Azure SQL Database の Advanced Threat Protection](sql-advanced-threat-protection.md) についてさらに詳しく学習します。 
* [Azure SQL Database 監査](sql-database-auditing.md)の詳細について参照してください
* [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について参照してください
* 価格の詳細については、[SQL Database の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください  
