---
title: "脅威の検出 - Azure SQL Database | Microsoft Docs"
description: "脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: security-protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c6f580a35115ed93e5575f51956e55dc7b5b8d0a
ms.lasthandoff: 04/15/2017


---
# <a name="sql-database-threat-detection"></a>SQL Database の脅威の検出

脅威の検出は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。  脅威の検出は現在プレビュー段階です。

## <a name="overview"></a>概要

脅威の検出で提供される新しいセキュリティ階層は、異常なアクティビティに対するセキュリティ アラートを提供することによって、発生する可能性のある脅威をユーザーが検出して対応できるようにします。  ユーザーは、[SQL Database の監査](sql-database-auditing.md)を使って疑わしいイベントを調査し、データベース内のデータに対するアクセス、侵害、または悪用の試行による結果かどうかを判断できます。
脅威の検出を使用するとデータベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。

たとえば、脅威の検出は、SQL インジェクションの可能性を示す特定の異常なデータベース アクティビティを検出します。 SQL インジェクションはインターネットでの一般的な Web アプリケーションのセキュリティの問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Azure Portal でデータベースの脅威検出を設定する
1. [https://portal.azure.com](https://portal.azure.com) で Azure Portal を起動します。
2. 監視する SQL Database の構成ブレードに移動します。 [設定] ブレードで、**[監査と脅威の検出]** を選択します。
   
    ![ナビゲーション ウィンドウ][1]
3. **[監査と脅威の検出]** 構成ブレードで、監査を **[ON]** にすると、脅威の検出の設定が表示されます。
   
    ![ナビゲーション ウィンドウ][2]
4. 脅威の検出を **[ON]** にします。
5. 異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取る電子メールのリストを構成します。
6. **[監査と脅威の検出]** ブレードの **[保存]** をクリックして、新規または更新済みの監査と脅威の検出のポリシーを保存します。
   
    ![ナビゲーション ウィンドウ][3]

## <a name="set-up-threat-detection-using-powershell"></a>PowerShell を使用して脅威検出を設定する

スクリプトの例については、[PowerShell を使用した監査と脅威の検出](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページをご覧ください。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>疑わしいイベントが検出されたときの異常なデータベース アクティビティの調査
1. 異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 <br/>
   電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、イベントの時刻など、疑わしいセキュリティ イベントについての情報が提供されます。 さらに、データベースへの潜在的な脅威の考えられる原因および調査や緩和のための推奨されるアクションについての情報も提供されます。<br/>
   
    ![ナビゲーション ウィンドウ][4]
2. 電子メールの **[Azure SQL 監査ログ]** リンクをクリックすると、Azure ポータルが起動し、疑わしいイベントの時刻前後の関連する監査レコードが表示されます。
   
    ![ナビゲーション ウィンドウ][5]
3. 監査レコードをクリックすると、SQL ステートメント、エラーの理由、クライアントの IP など、疑わしいデータベース アクティビティについての詳細が表示されます。
   
    ![ナビゲーション ウィンドウ][6]
4. [監査レコード] ブレードの **[Excel で開く]** をクリックすると、あらかじめ構成されている Excel テンプレートが開き、疑わしいイベントの時刻前後の監査ログがインポートされて詳細な分析が実行されます。<br/>
   **注:** Excel 2010 以降では、Power Query と**高速結合**の設定が必要です。
   
    ![ナビゲーション ウィンドウ][7]
5. **[高速結合]** の設定を構成するには、**[POWER QUERY]** リボン タブの **[オプション]** を選択して [オプション] ダイアログ ボックスを表示します。 [プライバシー] セクションを選択し、2 番目のオプション [プライバシー レベルを無視し、可能であればパフォーマンスを向上させる] をオンにします。
   
    ![ナビゲーション ウィンドウ][8]
6. SQL 監査ログをロードするには、[設定] タブのパラメーターが正しく設定されていることを確認してから、データのリボンを選択し、[すべて更新] ボタンをクリックします。
   
    ![ナビゲーション ウィンドウ][9]
7. **[SQL 監査ログ]** シートに結果が表示されます。このシートでは、検出された異常なアクティビティを詳細に分析し、アプリケーションでのセキュリティ イベントの影響を軽減できます。

## <a name="next-steps"></a>次のステップ

* SQL Database の監査の概要については、[データベースの監査に関するページ](sql-database-auditing.md)をご覧ください。
* PowerShell スクリプトの例については、[PowerShell を使用した監査と脅威の検出](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページをご覧ください。

<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png


