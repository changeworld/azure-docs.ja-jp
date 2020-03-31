---
title: Azure Application Insights のアラートの設定
description: Web アプリにおける長い応答時間、例外、パフォーマンスまたは使用状況のその他の変化について通知します。
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295078"
---
# <a name="set-alerts-in-application-insights"></a>Application Insights のアラートの設定

[Azure Application Insights][start] では、Web アプリのパフォーマンスまたは使用状況のメトリックの変化についてアラートを発行できます。 

Application Insights では、[さまざまなプラットフォーム][platforms]上でライブ アプリを監視して、パフォーマンスの問題の診断と使用パターンの把握を支援します。

アラートには、次のように複数の種類があります。

* [**メトリック アラート**](../../azure-monitor/platform/alerts-metric-overview.md)は、一定の期間に、メトリックが応答時間、例外の数、CPU 使用率、ページ ビューなどのしきい値を超えたときに通知します。
* [**ログ アラート**](../../azure-monitor/platform/alerts-unified-log.md)は、アラート シグナルがカスタムの Kusto クエリに基づくアラートを説明するために使用されます。
* [**Web テスト**][availability]では、サイトがインターネット上で使用できなくなったとき、または応答速度が低下したときに通知を行います。 詳細については、[こちら][availability]をご覧ください。
* [**プロアクティブ診断**](../../azure-monitor/app/proactive-diagnostics.md)は、通常と異なるパフォーマンス パターンについて通知するよう自動的に構成されます。

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>カスタム ログ検索を使用して例外アラートを設定する方法

このセクションでは、例外アラートに基づいてクエリを設定する方法を説明します。 この例では、過去 24 時間の失敗率が 10% を超える場合にアラートを表示します。

1. Azure portal の Application Insight リソースに移動します。
2. 左側の [構成] で、 **[アラート]** をクリックします。

    ![左側の [構成] で、[アラート] をクリックする](./media/alerts/1appinsightalert.png)

3. [アラート] タブの先頭にある **[新しいアラート ルール]** を選択します。

     ![[アラート] タブの先頭にある [新しいアラート ルール] をクリックする](./media/alerts/2createalert.png)

4. リソースは自動的に選択されます。 条件を設定するには、 **[条件の追加]** をクリックします。

    ![[条件の追加] をクリックする](./media/alerts/3addcondition.png)

5. [シグナル ロジックの構成] タブで、 **[カスタム ログ検索]** を選択します

    ![[カスタム ログ検索] をクリックする](./media/alerts/4customlogsearch.png)

6. [カスタム ログ検索] タブで、"検索クエリ" ボックスにクエリを入力します。 この例では、以下の Kusto クエリを使用します。
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![検索クエリ ボックスにクエリを入力する](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > 他の種類のクエリベースのアラートにもこれらの手順を適用できます。 Kusto クエリ言語の詳細については、この [Kusto の概要に関するドキュメント](https://docs.microsoft.com/azure/kusto/concepts/)またはこの [SQL から Kusto へのチート シート](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)に関するページを参照してください

7. [アラート ロジック] で、結果の数とメトリック測定のどちらに基づくかを選択します。 次に、条件 (より大きい、等しい、より小さい) としきい値を選択します。 これらの値を変更するときに、条件のプレビュー文が変わることに気付くかもしれません。 この例では、"等しい" を使用しています。

    ![[アラート ロジック] で、基準と条件で提供されるオプションから選択し、しきい値を入力する](./media/alerts/6alertlogic.png)

8. [評価基準] で、期間と頻度を設定します。 この期間は、上記のクエリの期間に対して設定した値と一致する必要があります。 次に、 **[完了]** をクリックします。

    ![下部で期間と頻度を設定し、[完了] をクリックする](./media/alerts/7evaluate.png)

9. これで、月額料金の見積もりで作成した条件が表示されます。 以下の [[アクション グループ]](../platform/action-groups.md) では、新しいグループを作成することも、既存のものを選択することもできます。 必要に応じて、アクションをカスタマイズできます。

    ![[アクション グループ] で選択ボタンまたは作成ボタンをクリックする](./media/alerts/8actiongroup.png)

10. 最後に、アラートの詳細 (アラート ルール名、説明、重大度) を追加します。 完了したら、下部にある **[アラート ルールの作成]** をクリックします。

    ![[アラート詳細] で、アラート ルール名を入力し、説明を記述して重大度を選ぶ](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>クラシック アラートのメール通知の登録を解除する方法

このセクションは、**従来の可用性アラート**、**従来の Application Insights のメトリック アラート**、**従来の障害異常アラート**に適用されます。

次のいずれかに該当する場合は、これらのクラシック アラートのメール通知を受信しています。

* 自分のメール アドレスが、アラート ルール設定の [通知メール受信者] フィールドの一覧に表示されている。

* サブスクリプションの特定の役割を持つユーザーに通知メールを送信するオプションが有効になっていて、その特定の Azure サブスクリプションの該当する役割を持っている。

![アラート通知のスクリーンショット](./media/alerts/alert-notification.png)

セキュリティとプライバシーをより細かく制御するには、 **[通知メールの受信者]** フィールドでクラシック アラートの通知メールの受信者を明示的に指定することが一般的に推奨されます。 特定の役割を持つすべてのユーザーに通知するオプションは、旧バージョンとの互換性のために提供されています。

特定のアラート ルールによって生成される通知メールの登録を解除するには、自分のメール アドレスを **[通知メールの受信者]** フィールドから削除します。

一覧に自分のメール アドレスが明示的に表示されていない場合は、特定の役割を持つすべてのメンバーに自動的に通知するオプションを無効にし、代わりに [通知メールの受信者] フィールドの一覧で、そのアラート ルールの通知を受信する必要があるすべてのユーザーのメール アドレスを指定することをお勧めします。

## <a name="who-receives-the-classic-alert-notifications"></a>(クラシック) アラート通知は誰が受け取りますか。

このセクションは、クラシック アラートにのみ適用され、目的の受信者だけが通知を受け取るように、アラート通知を最適化するために役立ちます。 [クラシック アラート](../platform/alerts-classic.overview.md)と新しいアラート エクスペリエンスの違いの詳細については、[アラートの概要の記事](../platform/alerts-overview.md)を参照してください。 新しいアラート エクスペリエンスのアラート通知を制御するには、[アクション グループ](../platform/action-groups.md)を使用します。

* クラシック アラート通知には、特定の受信者の使用をお勧めします。

* Application Insights のメトリック (可用性メトリックを含む) についてのアラートの場合、**一括/グループ** チェックボックス オプションが有効にされていれば、サブスクリプション内の所有者、共同作成者、または閲覧者ロールを持つユーザーに送信されます。 実際には、サブスクリプションの Application Insights リソースにアクセスできる _すべて_ のユーザーが範囲内になり、通知を受け取ります。

> [!NOTE]
> 現在、**一括/グループ** チェックボックス オプションを使用しており、それを無効にすると、変更を元に戻すことはできません。

ロールに基づいてユーザーに通知する必要がある場合は、新しいアラート エクスペリエンス/準リアルタイム アラートを使用します。 [アクション グループ](../platform/action-groups.md)を使用して、共同作成者/所有者/閲覧者のいずれかのロール (1 つのオプションとして組み合わされていない) を持つユーザーに対し、電子メール通知を構成することができます。

## <a name="automation"></a>Automation
* [PowerShell を使用したアラートの設定の自動化](../../azure-monitor/app/powershell-alerts.md)
* [webhook を使用したアラートへの対応の自動化](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>参照
* [可用性 Web テスト](../../azure-monitor/app/monitor-web-app-availability.md)
* [アラートの設定の自動化](../../azure-monitor/app/powershell-alerts.md)
* [プロアクティブ診断](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

