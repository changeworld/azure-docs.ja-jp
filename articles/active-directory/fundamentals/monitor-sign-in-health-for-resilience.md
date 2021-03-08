---
title: Azure Active Directory で回復性のためにアプリケーションのサインインの正常性を監視する
description: クエリと通知を作成して、アプリケーションのサインインの正常性を監視します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/10/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad99c8d319a22f8b5388838b9d537de2f610478a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650993"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>回復性のためにアプリケーションのサインインの正常性を監視する

インフラストラクチャの回復性を向上させるには、重要なアプリケーションを対象にアプリケーションのサインインの正常性を監視する設定を行い、影響のあるインシデントが発生した場合にアラートを受け取るようにします。 この作業を簡単に行うために、サインインの正常性のブックを基にアラートを構成できます。 

このブックを使用すると、管理者はテナント内のアプリケーションに対する認証要求を監視できます。 次の主要な機能が用意されています。

* ほぼリアルタイムのデータを使用して、すべてまたは個別のアプリを監視するようにブックを構成します。

* 認証パターンが変わったときに通知するようにアラートを構成して、調査とアクションを実行できるようにします。

* 一定期間の、たとえばブックの既定の設定である週ごとなどの傾向を比較します。

> [!NOTE]
> 使用可能なすべてのブックとそれらを使用するための前提条件については、[レポートに Azure Monitor ブックを使用する方法に関するページ](../reports-monitoring/howto-use-azure-monitor-workbooks.md)を参照してください。

影響のあるイベントの発生時には、2 つのことが発生する可能性があります。

* ユーザーがサインインできないため、アプリケーションのサインイン回数が急激に減少する場合があります。

* サインイン失敗の数が増加するおそれがあります。 

この記事では、ユーザーのサインインの中断を監視するために、サインインの正常性ブックを設定する手順について説明します。

## <a name="prerequisites"></a>前提条件 

* Azure AD テナント。

* Azure AD テナントのグローバル管理者またはセキュリティ管理者のロールを持つユーザー。

* Azure Monitor ログにログを送信するための、Azure サブスクリプション内の Azure Log Analytics ワークスペース。 

   * [Log Analytics ワークスペースの作成](../../azure-monitor/logs/quick-create-workspace.md)方法をご確認ください

* Azure Monitor ログと統合された Azure AD ログ

   * [Azure AD サインイン ログを Azure Monitor Stream と統合する](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)方法を参照してください。

 

## <a name="configure-the-app-sign-in-health-workbook"></a>アプリのサインイン正常性ブックを構成する 

ブックにアクセスするには、**Azure portal** を開き、 **[Azure Active Directory]** を選択し、 **[ブック]** を選択します。

ブックは、[使用状況]、[条件付きアクセス]、および [トラブルシューティング] の下に表示されます。 アプリのサインインの正常性ブックは、[使用状況] セクションに表示されます。

ブックを使用すると、[最近変更されたブック] セクションに表示されることがあります。

![Azure portal のブック ギャラリーを示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


アプリのサインインの正常性ブックを使用すると、サインインの状況を視覚化できます。 

既定では、ブックには 2 つのグラフが表示されます。 これらのグラフは、現在アプリに起こっていることを 1 週間前の同じ期間と比較したものです。 青い線が現在、オレンジ色の線が前の週です。

![サインインの正常性グラフを示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**最初のグラフは、Hourly usage (number of successful users) つまり時間あたりの使用量 (成功したユーザーの数) です**。 現在の成功したユーザーの数を典型的な使用量の期間と比較すると、調査が必要な可能性のある使用量の低下を特定するのに役立ちます。 使用の成功率の低下は、失敗率では検出できないパフォーマンスと稼働率の問題を検出するのに役立ちます。 たとえば、ユーザーがサインインしようとしてアプリケーションに接続できなかった場合、失敗にはならず、使用量が低下するだけです。 このデータのサンプル クエリについては、次のセクションを参照してください。

2 番目のグラフは、Hourly failure rate (時間あたりの失敗率) です。 失敗率の急上昇は、認証メカニズムに問題があることを示している場合があります。 失敗率は、ユーザーが認証を試みることができた場合にのみ測定できます。 ユーザーがアクセス権を取得できなかった場合、失敗とは表示されません。

使用量または失敗率が指定したしきい値を超えたときに特定のグループに通知するアラートを構成できます。 このデータのサンプル クエリについては、次のセクションを参照してください。

 ## <a name="configure-the-query-and-alerts"></a>クエリとアラートを構成する

Azure Monitor でアラート ルールを作成し、保存済みのクエリまたはカスタム ログ検索を一定の間隔で自動的に実行できます。

次の手順に従って、グラフに反映されたクエリに基づいて電子メール アラートを作成します。 以下のサンプル スクリプトでは、次の場合に電子メール通知が送信されます。

* 前のセクションにある時間あたりの使用量のグラフのように、使用の成功率が 2 日前の同じ時間から 90% 低下した。 

* 前のセクションにある時間あたりの失敗率グラフのように、失敗率が 2 日前の同じ時間から 90% 増加した。 

 基になるクエリを構成し、アラートを設定するには、次の手順を行います。 このサンプル クエリを実際の構成の基礎として使用します。 このセクションの最後にクエリ構造の説明があります。

Azure Monitor を使用してログ アラートを作成、表示、管理する方法の詳細については、[ログ アラートの管理](../../azure-monitor/alerts/alerts-log.md)に関するページを参照してください。

 
1. ブックで、 **[編集]** を選択し、グラフの右側のすぐ上にある **[クエリ]** アイコンを選択します。   

   [![ブックの編集を示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   クエリ ログが開きます。

  [![クエリ ログを示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/query-log.png)](/media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. 新しい Kusto クエリ用に次のいずれかのサンプル スクリプトをコピーします。

**使用量の低下に関する Kusto クエリ**

```Kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(2d))..(now() - totimespan(2d)))

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersPriorWeek = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join

(

 lastWeek

)

on rn

| project TimeGenerated, users, usersPriorWeek, difference = abs(users - usersPriorWeek), max = max_of(users, usersPriorWeek)

| where (difference * 2.0) / max > 0.9

```

 

**失敗率の増加に関する Kusto クエリ**


```kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1 h)

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1 h) - totimespan(2d))..(ago(1h) - totimespan(2d)))

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRatePriorWeek = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join (lastWeek) on rn

| project TimeGenerated, failureRate, failureRatePriorWeek

| where abs(failureRate – failureRatePriorWeek) > **THRESHOLD VALUE**

```

3. ウィンドウにクエリを貼り付け、 **[実行]** を選択します。 次の画像に示されている完了メッセージと、そのメッセージの下に結果が表示されたことを確認します。

   [![クエリの実行結果を示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. クエリを強調表示し、[+ **新しいアラート ルール**] を選択します。 
 
   [![新しいアラート ルールの画面を示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. アラートの条件を構成します。 [条件] セクションで、**平均のカスタム ログ検索が "ロジックで定義された数" より大きい場合** を示すリンクを選択します。 [シグナル ロジックの構成] ウィンドウで、[アラート ロジック] までスクロールします。

   [![アラートの構成画面を示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **しきい値**: 0。 この値を使用すると、任意の結果に対してアラートが生成されます。

   * **期間 (分単位)** : 60 です。 この値にすると、1 時間ごとに確認されます

   * **頻度 (分単位)** : 60 です。 この値を使用して、直前の時間について評価期間を 1 時間に 1 回に設定します。

   * **[完了]** を選択します。

6. **[アクション]** セクションで、これらの設定を構成します。  

    [![[アラート ルールの作成] ページを示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * **[アクション]** で **[アクション グループの選択]** を選択し、アラートの通知を受け取るグループを追加します。

   * **[アクションをカスタマイズする]** で、 **[メール アラート]** を選択します。

   * **件名行** を追加します。

7. **[アラート ルールの詳細]** で、これらの設定を構成します。

   * わかりやすい名前と説明を追加します。

   * アラート追加先となる **リソース グループ** を選択します。

   * アラートの既定の **重要度** を選択します。

   * すぐに有効にする場合は **[作成時にアラート ルールを有効にする]** を、そうでない場合は **[アラートを表示しない]** を選択します。

8. **[アラート ルールの作成]** を選択します。

9. **[保存]** を選択し、クエリの名前を入力して、**カテゴリがアラートのクエリとして保存** します。 次に、もう一度 **[保存]** を選択します。  

   [![クエリの保存ボタンを示すスクリーンショット。](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)



### <a name="refine-your-queries-and-alerts"></a>クエリとアラートを調整する
効果が最大限になるようにクエリとアラートを変更できます。

* アラートは必ずテストしてください。

* 重要な通知を受け取るようにアラートの感度と頻度を変更します。 管理者は、受け取る数が多すぎるとアラートに対して鈍感になり、重要なことを見逃すおそれがあります。 

* 管理者の電子メール クライアントに着信するアラートの送信元のメール アドレスが、許可された送信者の一覧に追加されていることを確認します。 そうしないと、電子メール クライアントのスパム フィルターによって通知が表示されなくなるおそれがあります。 

* Azure Monitor のアラート クエリには、過去 48 時間の結果のみを含めることができます。 [これは仕様による現在の制限です](https://github.com/MicrosoftDocs/azure-docs/issues/22637)。

## <a name="create-processes-to-manage-alerts"></a>アラートを管理するプロセスを作成する

クエリとアラートを設定したら、アラートを管理するビジネス プロセスを作成します。

* だれが、いつブックを監視するか?
* アラートが生成されたとき、だれが調査するか?

* 通信のニーズはどのようなものか? だれが通信を作成し、それをだれが受信するか?

* 障害が発生した場合、どのようなビジネス プロセスをトリガーする必要があるか?

## <a name="next-steps"></a>次のステップ

[ブックの詳細情報](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

 

 

