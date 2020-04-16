---
title: Managed Instance のアラートと通知を設定する (Azure portal)
description: Azure portal を使用して SQL Managed Instance アラートを作成します。このアラートにより、指定した条件が満たされたときに通知やオートメーションをトリガーできます。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/02/2020
ms.openlocfilehash: a332627d149a36ba5d5beb2626023e58a221f0d6
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639186"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Azure portal を使用して Azure SQL Managed Instance のアラートを作成する

## <a name="overview"></a>概要

この記事では、Azure portal を使用して、Azure SQL Managed Instance Database のデータベースのアラートを設定する方法について説明します。 あるメトリック (インスタンスのストレージ サイズや CPU 使用率など) がしきい値に達したら、アラートはユーザーに電子メールを送信するか、または Web hook を呼び出すことができます。 この記事では、アラート期間を設定するベスト プラクティスも紹介します。

監視メトリック、イベント、Azure サービスに基づいて通知を受け取ることができます。

* **メトリック値** - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。
* **アクティビティ ログ イベント** - アラートは、" *すべて* " のイベントに対して、または特定数のイベントが発生したときにのみトリガーされます。

アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービス管理者/共同管理者に電子メール通知を送信する
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [コマンド ライン インターフェイス (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure ポータルでメトリックにアラート ルールを作成する

1. [ポータル](https://portal.azure.com/)で、監視するリソースを見つけて選択します。
2. [監視] セクションで、 **[アラート]** を選択します。 テキストとアイコンは、リソースごとに多少異なる場合があります。  

   ![監視](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. **[新しいアラート ルール]** ボタンを選択して **[ルールの作成]** ページを開きます。
   ![ルールの作成](media/sql-database-insights-alerts-portal/create-rule.png)

4. **[条件]** セクションで、 **[追加]** をクリックします。
   ![条件を定義する](media/sql-database-insights-alerts-portal/create-rule.png)
5. **[シグナル ロジックの構成]** ページで、シグナルを選択します。
   ![シグナルを選択する](media/sql-database-insights-alerts-portal/select-signal.png)
6. **[CPU の割合]** などのシグナルを選択すると、 **[シグナル ロジックの構成]** ページが表示されます。
   ![シグナル ロジックを構成する](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. このページで、しきい値の種類、演算子、集計の種類、しきい値、集計の粒度、および評価の頻度を構成します。 次に、 **[Done]** をクリックします。
8. **[ルールの作成]** で、既存の**アクション グループ**を選択するか、新しいグループを作成します。 アクション グループを使用して、アラート条件が発生したときに実行するアクションを定義できます。
  ![アクション グループを定義する](media/sql-database-insights-alerts-portal/action-group.png)

9. ルールの名前を定義し、必要に応じて説明を入力し、ルールの重大度レベルを選択し、ルールの作成時にルールを有効にするかどうかを選択した後、 **[Create rule alert]\(ルールのアラートの作成\)** をクリックしてメトリック ルールのアラートを作成します。

10 分以内にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="next-steps"></a>次のステップ

* [アラートでの webhook の構成](../azure-monitor/platform/alerts-webhooks.md)に関する詳細情報を確認します。
