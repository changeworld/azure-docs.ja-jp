---
title: 脅威検出 - Azure SQL Database マネージ インスタンス | Microsoft Docs
description: 脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。
services: sql-database
author: rmatchoro
manager: cguyer
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: f8c08bc16d622516958b8bd182179d07edfa4891
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Azure SQL Database マネージ インスタンスの脅威検出

SQL 脅威検出では、Azure SQL Database マネージ インスタンス (プレビュー) の データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティを検出します。

## <a name="overview"></a>概要

脅威検出では、マネージ インスタンスに対する潜在的なセキュリティの脅威を示す異常なデータベース アクティビティを検出します。 マネージ インスタンスの脅威検出は現在プレビュー段階にあります。

脅威検出で提供される新しいセキュリティ レイヤーは、異常なデータベース アクティビティに対するセキュリティ アラートを提供することによって、発生した潜在的な脅威を検出して対応できるようにします。 脅威検出を使用すると、マネージ インスタンスに対する潜在的な脅威に簡単に対処できます。セキュリティ専門家である必要はなく、高度なセキュリティ監視システムを管理する必要もありません。 完全な調査エクスペリエンスを実現するために、データベース イベントを Azure ストレージ アカウントの監査ログに書き込む、Azure マネージ インスタンス監査を有効にすることをお勧めします。 

SQL 脅威検出では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されています。保護対象の各マネージ インスタンスは、Azure Security Center Standard レベルと同じ価格 (15 ドル/ノード/月) で課金されます。保護対象のマネージ インスタンスは、それぞれ 1 つのノードとしてカウントされます。  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Azure Portal でマネージ インスタンスの脅威検出を設定する
1. [https://portal.azure.com](https://portal.azure.com) で Azure Portal を起動します。
2. 保護するマネージ インスタンスの構成ページに移動します。 **[設定]** ページで **[脅威検出]** を選択します。 
3. [脅威検出] 構成ページで、次の操作を実行します。 
   - 脅威の検出を **[ON]** にします。
   - 異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取る**電子メールのリスト**を構成します。
   - 異常な脅威監査レコードの保存先となる **Azure ストレージ アカウント**を選択します。 
4.  **[保存]** をクリックして、新しい脅威検出ポリシーまたは更新された脅威検出ポリシーを保存します。

   ![脅威検出](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>疑わしいイベントが検出されたときの異常なマネージ インスタンス アクティビティを調査する

1. 異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 

   この電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、イベントの時刻など、疑わしいセキュリティ イベントに関する情報が提供されます。 さらに、マネージ インスタンスに対する潜在的な脅威の考えられる原因と、調査や軽減のための推奨されるアクションに関する情報も提供されます。

   ![脅威検出 - 電子メール](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. 電子メールの **[View recent SQL alerts]\(最近の SQL アラートの表示\)** リンクをクリックして Azure Portal を起動し、Azure Security Center のアラート ページを表示します。このページには、マネージ インスタンスのデータベースで検出されたアクティブな SQL の脅威の概要が示されます。

   ![アクティブな脅威](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. 特定のアラートをクリックすると、さらに詳細な情報と、この脅威を調査し、今後の脅威に対処するためのアクションが表示されます。

   たとえば、SQL インジェクションはインターネットにおける Web アプリケーションの一般的なセキュリティ問題の 1 つです。 SQL インジェクションは、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。 SQL インジェクションのアラートの場合、アラートの詳細に、悪用された脆弱な SQL ステートメントが含まれています。

   ![SQL インジェクション](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>マネージ インスタンスの脅威検出アラート 

マネージ インスタンスの脅威検出では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティを検出し、次のアラートをトリガーします。
- **Vulnerability to SQL Injection (SQL インジェクションの脆弱性)**: このアラートは、アプリケーションがデータベースにエラーのある SQL 文を生成したときにトリガーされます。 これは、SQL インジェクション攻撃に対する脆弱性が存在する可能性を示すものです。 エラーのあるステートメントが生成される理由として、次の 2 つが考えられます。
 - アプリケーション コードの欠陥により、エラーのある SQL 文が作成される
 - アプリケーション コードまたはストアド プロシージャが、SQL インジェクションに悪用される可能性があるエラーのある SQL ステートメントを作成するときにユーザー入力をサニタイズしない
- **Potential SQL injection (SQL インジェクションの可能性)**: このアラートは、SQL インジェクションに対する特定されたアプリケーションの脆弱性に対してアクティブな悪用が発生したときにトリガーされます。 これは、攻撃者が脆弱なアプリケーション コードまたはストアド プロシージャを使用して、悪意のある SQL ステートメントを挿入しようとしていることを意味します。
- **Access from unusual location (通常とは異なる場所からのアクセス)**: このアラートは、だれかが通常とは異なる地理的な場所からマネージ インスタンスにログオンしたことで、マネージ インスタンスへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートによって、正当なアクション (新しいアプリケーションや開発者によるメンテナンス操作) が検出されることがあります。 このアラートによって、悪意のあるアクション (元従業員、外部の攻撃者など) が検出されることもあります。
- **Access from unusual Azure data center (通常とは異なる Azure データ センターからのアクセス)**: このアラートは、最近このマネージ インスタンスへのアクセスが確認されていない Azure データ センターから、だれかがマネージ インスタンスにログオンしたことで、マネージ インスタンスへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートによって、正当なアクション (Azure、Power BI、Azure SQL クエリ エディターの新しいアプリケーションなど) が検出されることがあります。 別のケースでは、このアラートによって Azure リソース/サービス (元従業員、外部の攻撃者) からの悪意のあるアクションが検出されます。
- **Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)**: このアラートは、だれかが通常とは異なるプリンシパル (SQL ユーザー) を使用してマネージ インスタンスにログオンしたことで、マネージ インスタンス サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートによって、正当なアクション (新しいアプリケーション開発者によるメンテナンス操作) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。
- **Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)**: このアラートは、データベースにアクセスするために潜在的に有害なアプリケーションが使用されたときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的な攻撃ツールを使用した攻撃が検出されます。
- **Brute force SQL credentials (SQL 資格情報に対するブルート フォース攻撃)**: このアラートは、異なる資格情報でログインに失敗した回数が異常に多いときにトリガーされます。 このアラートで実行中の侵入テストが検出されることがあります。 このアラートでブルート フォース攻撃が検出されることもあります。

## <a name="next-steps"></a>次の手順

- [マネージ インスタンスの概要](sql-database-managed-instance.md)を確認する
- [マネージ インスタンスの監査](https://go.microsoft.com/fwlink/?linkid=869430)の詳細を確認する 
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について参照してください
