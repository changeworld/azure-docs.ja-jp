---
title: "仮想マシンの変更の監視 - Azure Event Grid と Logic Apps | Microsoft Doc"
description: "Azure Event Grid と Logic Apps を使用して、仮想マシン (VM) の構成の変更を確認します"
keywords: "ロジック アプリ, イベント グリッド, 仮想マシン, VM"
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 08/16/2017
ms.author: LADocs; estfan
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: a2ab8c882b324230088c12f6971207b32e23bd1d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---

# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Azure Event Grid と Logic Apps で仮想マシンの変更を監視する

特定のイベントが Azure リソースまたはサード パーティのリソースで発生したときに、自動[ロジック アプリ ワークフロー](../logic-apps/logic-apps-what-are-logic-apps.md)を開始できます。 こうしたリソースは、そのイベントを [Azure Event Grid](../event-grid/overview.md) に発行できます。 また、そのイベントは、イベント グリッドによって、エンドポイントとしてキュー、webhook、または [Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) を持つサブスクライバーにプッシュされます。 ロジック アプリはサブスクライバーとして、イベント グリッドからのイベントを待機してから、自動ワークフローを実行してタスクを実行できます。

たとえば、Azure Event Grid サービスを介して発行元がサブスクライバーに送信できるイベントをいくつか次に示します。

* リソースの作成、読み取り、更新、または削除。 たとえば、Azure サブスクリプションで料金を発生させ、課金内容に影響する可能性がある変更を監視できます。
* アプリによる特定のアクションの実行。
* Azure サブスクリプションのユーザーの追加または削除。
* キューでの新着メッセージ。

このチュートリアルでは、仮想マシンへの変更を監視して、その変更に関する電子メールを取得できるように、ロジック アプリを作成する方法を示します。 ロジック アプリに Azure リソースのイベント サブスクリプションがある場合、イベントは、そのリソースから、イベント グリッドを介してロジック アプリに渡されます。 このチュートリアルで作成できるロジック アプリを次に示します。

![概要 - イベント グリッドとロジック アプリによる仮想マシンの監視](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

## <a name="requirements"></a>必要条件

このチュートリアルには、次のものが必要です。

* 仮想マシン。 
[Azure Portal を使用して仮想マシンを作成する方法を説明します](../virtual-machines/windows/quick-create-portal.md)。 仮想マシンでイベントを発行する場合は、[他の操作を行う必要はありません](../event-grid/overview.md)。

* [Azure Logic Apps がサポートする電子メール プロバイダー](../connectors/apis-list.md) (Office 365 Outlook、Outlook.com、Gmail など) を利用できる電子メール アカウント。 このチュートリアルでは、Office 365 Outlook 電子メール アカウントを使用します。

> [!IMPORTANT]
> このチュートリアルではリソースを使用して、Azure サブスクリプションで料金を発生させるアクションを実行します。 チュートリアルが完了したら、料金を発生させたくないリソースは必ず無効にするか、削除してください。

## <a name="create-a-logic-app-that-monitors-events-through-an-event-grid"></a>イベント グリッドを介してイベントを監視するロジック アプリを作成する

最初に、イベント グリッド トリガーを持つロジック アプリを作成し、仮想マシンのリソース グループを監視できるように、そのトリガーを設定します。 

1. 次に示すように、[Azure Portal](https://portal.azure.com) で、Azure のメイン メニューから **[新規]** > **[エンタープライズ統合]** > **[Logic App]** の順に選択します。

   ![ロジック アプリを作成する](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

2. ロジック アプリを作成します。

   1. ロジック アプリの名前を指定します。

   2. ロジック アプリに使用する Azure サブスクリプション、リソース グループ、および場所を選択します。 

   3. 準備ができたら、**[ダッシュボードにピン留めする]**、**[作成]** の順に選択します。

      ![ロジック アプリの詳細を指定する](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

      これで、ロジック アプリの Azure リソースが作成されました。 
      Azure によってロジック アプリがデプロイされたら、すばやく開始できるように Logic Apps デザイナーによって一般的なパターンのテンプレートが表示されます。

      > [!NOTE] 
      > **[ダッシュボードにピン留めする]** を選択すると、ロジック アプリが Logic Apps デザイナーで自動的に開きます。 開かない場合は、手動でロジック アプリを見つけて開くことができます。

3. ここで、ロジック アプリ テンプレートを選択します。 **[テンプレート]** の **[空のロジック アプリ]** を選択すると、ロジック アプリを最初から作成できます。

   Logic Apps デザイナーには、ロジック アプリの開始に使用できる "[*コネクタ*](../connectors/apis-list.md)" および "[*トリガー*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)" のほか、タスクを実行するためにトリガーの後に追加できるアクションが表示されます。 
   トリガーは、ロジック アプリ インスタンスを作成し、ロジック アプリ ワークフローを開始するイベントです。 ロジック アプリには、最初の項目としてトリガーが必要です。

4. 検索ボックスに、フィルターとして「event grid」と入力します。 トリガーとして **[Azure Event Grid - On a resource event]\(Azure Event Grid - リソース イベントの発生時\)** を選択します。

   ![トリガーとして [Azure Event Grid - On a resource event]\(Azure Event Grid - リソース イベントの発生時\) を選択する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

5. このトリガーで、ロジック アプリが発行元リソースからイベントを受信できるように、次のイベント サブスクリプションの詳細を指定します。

   ![イベント サブスクリプションの詳細を指定する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | 設定  | Description  |
   | :------- | :----------- |
   | **サブスクリプション** | 発行元リソースの Azure サブスクリプション。 このチュートリアルでは、仮想マシンに使用する Azure サブスクリプションを選択します。 | 
   | **リソースの種類** | 発行元のリソースの種類。 このチュートリアルでは、リソース グループだけがロジック アプリで監視されるように **[Microsoft.Resources.resourceGroups]** を選択します。 |
   | **リソース名** | 発行元のリソースの名前。 このチュートリアルでは、仮想マシンのリソース グループの名前を選択します。 |
   | オプションの設定を指定する場合は、**[詳細オプションを表示する]** を選択します。 | * **プレフィックス フィルター**: このチュートリアルでは、この設定は空のままにします。 既定の動作はすべての値と一致します。    ただし、フィルターとしてプレフィックス文字列を指定できます。たとえば、特定のリソースのパスとパラメーターを指定できます。 <p>* **サフィックス フィルター**: このチュートリアルでは、この設定は空のままにします。 既定の動作はすべての値と一致します。 ただし、フィルターとしてサフィックス文字列を指定できます。たとえば、特定の種類のファイルが必要なときは、ファイル名拡張子を指定できます。<p>* **サブスクリプション名**: イベント サブスクリプションの名前を指定します。 |
   |||

   ロジック アプリ トリガーの例を次に示します。
   
   ![イベント グリッド トリガーの詳細の例](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

6. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。 ロジック アプリでアクションの詳細を折りたたんで非表示にするには、アクションのタイトル バーを選択します。

   ![ロジック アプリを保存する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

イベント グリッド トリガーを含むロジック アプリを保存すると、指定したリソースのイベント サブスクリプションが自動的に作成されます。 このため、リソースがイベントをイベント グリッドに発行したとき、イベント グリッドは、そのイベントをロジック アプリに自動的にプッシュします。 このイベントはロジック アプリをトリガーし、これにより次の手順で定義するワークフローのインスタンスが作成され、実行が開始されます。 

ロジック アプリはアクティブですが、アクションをワークフローに追加するまでは何も行いません。 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>仮想マシンの変更を確認する条件を追加する

特定のイベントが発生した場合にのみがロジック アプリが実行されるように指定するには、仮想マシンの "書き込み" 操作を確認する条件を追加します。 この条件が true の場合、ロジック アプリによって、更新された仮想マシンに関する詳細が電子メールで送信されます。

1. ロジック アプリ デザイナーのイベント グリッド トリガーで、**[新しいステップ]** > **[条件の追加]** の順に選択します。

   ![ロジック アプリに条件を追加する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-condition.png)

   ロジック アプリケーション デザイナーにより、条件 (true か false かに応じて従うアクション パスなど) がワークフローに追加されます。

2. 条件ボックスで、**[詳細設定モードで編集]** を選択します。
次の式を入力します。

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   この式は、イベント `body` で `operationName` プロパティが `Microsoft.Compute/virtualMachines/write` 操作の `data` オブジェクトをチェックします。 
   詳細については、「[Event Grid event schema (Event Grid イベント スキーマ)](../event-grid/event-schema.md)」を参照してください。

3. 次に、**[基本モードで編集]** を選択します。
条件の説明を入力するには、条件シェイプの**省略記号** (**...**) ボタンを選択し、**[名前の変更]** を選択します。

   この条件は次の例のようになります。

   ![ロジックのアプリの条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

4. ロジック アプリを保存し、

## <a name="send-email-when-your-virtual-machine-changes"></a>仮想マシンの変更時に電子メールを送信する

次は、指定した条件が true の場合に電子メールを受信するという "[*アクション*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)" を追加します。

1. 条件の下の、**[If true]\(true の場合\)** ボックスで、**[アクションの追加]** を選択します。

   ![条件が true の場合のアクションを追加する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. 検索ボックスに、フィルターとして「email」と入力します。 電子メール プロバイダーに基づいて、一致するコネクタを検索して選択します。 次に、コネクタに対して "電子メールの送信" アクションを選択します。 For example: 

   * たとえば、Azure 職場または学校アカウントの場合は、Office 365 Outlook コネクタを選択します。 

   * 個人用 Microsoft アカウントの場合は、Outlook.com コネクタを選択します。 

   * Gmail アカウントの場合は、Gmail コネクタを選択します。 

   このチュートリアルでは、Office 365 Outlook コネクタを使用して続行します。 
   別のプロバイダーを使用する場合、手順は変わりませんが、UI の表示が異なることがあります。 
   
   ワークフローは次のようになります。

   !["電子メールの送信" アクション](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

4. 電子メール プロバイダーに接続していない場合は、認証を求められた時点で、電子メール アカウントにサインインします。

5. 電子メールの詳細を入力します。 **動的なコンテンツ**の一覧が表示されたら、ワークフローで使用できるフィールドから選択できます。

   * **[宛先]** ボックスに、受信者の電子メール アドレスを入力します。 
   テスト目的のため、自分の電子メール アドレスを使用します。

   * **[件名]** ボックスと **[本文]** ボックスで、電子メールに含めるフィールドを選択します。 
   このチュートリアルでは、次を使用します。
   
     * **件名**: **[件名]** フィールドを選択し、電子メールに、更新されたリソースの名前が含まれるように指定します。

     * **本文**: **[トピック]**、**[イベント時間]**、および **[イベント ID]** フィールドを選択し、電子メールに、更新に対するリソース グループ名、イベントの timestamp、イベント ID を含めるように指定します。

   * 省略可能: 電子メールの説明を入力するには、電子メール シェイプで**省略記号** (**...**) ボタンを選択し、**[名前の変更]** を選択します。

   電子メール アクションは次の例のようになります。

   ![電子メールに含める出力を選択する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   > [!NOTE] 
   > 配列を表すフィールドを選択すると、その配列を参照するアクションに **[For each]** ループが自動的に追加されます。 このように、ロジック アプリは、そのアクションを各配列項目に対して実行します。

   完了すると、ロジック アプリは次の例のようになります。

   ![完成したロジック アプリ](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. ロジック アプリを保存し、 ロジック アプリで各アクションの詳細を折りたたんで非表示にするには、アクションのタイトル バーを選択します。

   ![ロジック アプリを保存する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   ロジック アプリはアクティブですが、仮想マシンが変更されるまで何も行いません。 
   ロジック アプリをテストするには、次のセクションに進みます。

## <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. 指定したイベントをロジック アプリが取得していることを確認するには、仮想マシンを更新します。 

   たとえば、Azure Portal で仮想マシンのサイズを変更するか、[Azure PowerShell で VM のサイズを変更](../virtual-machines/windows/resize-vm.md)できます。 

   しばらくすると、電子メールが届きます。 For example:

   ![仮想マシンの更新に関する電子メール](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. ロジック アプリの実行およびトリガー履歴を確認するには、ロジック アプリのメニューの **[概要]** を選択します。

これで、イベント グリッドでリソース イベントを監視し、そのイベントが発生したときに電子メールを送信するロジック アプリが作成、および実行されました。 また、プロセスを自動化するワークフローを簡単に作成し、システムとクラウド サービスを統合できる方法を学習しました。

> [!IMPORTANT]
> テストが完了したら、不要な料金を発生させるテスト リソースは必ず無効にしてください。 たとえば、ロジック アプリを無効にして、実行と電子メールの送信を停止します。 ロジック アプリのメニューで、**[概要]** を選択します。 ツール バーで、**[無効にする]** を選択します。
>
> ![ロジック アプリをオフにする](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>FAQ

**Q**: イベント グリッドおよびロジック アプリでは、他にどのような仮想マシンの監視タスクを実行できますか。 </br>
**A**: 他の構成変更を監視できます。次に例を示します。

* 仮想マシンによるロールベースのアクセス制御 (RBAC) 権限の取得。
* ネットワーク インターフェイス (NIC) のネットワーク セキュリティ グループ (NSG) への変更。
* 仮想マシンのディスクの追加または削除。
* 仮想マシン NIC へのパブリック IP アドレスの割り当て。

## <a name="next-steps"></a>次のステップ

* [Event Grid の概要](../event-grid/overview.md)
* [Event Grid の概念](../event-grid/concepts.md)
* [クイックスタート: Event Grid を使ったカスタム イベントの作成とルーティング](../event-grid/custom-event-quickstart.md)
* [Event Grid イベント スキーマ](../event-grid/event-schema.md)
* [定義済みテンプレートを使用したロジック アプリ ワークフローの作成](../logic-apps/logic-apps-use-logic-app-templates.md)
