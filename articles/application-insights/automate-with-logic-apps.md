---
title: "Azure Logic App を使って Azure Application Insights のプロセスを自動化します。"
description: "Application Insights コネクターをお使いの Azure Logic App に追加することで、反復可能なプロセスを迅速に自動化する方法を説明します。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 571a76253fa62a89f325e0c295e9a7e98e201079
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-an-azure-logic-app"></a>Azure Logic App を使って Application Insights を自動化する

お使いのサービスが正常に機能しているかを確認するために、利用統計情報に対して同じクエリを繰り返し実行していませんか。 これらのクエリを自動化して傾向や異常を検出し、関連する独自のワークフローを構築する方法を確認しましょう。 必要なものは、Azure Logic Apps 対応の Application Insights Connector (プレビュー) だけです。
この統合によって、一行のコードも記述せずに、膨大な数のプロセスを自動化できるようになりました。 Application Insights コネクターを使ってロジック アプリを作成し、どの Application Insights プロセスでも迅速に自動化できます。 また、その他のアクションを追加することもできます。 Logic Apps によって、多数のアクションが可能になります。 たとえば、ロジック アプリの一部として、自動的に電子メール通知を送信したり、Visual Studio Team Services のバグを作成したりできます。 また、Logic Apps で使用できる多数の[テンプレート](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) の 1 つを使うこともできます。 これらのテンプレートによって、Logic Apps を作成するプロセスを高速化できます。 

## <a name="tutorial-for-creating-an-azure-logic-app-for-application-insights"></a>Application Insights の Azure Logic App を作成するためのチュートリアル

このチュートリアルでは、Analytics 自動クラスター アルゴリズムを使う Azure Logic App を作成して、Web アプリケーションのデータの属性をグループ化する方法を説明します。 フローでは、電子メールで結果を自動送信します。 これは、Application Insights Analytics と Azure Logic Apps を一緒に利用する方法の一例にすぎません。 

### <a name="step-1-create-a-logic-app"></a>手順 1: ロジック アプリを作成する
1. https://portal.azure.com にサインインします。
2. [新規] の [Web + モバイル] メニューから新しいロジック アプリを作成します。

![新しいロジック アプリの画面](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>手順 2: ロジック アプリのトリガーを作成する
1.  ロジック アプリ デザイナーで、[一般的なトリガーで開始する] の下の [繰り返し] を選択します。
2.  [頻度] を [日] に、[間隔] を 1 に設定します。

![ロジック アプリ トリガーのダイアログ](./media/automate-with-logic-apps/logicapp2.png)

![ロジック アプリの頻度のダイアログ](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-azure-application-insights-action"></a>手順 3: Azure Application Insights のアクションを追加する
1. **[新しいステップ]**、**[アクションの追加]** の順にクリックします。
2. Azure Application Insights を検索します。
3. [Azure Application Insights – Visualize Analytics query Preview]\(Azure Application Insights – Visualize Analytics クエリのプレビュー\) をクリックします。

![Analytics クエリの実行画面](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>手順 4: Application Insights リソースに接続する

**前提条件**

この手順を完了するには、お使いのリソースのアプリケーション ID と API キーが必要です。 以下の図に示すように、Azure ポータルから ID と キーを取得できます。

![Azure ポータルのアプリケーション ID](./media/automate-with-logic-apps/appid.png) 

- アプリケーション ID と API キーによる接続に名前を付与します。

![フロー接続画面](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>手順 5: Analytics クエリとグラフの種類を指定する
この例では、前日に失敗したリクエストを選択して、処理の一環として発生した例外と関連付けています。 operation_Id 識別子に基づいて、Analytics によって関連付けられます。 その後、自動クラスター アルゴリズムを使って、クエリによる結果のセグメント化が行われます。 独自のクエリを作成するときは、クエリをフローに追加する前に、必ず Analytics で正常に動作していることを確認してください。

- 次の Analytics クエリを追加して、グラフの種類として [Html の表] を選択します。 

```
requests
| where timestamp > ago(1d)
| where success == "False"
| project name, operation_Id
| join ( exceptions
    | project problemId, outerMessage, operation_Id
) on operation_Id
| evaluate autocluster()
```
![Analytics クエリの設定画面](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-app-to-send-email"></a>手順 6: 電子メールを送信するようにアプリを設定する

1. **[新しいステップ]**、**[アクションの追加]** の順にクリックします。
2. Office 365 Outlook を検索します。
3. [Office 365 Outlook – Send an email]\(Office 365 Outlook – 電子メールの送信\) をクリックします。
![[Office 365 Outlook] の選択内容](./media/automate-with-logic-apps/flow2b.png)

4. 電子メールのアクションに、次のデータを追加します。
 - 受信者の電子メール アドレスを指定する
 - 電子メールの件名を入力する
 - カーソルを **[本文]** フィールドに置いて、右に展開する動的なコンテンツのメニューから、**[本文]** を選択します。
 - **[詳細オプションを表示する]** をクリックします。

 ![Office 365 Outlook の設定](./media/automate-with-logic-apps/flow5.png)

5. 動的なコンテンツのメニューから、以下を実行します。
- **[添付ファイル名]** を選択する
- **[添付ファイルの内容]** を選択する
- **[Is HTML]\(HTML にする\)** フィールドで、**[はい]** を選択します。

![Office 365 の電子メール設定画面](./media/automate-with-logic-apps/flow7.png)
### <a name="step-7-save-and-test-your-logic-app"></a>手順 7: ロジック アプリを保存してテストする
1. **[保存]** をクリックして変更を保存します。
1. トリガーがロジック アプリを実行するまで待機するか、または、**[実行]** をクリックしてすぐに実行できます。

![ロジック アプリの作成画面](./media/automate-with-logic-apps/step7.png)

作成したロジック アプリを実行すると、電子メールの一覧に指定した受信者は、以下のような電子メールを受信します。

![ロジック アプリの電子メール](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>次のステップ

- [Analytics クエリ](app-insights-analytics-using.md)の作成についての詳細を見る
- [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) についての詳細を見る



<!--Link references-->






