---
title: クロスプラットフォーム Azure CLI を使用して Azure サービスのクラシック アラートを作成する | Microsoft Docs
description: 指定した条件が満たされたときに、電子メール、通知、または Websites URL (webhook) の呼び出し、またはオートメーションをトリガーします。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287109"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>クロスプラットフォームの Azure CLI を使用して、Azure Monitor で Azure サービスのクラシック メトリック アラートを作成する 

> [!div class="op_single_selector"]
> * [ポータル](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> この記事では、古いクラシック メトリック アラートの作成方法について説明します。 Azure Monitor では、[新しい、より優れたメトリック アラート](monitoring-near-real-time-metric-alerts.md)がサポートされるようになりました。 これらのアラートでは複数のメトリックを監視でき、次元メトリックのアラートが許可されます。 新しいメトリック アラートの Azure CLI サポートはまもなく提供されます。
>
>

この記事では、クロスプラットフォーム コマンド ライン インターフェイス (Azure CLI) を使用して Azure クラシック メトリック アラートを設定する方法について説明します。

> [!NOTE]
> Azure Monitor は、2016 年 9 月 25 日まで "Azure Insights" と呼ばれていたサービスの新しい名前です。 ただし、名前空間と、ここで説明しているコマンドには、引き続き "insights" という語が含まれています。

Azure サービスのメトリックに基づいて、または Azure で発生したイベントに基づいてアラートを受け取ることができます。

* **メトリック値**: アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、その条件を満たさなくなったときの両方でトリガーされます。    

* **アクティビティ ログ イベント**: アラートは*すべて*のイベントに対して、または特定のイベントが発生したときにトリガーされます。 アクティビティ ログの詳細については、「[アクティビティ ログ アラート (クラシック) の作成](monitoring-activity-log-alerts.md)」を参照してください。 

クラシック メトリック アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービスの管理者/共同管理者に電子メール通知を送信する。 
* 指定した電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す。
* Azure Runbook の実行を開始する (現時点では Azure portal からのみ)。

クラシック メトリック アラート ルールを構成したり、その情報を取得したりするには、以下を使用します。 

* [Azure ポータル](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

コマンドの末尾に **-help** を追加して入力すると、そのコマンドのヘルプを確認することもできます。 たとえば次のようになります。 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Azure CLI を使用してアラート ルールを作成する
1. 必須コンポーネントをインストールしたら、Azure にサインインします。 初めて使用するコマンドについては、[Azure Monitor CLI のサンプル](insights-cli-samples.md)に関するページを参照してください。 これらのコマンドは、サインイン、使用しているサブスクリプションの表示、Azure Monitor コマンドを実行する準備に役立ちます。

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. リソース グループの既存のルールを一覧表示するには、次の形式を使用します。 

   **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. ルールを作成するには、最初に重要な情報をいくつか入手する必要があります。
    * アラートを設定するリソースの**リソース ID**。
    * そのリソースに使用できる**メトリック定義**。

     リソース ID は、Azure portal を使用して取得できます。 リソースが既に作成されている場合は、ポータルでそのリソースを選択し、 次のブレードで **[設定]** セクションの **[プロパティ]** を選択します。 次のブレードに "**リソース ID**" フィールドがあります。 
     
     また、[Azure Resource Explorer](https://resources.azure.com/) を使用してリソース ID を取得することもできます。

     Web アプリのリソース ID の例を次に示します。

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     この例のリソースで使用できるメトリック、およびそのメトリックの単位の一覧を取得するには、次の Azure CLI コマンドを使用します。  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* は、利用可能な測定の粒度 (1 分間隔) です。 使用する粒度によって、使用できるメトリック オプションは異なります。
     
4. メトリック ベースのアラート ルールを作成するには、次の形式のコマンドを使用します。

    **azure insights alerts rule metric set** *[オプション] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    次の例では、Web サイト リソースにアラートを設定します。 アラートは、5 分間を通してすべてのトラフィックを受け取り続けたときにトリガーされ、その後 5 分間トラフィックを受け取らなくなった時点で再度トリガーされます。

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. クラシック メトリック アラートが発生したときに webhook を作成するか、電子メールを送信するには、最初に電子メールまたは webhook を作成します。 その後すぐに、ルールを作成します。 既に作成されているルールに webhook または電子メールを関連付けることはできません。

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. アラートが正しく作成されていることを確かめるには、個別のルールを確認します。

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. ルールを削除するには、次の形式のコマンドを使用します。

    **insights alerts rule delete** [オプション] &lt;resourceGroup&gt; &lt;ruleName&gt;

    この記事で作成したルールは、次のコマンドで削除されます。

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>次の手順
* 収集および監視できる情報の種類など、[Azure での監視の概要](monitoring-overview.md)情報を入手します。
* [アラートでの webhook の構成](insights-webhooks-alerts.md)に関する詳細情報を確認します。
* [アクティビティ ログ イベントに対するアラートの構成](monitoring-activity-log-alerts.md)に関する詳細情報を確認します。
* [Azure Automation Runbooks](../automation/automation-starting-a-runbook.md) の詳細情報を確認します。
* [診断ログ収集の概要](monitoring-overview-of-diagnostic-logs.md) 情報を入手して、サービスに関する詳細な頻度の高いメトリックを収集します。
* [メトリック収集の概要](insights-how-to-customize-monitoring.md) 情報を入手して、サービスの可用性と応答性を確認します。
