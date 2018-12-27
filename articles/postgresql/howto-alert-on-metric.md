---
title: Azure Portal での Azure Database for PostgreSQL のメトリック アラートの構成
description: この記事では、Azure Portal から Azure Database for PostgreSQL のメトリック アラートを構成およびアクセスする方法について説明します。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b57827cb4e8c1105c88b6fab217e17d6cf4db056
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271233"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Azure Portal を使用して Azure Database for PostgreSQL のメトリックのアラートを設定する 

この記事では、Azure Portal を使用して Azure Database for PostgreSQL のアラートを設定する方法について説明します。 お使いの Azure のサービスの監視メトリックに基づいてアラートを受け取ることができます。

このアラートは、指定したメトリックの値が、割り当てたしきい値を超えたときにトリガーされます。 アラートは条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。 

アラートがトリガーされたときに実行されるように構成できるアクションは次のとおりです。
* サービスの管理者/共同管理者に電子メール通知を送信する。
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す。

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します。
* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [コマンド ライン インターフェイス (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure Portal でメトリックのアラート ルールを作成する
1. [Azure Portal](https://portal.azure.com/) で、監視する Azure Database for PostgreSQL サーバーを選択します。

2. 次のように、サイドバーの **[監視]** セクションで、**[アラート ルール]** を選択します。

   ![アラート ルールを選択する](./media/howto-alert-on-metric/1-alert-rules.png)

3. **[メトリック アラートの追加]** (+ アイコン) を選択します。 

4. 以下のように、**[ルールの追加]** ページが開きます。  必要な情報を入力します。

   ![メトリック アラート フォームを追加する](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Setting | 説明  |
   |---------|---------|
   | Name | アラート ルールの名前を指定します。 この値はアラート通知メールで送信されます。 |
   | 説明 | アラート ルールの簡単な説明を入力します。 この値はアラート通知メールで送信されます。 |
   | アラート対象 | このアラートの対象となる**メトリック**を選択します。 |
   | サブスクリプション | このフィールドには Azure Database for PostgreSQL をホストするサブスクリプションが入力されています。 |
   | リソース グループ | このフィールドには Azure Database for PostgreSQL のリソース グループが入力されています。 |
   | リソース | このフィールドには Azure Database for PostgreSQL の名前が入力されています。 |
   | メトリック | アラートを発行する対象のメトリックを選択します  (例: **ストレージの割合**)。 |
   | 条件 | 比較するメトリックの条件を選択します  (例: **より大きい**)。 |
   | しきい値 | メトリックのしきい値 (例: 85 (パーセント))。 |
   | 期間 | アラートがトリガーされるまでにメトリック ルールが満たす必要がある期間  (例: **直近 30 分**)。 |

   この例では、アラートは直近 30 分で 85% を超えるストレージを対象とします。 ストレージが 30 分間で平均 85% を上回ると、アラートがトリガーされます。 最初のトリガーが発生した後、ストレージが 30 分間で平均 85% を下回ったときに、再度アラートがトリガーされます。

5. アラート ルールに適用する通知方法を選択します。 

   アラートが発生したときにサブスクリプションの管理者と共同管理者に電子メールが送信されるようにするには、**[所有者、共同作成者、閲覧者に電子メールを送信]** をオンにします。

   アラートが発生したときに、他のアドレスにも電子メールを送信して通知する場合は、**[追加する管理者の電子メール]** フィールドにそのアドレスを入力します。 複数の電子メール アドレスを入力する場合はセミコロンで区切ります- *email@contoso.com;email2@contoso.com*

   オプションで、アラートが発生したときに呼び出す webhook の有効な URI を **[webhook]** フィールドに入力します。

6. **[OK]** を選択してアラートを作成します。

   数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="manage-your-alerts"></a>アラートの管理
アラートを作成したら、それを選択して次のアクションを実行できます。

* このアラートに関連するメトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* アラート ルールを**編集**または**削除**する。
* アラートを**無効**にしてアラートを一時的に停止する、または**有効**にして通知の受け取りを再開する。

## <a name="next-steps"></a>次の手順
* [アラートでの webhook の構成](../azure-monitor/platform/alerts-webhooks.md)に関する詳細情報を確認します。
* [メトリック収集の概要](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 情報を入手して、サービスの可用性と応答性を確認します。
