---
title: "Microsoft Flow を使って Azure Application Insights のプロセスを自動化する"
description: "Microsoft Flow を利用し、Flow 対応の Application Insights コネクターを使って反復可能なプロセスを迅速に自動化する方法を説明します。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 7e17fde55097a8e852be33bac66940a05fdd9df6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow 対応のコネクターを使って Application Insights プロセスを自動化する

お使いのサービスが正常に機能しているかを確認するために、利用統計情報に対して同じクエリを繰り返し実行していませんか。 これらのクエリを自動化して傾向や異常を検出し、関連する独自のワークフローを構築する方法を確認しましょう。 必要なものは、Microsoft Flow 対応の Application Insights Connector (プレビュー) だけです。
この統合によって、一行のコードも記述せずに、膨大な数のプロセスを自動化できるようになりました。 Application Insights のアクションを使ってフローを作成した後、Application Insights Analytics クエリを自動的に実行します。 また、その他のアクションを追加することもできます。 フローによって、多数のアクションが利用可能になります。 たとえば、Flow を使って、自動的に電子メール通知を送信したり、Visual Studio Team Services のバグを作成したりできます。 また、Microsoft Flow に対応したコネクターで使用できる多数の[テンプレート](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights)の 1 つを使うこともできます。 これらのテンプレートによって、フローを作成するプロセスを高速化できます。 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="tutorial-for-creating-a-flow-for-application-insights"></a>Application Insights のフローを作成するためのチュートリアル

このチュートリアルでは、Analytics 自動クラスター アルゴリズムを使うフローを作成して、Web アプリケーションのデータの属性をグループ化する方法を説明します。 フローでは、電子メールで結果を自動送信します。 これは、Microsoft Flow と Application Insights Analytics を共に利用する方法の一例にすぎません。 

### <a name="step-1-create-a-flow"></a>手順1 : フローを作成する
1. http://flow.microsoft.com にサインインして、**[マイフロー]** を選択します。
2. **[フローを一から作成する]** をクリックします。

### <a name="step-2-create-a-trigger-for-your-flow"></a>手順 2: フローのトリガーを作成する
1. **[スケジュール]**、**[スケジュール - 繰り返し]** の順にクリックします。
2. **[頻度]** を [日] に、**[間隔]** を [1] に設定します。

![フローのトリガーのダイアログ](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-azure-application-insights-action"></a>手順 3: Azure Application Insights のアクションを追加する
1. **[新しいステップ]**、**[アクションの追加]** の順にクリックします。
2. Azure Application Insights を検索します。
3. [Azure Application Insights – Visualize Analytics query Preview]\(Azure Application Insights – Visualize Analytics クエリのプレビュー\) をクリックします。

![Analytics クエリの実行画面](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>手順 4: Application Insights リソースに接続する

**前提条件**

この手順を完了するには、お使いのリソースのアプリケーション ID と API キーが必要です。 以下の図に示すように、Azure ポータルから ID と キーを取得できます。

![Azure ポータルのアプリケーション ID](./media/app-insights-automate-with-flow/appid.png) 

- アプリケーション ID と API キーによる接続に名前を付与します。

![フロー接続画面](./media/app-insights-automate-with-flow/flow3.png)

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
![Analytics クエリの設定画面](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>手順 6: 電子メールを送信するフローを設定する

1. **[新しいステップ]**、**[アクションの追加]** の順にクリックします。
2. Office 365 Outlook を検索します。
3. [Office 365 Outlook – Send an email]\(Office 365 Outlook – 電子メールの送信\) をクリックします。
![[Office 365 Outlook] の選択内容](./media/app-insights-automate-with-flow/flow2b.png)

4. 電子メールのアクションに、次のデータを追加します。
 - 受信者の電子メール アドレスを指定する
 - 電子メールの件名を入力する
 - カーソルを **[本文]** フィールドに置いて、右に展開する動的なコンテンツのメニューから、**[本文]** を選択します。
 - **[詳細オプションを表示する]** をクリックします。

 ![Office 365 Outlook の設定](./media/app-insights-automate-with-flow/flow5.png)

5. 動的なコンテンツのメニューから、以下を実行します。
- **[添付ファイル名]** を選択する
- **[添付ファイルの内容]** を選択する
- **[Is HTML]\(HTML にする\)** フィールドで、**[はい]** を選択します。

![Office 365 の電子メール設定画面](./media/app-insights-automate-with-flow/flow7.png)
### <a name="step-7-save-and-test-your-flow"></a>手順 7: フローを保存してテストする
- フローに名前を付与し、**[フローの作成]** をクリックします。

![Microsoft Flow の作成画面](./media/app-insights-automate-with-flow/flow8.png)

トリガーがこのアクションを実行するまで待機するか、または、[必要に応じてトリガーを実行する](https://flow.microsoft.com/blog/run-now-and-six-more-services/)ことで、すぐにフローを実行できます。

フローを実行すると、電子メールの一覧に指定した受信者は、以下のような電子メールを受信します。

![電子メールのサンプル](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>次のステップ

- [Analytics クエリ](app-insights-analytics-using.md)の作成についての詳細を見る
- [Microsoft Flow](https://ms.flow.microsoft.com)についての詳細を見る



<!--Link references-->






