---
title: Azure portal でシングルテナント Azure Logic Apps (Standard) を使用してワークフローを作成する
description: Azure portal でシングルテナント Azure Logic Apps (Standard) を使用して、アプリ、データ、サービス、システムを統合する自動化されたワークフローを作成します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/05/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 72e869b1439e5fd9bcb77af57bd4e0d3d8f6a677
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324804"
---
# <a name="create-an-integration-workflow-with-single-tenant-azure-logic-apps-standard-in-the-azure-portal"></a>Azure portal でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する

この記事では、**ロジック アプリ (Standard)** のリソースの種類と Azure portal を使用して、"*シングル テナント*" Azure Logic Apps 環境で実行される自動化された統合ワークフローの例を作成する方法について説明します。 この種類のリソースは、[ステートフル ワークフローおよびステートレス ワークフロー](single-tenant-overview-compare.md#stateful-stateless)を複数ホストすることができます。 また、同じロジック アプリとテナントのワークフローは、再設計された Azure Logic Apps ランタイムと同じプロセスで実行されるため、同じリソースを共有し、パフォーマンスが向上します。 シングルテナント Azure Logic Apps オファリングの詳細については、[シングルテナント、マルチテナント、統合サービス環境の比較](single-tenant-overview-compare.md)に関するページを参照してください。

このワークフロー例はクラウドベースであり、ステップは 2 つだけですが、クラウド、オンプレミス、ハイブリッド環境全体でさまざまなアプリ、データ、サービス、システムを接続できる数百の操作からワークフローを作成できます。 このワークフローの例は、組み込みの Request トリガーから始まり、Office 365 Outlook アクションが続きます。 このトリガーは、ワークフローの呼び出し可能なエンドポイントを作成し、任意の呼び出し元からの受信 HTTPS 要求を待機します。 トリガーが要求を受信して起動すると、次のアクションは、トリガーから選択した出力と共に、指定したメール アドレスに電子メールを送信することで実行されます。

> [!TIP]
> Office 365 アカウントをお持ちでない場合は、電子メール アカウントからメッセージを送信できる他の使用可能なアクション (Outlook.com など) を使用できます。
>
> 代わりに Visual Studio Code でこのワークフローの例を作成するには、[シングル テナントの Azure Logic Apps と Visual Studio Code を使用して統合ワークフローを作成](create-single-tenant-workflows-visual-studio-code.md)するページの手順に従います。 
> どちらのオプションも、同じ種類の環境でロジック アプリ ワークフローを開発、実行、デプロイする機能を提供します。 
> ただし、Visual Studio Code を使用すると、ご使用の開発環境でワークフローを "*ローカル*" で開発、テスト、実行できます。

![Azure portal と "ロジック アプリ (Standard)" リソース用のワークフロー デザイナーを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/azure-portal-logic-apps-overview.png)

作業を進めていくと、次の高レベルのタスクが完了します。

* 新しいロジック アプリ リソースを作成し、空の "[*ステートフル*](single-tenant-overview-compare.md#stateful-stateless)" ワークフローを追加します。
* トリガーとアクションを追加します。
* ワークフローの実行をトリガーします。
* ワークフローの実行履歴とトリガー履歴を表示します。
* デプロイの後で、Application Insights を有効にするか開きます。
* ステートレス ワークフローの実行履歴を有効にします。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* [Azure ストレージ アカウント](../storage/common/storage-account-overview.md)。 お持ちでない場合は、事前に、またはロジック アプリの作成時にストレージ アカウントを作成できます。

  > [!NOTE]
  > **ロジック アプリ (Standard)** のリソースの種類は Azure Functions によって実行され、[関数アプリに似たストレージ要件](../azure-functions/storage-considerations.md)があります。 
  > [ステートフル ワークフロー](single-tenant-overview-compare.md#stateful-stateless)は、スケジュールでのキューの使用や、テーブルや BLOB へのワークフローの状態の格納など、ストレージ トランザクションを実行します。 これらのトランザクションには、[ストレージの料金](https://azure.microsoft.com/pricing/details/storage/)がかかります。 ステートフル ワークフローによって外部ストレージにデータが格納される方法の詳細については、「[ステートフルとステートレスのワークフロー](single-tenant-overview-compare.md#stateful-stateless)」をご覧ください。

* この記事のワークフローの例と同じものを作成するには、Microsoft の職場または学校アカウントを使用してサインインする Office 365 Outlook の電子メール アカウントが必要です。

  [別の電子メール コネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors) (Outlook.com など) を選択した場合でも、例に従うことができ、全般的な手順は同じです。 ただし、オプションはいくつかの点で異なる場合があります。 たとえば、Outlook.com コネクタを使用する場合は、代わりに個人用 Microsoft アカウントを使用してサインインします。

* この記事のワークフローの例をテストするには、Request トリガーによって作成されたエンドポイントに呼び出しを送信できるツールが必要です。 このようなツールがない場合は、[Postman](https://www.postman.com/downloads/) をダウンロードしてインストールし、使用することができます。

* [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用をサポートする設定を使用してロジック アプリ リソースを作成する場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。 ロジック アプリを作成するとき、またはデプロイの後に、それを行うことができます。 Application Insights のインスタンスを用意する必要がありますが、このリソースは、[事前に](../azure-monitor/app/create-workspace-resource.md)、ロジック アプリを作成するときに、またはデプロイの後で、作成することができます。

* **Logic App (Standard)** リソースを [App Service Environment v3 (ASEv3)](../app-service/environment/overview.md) にデプロイするには、まずこの環境リソースを作成する必要があります。 ロジック アプリ リソースを作成するときに、配置場所としてこの環境を選択できます。 詳細については、「[リソースの種類と環境](single-tenant-overview-compare.md#resource-environment-differences)」と「[App Service Environment を作成する](../app-service/environment/creation.md)」を参照してください。

<a name="create-logic-app-resource"></a>

## <a name="create-the-logic-app-resource"></a>ロジック アプリ リソースを作成する

1. Azure アカウントの資格情報で [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の検索ボックスに「`logic apps`」と入力し、 **[ロジック アプリ]** を選択します。

   ![Azure portal の検索ボックスに "ロジック アプリ" という検索用語が入力され、[ロジック アプリ (Standard)] グループが選択されているスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/find-logic-app-resource-template.png)

1. **[ロジック アプリ]** ページで **[追加]** を選択します。

1. **[ロジック アプリの作成]** ページの **[基本]** タブで、ロジック アプリ リソースに関する次の情報を入力します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | ロジック アプリに使用する Azure サブスクリプション。 |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | ロジック アプリと関連リソースを作成する Azure リソース グループ。 このリソース名は、リージョン間で一意である必要があり、文字、数字、ハイフン ( **-** )、アンダースコア ( **_** )、かっこ ( **()** )、ピリオド ( **.** ) のみを含めることができます。 <p><p>この例では、`Fabrikam-Workflows-RG` という名前のリソース グループを作成します。 |
   | **Type** | はい | **Standard** | このロジック アプリ リソースの種類は、シングルテナント Azure Logic Apps 環境で実行され、[Standard 使用、課金、価格モデル](logic-apps-pricing.md#standard-pricing)を使用します。 |
   | **ロジック アプリ名** | はい | <*ロジック アプリ名*> | ロジック アプリに使用する名前。 このリソース名は、リージョン間で一意である必要があり、文字、数字、ハイフン ( **-** )、アンダースコア ( **_** )、かっこ ( **()** )、ピリオド ( **.** ) のみを含めることができます。 <p><p>この例では、`Fabrikam-Workflows` という名前のロジック アプリを作成します。 <p><p>**注**: ロジック アプリの名前には自動的にサフィックス `.azurewebsites.net` が付けられます。これは、**ロジック アプリ (Standard)** リソースは、Azure Functions の機能拡張モデルを使用し、Azure Functions ランタイムの拡張機能としてホストされているシングルテナント Azure Logic Apps ランタイムで実行されるためです。 Azure Functions では、同じアプリの名前付け規則が使用されます。 |
   | **発行** | はい | <*デプロイ環境*> | ロジック アプリのデプロイ先。 既定では、シングル テナント Azure Logic Apps にデプロイするための **ワークフロー** が選択されています。 Azure により、最初のワークフローを追加する必要がある空のロジック アプリ リソースが作成されます。 <p><p>**注**: 現時点では、**Docker コンテナー** オプションを使用するには Azure Arc enabled Kubernetes クラスター上に [*カスタムの場所*](../azure-arc/kubernetes/conceptual-custom-locations.md) が必要です。これは、[Azure Arc enabled Logic Apps (プレビュー)](azure-arc-enabled-logic-apps-overview.md) で使用できます。 ロジック アプリのリソースの場所、カスタムの場所、クラスターはすべて同じである必要があります。 |
   | **[リージョン]** | はい | <*Azure-region*> | リソース グループやリソースを作成する際に使用する場所です。 この例では、サンプル ロジック アプリを Azure にデプロイし、**米国西部** を使用します。 <p>- **[Docker コンテナー]** を選択した場合は、カスタムの場所を選択します。 <p>- あらかじめ存在している必要がある [ASEv3](../app-service/environment/overview.md) リソースにデプロイするには、**[リージョン]** の一覧からその環境リソースを選択します。 |
   |||||

   次の例は、 **[ロジックアプリの作成 (標準)]** ページを示しています。

   ![Azure portal と [ロジック アプリの作成] ページが示されているスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/create-logic-app-resource-portal.png)

1. **[ホスティング]** タブで、ロジック アプリに使用するストレージ ソリューションとホスティング プランに関する情報を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **ストレージの種類** | はい | - **SQL と Azure Storage** <br>- **Azure Storage** | ワークフロー関連の成果物およびデータに使用するストレージの種類。 <p><p>- Azure のみにデプロイする場合は、 **[Azure Storage]** を選択します。 <p><p>- プライマリ ストレージとして SQL を使用し、セカンダリ ストレージとして Azure Storage を使用するには、 **[SQL と Azure Storage]** を選び、「[シングルテナント Azure Logic Apps で Standard ロジック アプリの SQL データベース ストレージを設定する](set-up-sql-db-storage-single-tenant-standard-workflows.md)」を参照してください。 <p><p>**注**: Azure リージョンにデプロイする場合は、引き続き Azure Storage アカウントが必要です。これは、Azure Logic Apps プラットフォームでロジック アプリの構成の 1 回限りのホスティングを完了するために使用されます。 進行中のワークフローの状態、実行履歴、およびその他のランタイム成果物は、SQL データベースに格納されます。 <p><p>Azure Arc クラスターでホストされているカスタムの場所へのデプロイでは、ストレージ プロバイダーとして SQL のみが必要です。 |
   | **ストレージ アカウント** | はい | <*Azure-storage-account-name*> | ストレージ トランザクションに使用する [Azure ストレージ アカウント](../storage/common/storage-account-overview.md)。 <p><p>このリソース名は、リージョン間で一意であり、数字と小文字のみを含む 3 から 24 文字である必要があります。 既存のアカウントを選択するか、新しいアカウントを作成します。 <p><p>この例では、`fabrikamstorageacct` という名前のストレージ アカウントを作成します。 |
   | **[プランの種類]** | はい | <*hosting-plan*> | ロジック アプリのデプロイに使用するホスティング プラン。 <p><p>詳細については、「[ホスティング プランと価格レベル](logic-apps-pricing.md#standard-pricing)」を参照してください。 |
   | **Windows プラン** | はい | <*プラン名*> | 使用するプラン名。 既存のプラン名を選択するか、新しいプランの名前を指定します。 <p><p>この例では、 `Fabrikam-Service-Plan`という名前を使用しています。 |
   | **SKU とサイズ** | はい | <*価格レベル*> | ロジック アプリに使用する[価格レベル](../app-service/overview-hosting-plans.md)。 選択した内容は、ロジック アプリとワークフローで使用する料金、コンピューティング、メモリ、およびストレージに影響します。 <p><p>既定の価格レベルを変更するには、 **[サイズの変更]** を選択します。 その後、必要なワークロードに基づいて、他の価格レベルを選択できます。 <p><p>詳細については、「[ホスティング プランと価格レベル](logic-apps-pricing.md#standard-pricing)」を参照してください。 |
   |||||

1. 次に、作成とデプロイの設定で [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用がサポートされている場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。

   1. **[監視]** タブの **[Application Insights]** で、 **[Application Insights を有効にする]** を **[はい]** に設定します (まだ選択されていない場合)。

   1. **Application Insights** の設定で、Application Insights の既存のインスタンスを選択するか、新しいインスタンスを作成する場合は、 **[新規作成]** を選択して、使用する名前を指定します。

1. Azure によってロジック アプリの設定が検証されたら、 **[確認および作成]** タブで、 **[作成]** を選択します。次に例を示します。

   ![Azure portal と新しいロジック アプリ リソースの設定を示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > この手順の後に検証エラーが発生した場合は、エラーの詳細を開いて確認します。 たとえば、選択したリージョンが、作成しようとしているリソースのクォータに達する場合、別のリージョンの試行が必要になることがあります。

   Azure でデプロイが完了すると、ロジック アプリは自動的に有効になり、実行中になりますが、リソースが空で、まだワークフローを追加していないため、何も実行されません。

1. デプロイ完了ページで **[リソースに移動]** を選択して、空のワークフローを追加できるようにします。

   ![Azure portal と完了したデプロイを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>空のワークフローを追加する

空のロジック アプリ リソースを作成したら、最初のワークフローを追加する必要があります。

1. Azure でリソースが開かれたら、ロジック アプリのメニューで **[ワークフロー]** を選択します。 **[ワークフロー]** ツール バーで、 **[追加]** を選択します。

   ![ロジック アプリ リソースのメニューで [ワークフロー] が選択され、ツール バーで [追加] が選択されているスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. **[新しいワークフロー]** ペインが開いたら、ワークフローの名前を指定し、[ **[ステートフル]** または **[ステートレス]**](single-tenant-overview-compare.md#stateful-stateless) いずれかの状態の種類を選択します。 完了したら **[作成]** を選択します。

   この例では、`Fabrikam-Stateful-Workflow` という名前の空のステートフル ワークフローを追加します。 ワークフローは既定で有効になりますが、トリガーとアクションを追加するまでは何も行われません。

   ![新しく追加された空のステートフル ワークフロー "Fabrikam-Stateful-Workflow" を示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. 次に、トリガーとアクションを追加できるように、デザイナーで空のワークフローを開きます。

   1. ワークフローの一覧から、空のワークフローを選択します。

   1. ワークフローのメニューの **[開発者]** で、 **[デザイナー]** を選択します。

      デザイナー画面では **[操作を選択してください]** プロンプトが既に表示されて既定で選択されているので、 **[トリガーの追加]** ペインも開いた状態で表示されます。

      ![デザイナー画面で [操作を選択してください] が選択されている、開かれたワークフロー デザイナーを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>トリガーとアクションを追加する

この例では、次のような手順のワークフローを作成します。

* 組み込みの [要求トリガー](../connectors/connectors-native-reqres.md)である **[HTTP 要求の受信時]** 。入ってきた呼び出しまたは要求を受信し、他のサービスやロジック アプリで呼び出せるエンドポイントを作成します。

* [Office 365 Outlook アクション](../connectors/connectors-create-api-office365-outlook.md) **[メールの送信]** 。

### <a name="add-the-request-trigger"></a>要求トリガーを追加する

空のワークフローにトリガーを追加する前に、ワークフロー デザイナーが開いていて、デザイナー画面で **[操作を選択してください]** プロンプトが選択されていることを確認します。

1. デザイナー画面の横にある **[トリガーの追加]** ペインの **[操作を選択してください]** 検索ボックスの下で、 **[組み込み]** タブが選択されていることを確認します。 このタブには、Azure Logic Apps でネイティブに実行されるトリガーが表示されます。

1. **[操作の選択]** 検索ボックスに「`when a http request`」と入力し、 **[HTTP 要求の受信時]** という名前の組み込みの要求トリガーを選択します。

   ![デザイナーと、[HTTP 要求の受信時] トリガーが選択された **[トリガーの追加]** ウィンドウを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/find-request-trigger.png)

   トリガーがデザイナーに表示されると、トリガーの詳細ウィンドウが開き、トリガーのプロパティ、設定、およびその他のアクションが表示されます。

   ![[HTTP 要求の受信時] トリガーが選択されたデザイナーが表示され、トリガー詳細ウィンドウが開いたスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > 詳細ウィンドウが表示されない場合は、デザイナーでトリガーが選択されていることを確認します。

1. デザイナーから項目を削除する必要がある場合は、[デザイナーからの項目の削除に関するこちらの手順のようにします](#delete-from-designer)。

1. 作業を保存するには、デザイナーのツール バーで、 **[保存]** を選択します。

   ワークフローを初めて保存すると、そのワークフローが Request トリガーで開始されるときに、Logic Apps サービスによって、Request トリガーによって作成されるエンドポイントの URL が自動的に生成されます。 後でワークフローをテストするときに、この URL に要求を送信すると、トリガーが起動され、ワークフローの実行が開始されます。

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook アクションを追加する

1. デザイナーで、追加したトリガーの下のプラス記号 ( **+** ) > **[アクションの追加]** を選択します。

   デザイナーに **[操作を選択してください]** というプロンプトが表示され、次のアクションを選択できるように **[アクションの追加]** ペインが再び表示されます。

   > [!NOTE]
   > **[アクションの追加]** ペインに "Cannot read property 'filter' of undefined" (未定義のプロパティ 'filter' を読み取ることができない) という内容のエラー メッセージが表示される場合は、ワークフローを保存し、ページを再度読み込み、ワークフローを再度開いてから、もう一度やり直します。

1. **[アクションの追加]** ペインの **[操作を選択してください]** 検索ボックスの下で、 **[Azure]** を選択します。 このタブには、Azure でホスティングされている使用可能なマネージド コネクタが表示されます。

   > [!NOTE]
   > **[アクションの追加]** ペインに `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` (アクセス トークンの有効期限 '{token-expiration-date-time}' (UTC) が現在の時間 '{current-date-time}' (UTC) より前です) というエラー メッセージが表示される場合は、ワークフローを保存し、ページを再度読み込み、ワークフローを再度開いてから、アクションをもう一度追加してみます。

   この例では、 **[メールの送信 (V2)]** という名前の Office 365 Outlook アクションを使用します。

   ![デザイナーが表示され、**[アクションの追加]** ペインで Office 365 Outlook の [メールの送信] アクションが選択されたスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/find-send-email-action.png)

1. アクションの詳細ペインの **[接続の作成]** タブで **[サインイン]** を選択して、メール アカウントへの接続を作成できるようにします。

   ![デザイナーと、[サインイン] が選択された **[メールの送信 (V2)]** 詳細ペインを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/send-email-action-sign-in.png)

1. メール アカウントへのアクセスを求めるダイアログが表示されたら、アカウントの資格情報でサインインします。

   > [!NOTE]
   > `Failed with error: 'The browser is closed.'. Please sign in again` (エラーで失敗: ブラウザーが開かれていません。再度サインインしてください) という内容のエラー メッセージが表示される場合は、ブラウザーでサードパーティの Cookie がブロックされているかどうかを確認します。 これらの Cookie がブロックされている場合は、Cookie を使用できるサイトの一覧に `https://portal.azure.com` を追加してみてください。 Incognito モードを使用している場合は、そのモードでの作業中に、サードパーティの Cookie がブロックされないことを確認します。
   > 
   > 必要な場合は、ページを再度読み込み、ワークフローを開き、もう一度メール アクションを追加してから、接続を作成してみてください。

   Azure によって接続が作成されると、 **[メールの送信]** アクションがデザイナーに表示され、既定で選択されます。 アクションが選択されていない場合は、アクションを選択して、その詳細ペインも開きます。

1. アクション詳細ウィンドウの **[パラメーター]** タブで、アクションに必要な情報を指定します。次に例を示します。

   ![デザイナーと、[パラメーター] タブが選択された [メールの送信] 詳細ペインを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/send-email-action-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **To** | はい | <*your-email-address*> | 電子メールの受信者。テストのためにご自分のメール アドレスを指定できます。 この例では、架空のメール アドレス `sophiaowen@fabrikam.com` を使用しています。 |
   | **件名** | はい | `An email from your example workflow` | メールの件名 |
   | **本文** | はい | `Hello from your example workflow!` | メールの本文の内容 |
   ||||

   > [!NOTE]
   > 詳細ペインの **[設定]** 、 **[静的な結果]** 、または **[実行までの期間]** タブで変更を行うときは、タブを切り替えたり、デザイナーにフォーカスを変更したりする前に、必ず **[完了]** を選択して変更をコミットします。 そうしないと、デザイナーでの変更が保持されません。

1. 作業内容を保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

1. トラフィックを制限する厳しいネットワーク要件またはファイアウォールが環境内にある場合は、ワークフロー内に存在するすべてのトリガーまたはアクション接続に対してアクセス許可を設定する必要があります。 完全修飾ドメイン名を検索するには、「[ファイアウォール アクセス用のドメイン名を検索する](#firewall-setup)」を参照してください。

   そうでない場合、ワークフローをテストするには、[手動で実行をトリガーします](#trigger-workflow)。

<a name="firewall-setup"></a>

## <a name="find-domain-names-for-firewall-access"></a>ファイアウォール アクセス用のドメイン名を検索する

ロジック アプリをデプロイして Azure portal でワークフローを実行する前に、トラフィックを制限する厳しいネットワーク要件またはファイアウォールが環境内にある場合は、論理アプリ内に存在するワークフロー内のすべてのトリガーまたはアクション接続に対するネットワークまたはファイアウォールのアクセス許可を設定する必要があります。

ロジック アプリとワークフローで使用される受信および送信 IP アドレスを検索するには、次の手順を実行します。

1. ロジック アプリのメニューの **[設定]** で、 **[ネットワーク (プレビュー)]** を選択します。

1. [ネットワーク] ページで、 **[Inbound Traffic]\(受信トラフィック\)** と **[Outbound Traffic]\(送信トラフィック\)** のセクションを見つけて確認します。

接続用の完全修飾ドメイン名 (FQDN) を検索するには、次の手順を実行します。

1. ロジック アプリのメニューの **[ワークフロー]** で、 **[接続]** を選択します。 **[API 接続]** タブで、接続のリソース名を選択します。ここに例を示します。

   ![[接続] および [office365] 接続リソース名が選択されている、Azure portal とロジック アプリのメニューを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/logic-app-connections.png)

1. ブラウザーの右上隅に **[JSON ビュー]** が表示されるようにブラウザーの幅を広げて、 **[JSON ビュー]** を選択します。

   ![[JSON ビュー] が選択されている、Azure portal と [API 接続] ペインを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-view-json.png)

1. `connectionRuntimeUrl` プロパティ値をコピーし、安全な場所に保存して、この情報を使用してファイアウォールを設定できるようにします。

   ![選択された "connectionRuntimeUrl" プロパティ値を示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. 各接続について、関連する手順を繰り返します。

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>ワークフローをトリガーする

この例では、着信要求が Request トリガーによって受信されるとワークフローが実行され、それはトリガーによって作成されたエンドポイントの URL に送信されます。 この URL は、ワークフローを初めて保存するときに、Logic Apps サービスによって自動的に生成されます。 そのため、この要求を送信してワークフローをトリガーする前に、この URL を見つけておく必要があります。

1. ワークフロー デザイナーで、 **[HTTP 要求の受信時]** という名前の Request トリガーを選択します。

1. 詳細ペインが開いたら、 **[パラメーター]** タブで、 **[HTTP POST の URL]** プロパティを見つけます。 生成された URL をコピーするには、 **[URL のコピー]** (ファイルのコピー アイコン) を選択し、今のところは URL をどこかに保存しておきます。 URL は次の形式になっています。

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Request トリガーと [HTTP POST の URL] プロパティのエンドポイント URL が表示されているデザイナーを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/find-request-trigger-url.png)

   この例では、URL は次のようになります。

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > エンドポイント URL は、ロジックアプリの **[概要]** ペインの **[Workflow URL]\(ワークフロー URL\)** プロパティで確認することもできます。
   >
   > 1. リソース メニューで **[概要]** を選択します。
   > 1. **[概要]** ペインで、 **[Workflow URL]\(ワークフロー URL\)** プロパティを見つけます。
   > 1. エンドポイント URL をコピーするには、エンドポイント URL のテキストの末尾までポインターを移動し、 **[クリップボードにコピー]** (ファイルのコピー アイコン) を選択します。

1. 要求を送信して URL をテストするには、[Postman](https://www.postman.com/downloads/) を開くか、要求を作成して送信するための任意のツールを開きます。

   この例では、Postman を使用して続行します。 詳細については、[Postman の概要](https://learning.postman.com/docs/getting-started/introduction/)に関するページを参照してください。

   1. Postman のツールバーで、 **[新規]** を選択します。

      ![[新規] ボタンが選択された Postman を示すスクリーンショット](./media/create-single-tenant-workflows-azure-portal/postman-create-request.png)

   1. **[新規作成]** ウィンドウの **[構成要素]** で、 **[要求]** を選択します。

   1. **[要求の保存]** ウィンドウの **[要求名]** で、要求の名前を指定します。たとえば、`Test workflow trigger` のように指定します。

   1. **[保存先のコレクションまたはフォルダーの選択]** で、 **[コレクションの作成]** を選択します。

   1. **[すべてのコレクション]** で、要求を整理するために作成するコレクションの名前を指定して Enter キーを押し、 **[<*コレクション名*> に保存]** を選択します。 この例では、`Logic Apps requests` というコレクション名を使用しています。

      Postman アプリの要求ペインが開き、Request トリガーのエンドポイント URL に要求を送信できるようになります。

      ![要求ウィンドウを開いた Postman を示すスクリーンショット](./media/create-single-tenant-workflows-azure-portal/postman-request-pane.png)

   1. 要求ペインで、既定の要求メソッドとして現在は **[GET]** が表示されている、メソッド一覧の横にあるアドレス ボックスに、先ほどコピーした URL を貼り付けて、 **[送信]** を選択します。

      ![アドレス ボックスにエンドポイント URL が入力され、[送信] ボタンが選択されている Postman を示すスクリーンショット](./media/create-single-tenant-workflows-azure-portal/postman-test-endpoint-url.png)

      トリガーが発生すると、例のワークフローが実行され、次の例のようなメールが送信されます。

      ![例に記載されている Outlook の電子メールを示すスクリーンショット](./media/create-single-tenant-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>実行履歴を確認する

ステートフル ワークフローの場合は、各ワークフローの実行後に、実行全体、トリガー、各アクションの状態およびそれらの入力と出力が含まれる、実行履歴を表示することができます。 Azure portal では、実行履歴とトリガーの履歴が、ロジック アプリ レベルではなくワークフロー レベルで表示されます。 実行履歴のコンテキスト外でトリガー履歴を確認するには、「[トリガー履歴を確認する](#view-trigger-histories)」を参照してください。

1. Azure portal のワークフロー メニューで、 **[概要]** を選択します。

1. **[概要]** ウィンドウで、 **[実行履歴]** を選択します。このワークフローの実行履歴が表示されます。

   ![ワークフローの [概要] ウィンドウで [実行履歴] が選択されているスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > 最新の実行状態が表示されない場合は、 **[概要]** ウィンドウのツール バーで **[更新]** を選択します。 条件が満たされなかったか、データが見つからなかったためにスキップされたトリガーに対しては、実行は行われません。

   | 実行の状態 | 説明 |
   |------------|-------------|
   | **Aborted** | 外部に問題が発生したため、実行が停止したか、または完了しませんでした。たとえば、システムの停止や Azure サブスクリプションの中断などです。 |
   | **取り消し済み** | 実行がトリガーされ、開始されましたが、取り消し要求を受け取りました。 |
   | **Failed** | 実行中に少なくとも 1 つのアクションが失敗しました。 ワークフローの後続のアクションは、エラーを処理するように設定されていません。 |
   | **実行中** | 実行がトリガーされ、進行中です。ただし、この状態は、[アクション制限](logic-apps-limits-and-config.md)または[現在の料金プラン](https://azure.microsoft.com/pricing/details/logic-apps/)によって制限されている実行に対しても表示されます。 <p><p>**ヒント**:[診断ログ](monitor-logic-apps-log-analytics.md)を設定すると、発生するスロットル イベントに関する情報を取得することができます。 |
   | **Succeeded** | 実行は成功しました。 いずれかのアクションが失敗した場合、ワークフロー内の後続のアクションによってそのエラーが処理されます。 |
   | **タイムアウト** | 現在の継続時間が実行継続時間の制限を超えたため、実行がタイムアウトしました。これは、[ **[実行履歴の保持期間 (日数)]** 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)によって制御されます。 実行の継続時間は、実行の開始時刻とその開始時刻での実行継続時間の制限を使用して計算されます。 <p><p>**注**:実行の継続時間が現在の *"実行履歴の保持期間の制限"* も超えている場合は、毎日のクリーンアップ ジョブによって実行履歴から実行が消去されます。これも、[ **[実行履歴の保持期間 (日数)]** 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)によって制御されます。 実行がタイムアウトしたか完了したかにかかわらず、保持期間は常に、実行の開始時刻と "*現在の*" 保持期間の制限を使用して計算されます。 そのため、処理中の実行の継続時間制限を短くすると、実行はタイムアウトします。ただし、実行が実行履歴に残るか消去されるかは、実行の継続時間が保持期間の制限を超えているかどうかに基づいて決まります。 |
   | **待機中** | たとえば、その前のワークフロー インスタンスがまだ実行中である等の理由で、実行が開始されていないか、または一時停止しています。 |
   |||

1. 実行の各ステップの状態を確認するには、確認する実行を選択します。

   実行の詳細ビューが開き、実行の各ステップの状態が表示されます。

   ![ワークフローの各ステップの状態が表示された実行の詳細ビューを示すスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/review-run-details.png)

   ワークフローの各ステップに付けられる状態は次のとおりです。

   | アクションの状態 | 説明 |
   |---------------|-------------|
   | **Aborted** | 外部に問題が発生したため、アクションが停止したか、または完了しませんでした。たとえば、システムの停止や Azure サブスクリプションの中断などです。 |
   | **取り消し済み** | アクションは実行中でしたが、取り消し要求を受け取りました。 |
   | **失敗** | アクションに失敗しました。 |
   | **実行中** | アクションは現在実行中です。 |
   | **Skipped** | `runAfter` 条件が満たされていない (例: 前のアクションが失敗した) ため、アクションはスキップされました。 各アクションの `runAfter` オブジェクトには、現在のアクションが実行可能になる前に満たされる必要がある条件を設定できます。 |
   | **Succeeded** | アクションに成功しました。 |
   | **再試行により成功** | アクションは成功しましたが、1 回以上の再試行がありました。 再試行履歴を確認するには、実行履歴の詳細ビューでその操作を選択し、入力と出力を表示します。 |
   | **タイムアウト** | アクションの設定によって指定されたタイムアウト制限により、アクションが停止しました。 |
   | **待機中** | 呼び出し元からの受信要求を待機している Webhook アクションに適用されます。 |
   |||

   [aborted-icon]: ./media/create-single-tenant-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-single-tenant-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-single-tenant-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-single-tenant-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-single-tenant-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-single-tenant-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-single-tenant-workflows-azure-portal/waiting.png

1. 特定のステップの入力と出力を確認するには、そのステップを選択します。

   ![選択した "メールの送信" アクションでの入力と出力が示されているスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/review-step-inputs-outputs.png)

1. そのステップの未加工の入出力をさらに確認するには、 **[未加工入力の表示]** または **[未加工出力の表示]** を選択します。

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>トリガー履歴を確認する

ステートフル ワークフローの場合は、[実行履歴のコンテキスト](#view-run-history)とは別に、実行ごとのトリガー履歴を確認できます。これには、トリガーの状態と入力および出力が含まれます。 Azure portal では、トリガー履歴と実行履歴が、ロジック アプリ レベルではなくワークフロー レベルで表示されます。 この履歴データを見つけるには、これらの手順に従います。

1. Azure portal のワークフロー メニューで、 **[概要]** を選択します。

1. **[概要]** ページで、 **[トリガーの履歴]** を選択します。

   **[トリガー履歴]** ペインには、ワークフローの実行のトリガー履歴が表示されます。

1. 特定のトリガー履歴を確認するには、その実行の ID を選択します。

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>デプロイの後で Application Insights を有効にするか開く

ワークフローの実行中に、ロジック アプリによって他のイベントと共にテレメトリが出力されます。 このテレメトリを使用して、ワークフローの実行状況や、Logic Apps ランタイムのさまざまな方法での動作を、より明確に把握することができます。 [Application Insights](../azure-monitor/app/app-insights-overview.md) を使用してワークフローを監視でき、ほぼリアルタイムのテレメトリ (ライブ メトリック) が提供されます。 この機能を使用すると、このデータを使用して問題の診断、アラートの設定、グラフの作成を行うときに、エラーやパフォーマンスの問題をより簡単に調査できます。

ロジック アプリの作成とデプロイの設定で [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用がサポートされている場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。 Azure portal でロジック アプリを作成するとき、またはデプロイの後に、それを行うことができます。 Application Insights のインスタンスを用意する必要がありますが、このリソースは、[事前に](../azure-monitor/app/create-workspace-resource.md)、ロジック アプリを作成するときに、またはデプロイの後で、作成することができます。

デプロイされたロジック アプリで Application Insights を有効にするには、または既に有効になっている場合に Application Insights ダッシュボードを開くには、次の手順のようにします。

1. Azure portal で、デプロイされたロジック アプリを見つけます。

1. ロジック アプリのメニューの **[設定]** で、 **[Application Insights]** を選択します。

1. Application Insights が有効になっていない場合は、 **[Application Insights]** ペインで、 **[Application Insights を有効にする]** を選択します。 ペインが更新されたら、下部で **[適用]**  >  **[はい]** を選択します。

   Application Insights が有効になっている場合は、 **[Application Insights]** ペインで、 **[Application Insights データの表示]** を選択します。

Application Insights が開いたら、ロジック アプリのさまざまなメトリックを確認できます。 詳細については、次のトピックをご覧ください。

* [あらゆる場所で実行される Azure Logic Apps - Application Insights で監視する - パート 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [あらゆる場所で実行される Azure Logic Apps - Application Insights で監視する - パート 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>ステートレス ワークフローの実行履歴を有効にする

ステートレス ワークフローをさらに簡単にデバッグするには、そのワークフローの実行履歴を有効にした後、完了したら実行履歴を無効にすることができます。 Azure portal で以下の手順のようにします。または、Visual Studio Code で作業している場合は、[Visual Studio Code でのステートフルおよびステートレス ワークフローの作成](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) で、ご利用の **[ロジック アプリ (Standard)]** リソースを開きます。

1. ロジック アプリのメニューにある **[設定]** で、 **[構成]** を選択します。

1. **[アプリケーションの設定]** タブで、 **[新しいアプリケーション設定]** を選択します。

1. **[アプリケーション設定の追加/編集]** ウィンドウで、 **[名前]** ボックスに次の操作オプションの名前を入力します。 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. **[値]** ボックスに、「`WithStatelessRunHistory`」と入力します。

   次に例を示します。

   ![Azure portal とロジック アプリ (Standard) リソースを示すスクリーンショット。[構成] > [新しいアプリケーション設定] > [アプリケーション設定の追加/編集] ウィンドウが開かれ、"Workflows.{yourWorkflowName}.OperationOptions" オプションが "WithStatelessRunHistory" に設定されている。](./media/create-single-tenant-workflows-azure-portal/stateless-operation-options-run-history.png)

1. このタスクを完了するには、 **[OK]** を選択します。 **[構成]** ペインのツール バーで、 **[保存]** を選択します。

1. 完了時に実行履歴を無効にするには、`Workflows.{yourWorkflowName}.OperationOptions` プロパティを `None` に設定するか、プロパティとその値を削除します。

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>デザイナーから項目を削除する

デザイナーからワークフロー内の項目を削除するには、次のいずれかの手順のようにします。

* 項目を選択し、項目のショートカット メニューを開いて (Shift + F10)、 **[削除]** を選択します。 **[OK]** を選択して確認します。

* 項目を選択して、Delete キーを押します。 **[OK]** を選択して確認します。

* 項目を選択すると、その項目の詳細ペインが開きます。 ペインの右上隅で、省略記号 **[...]** メニューを開き、 **[削除]** を選択します。 **[OK]** を選択して確認します。

  ![詳細ペインが開いてデザイナーに選択した項目が表示され、省略記号ボタンと [削除] コマンドが選択されたスクリーンショット。](./media/create-single-tenant-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > 省略記号メニューが表示されない場合は、右上隅にある省略記号 **[...]** ボタンが詳細ペインに表示されるよう、ブラウザーのウィンドウを十分に広げます。

<a name="restart-stop-start"></a>

## <a name="restart-stop-or-start-logic-apps"></a>ロジック アプリの再起動、停止、または起動

[1 つのロジック アプリ](#restart-stop-start-single-logic-app)または[複数のロジック アプリを同時に](#stop-start-multiple-logic-apps)停止または起動できます。 最初に停止せずに 1 つのロジック アプリを再起動することもできます。 シングルテナント ベースのロジック アプリには複数のワークフローを含めることができるため、ロジック アプリ全体を停止するか、[ワークフローのみを無効にする](#disable-enable-workflows)ことができます。

> [!NOTE]
> ロジック アプリの停止とワークフローの無効化の各操作には、さまざまな影響があります。 詳細については、「[ロジック アプリの停止に関する考慮事項](#considerations-stop-logic-apps)」および[ワークフローの無効化の考慮事項](#disable-enable-workflows)に関するページを確認してください。

<a name="considerations-stop-logic-apps"></a>

### <a name="considerations-for-stopping-logic-apps"></a>ロジック アプリの停止に関する考慮事項

ロジック アプリを停止すると、ワークフロー インスタンスに次のような影響が生じます。

* Azure Logic Apps は、進行中および保留中のすべての実行を直ちに取り消します。

* Azure Logic Apps は、新しいワークフロー インスタンスを作成することも実行することもありません。

* トリガーは、次にその条件が満たされたときに起動されません。 ただし、トリガーの状態には、ロジック アプリが停止したポイントが記憶されます。 そのため、ロジック アプリを再起動すると、前回の実行以降のすべての未処理の項目に対してトリガーが起動されます。

  前回の実行以降に未処理の項目に対して各ワークフローがトリガーしないようにするには、次の手順に従って、ロジック アプリを再起動する前にトリガーの状態をクリアします。

  1. Azure Portal でロジック アプリを開きます。
  1. ロジック アプリのメニューの **[ワークフロー]** で、 **[ワークフロー]** を選択します。
  1. ワークフローを開き、そのワークフローのトリガーの任意の部分を編集します。
  1. 変更を保存します。 この手順により、トリガーの現在の状態がリセットされます。
  1. 各ワークフローに対してこの手順を繰り返します。
  1. 完了したら、[ロジック アプリを再起動](#restart-stop-start-single-logic-app)します。

<a name="restart-stop-start-single-logic-app"></a>

### <a name="restart-stop-or-start-a-single-logic-app"></a>単一のロジック アプリの再起動、停止、または起動

1. Azure Portal でロジック アプリを開きます。

1. ロジック アプリのメニューで、 **[概要]** を選択します。

   * 停止せずにロジック アプリを再起動するには、[概要] ペインのツール バーで **[再起動]** を選択します。
   * 実行中のロジック アプリを停止するには、[概要] ペインのツール バーで **[停止]** を選択します。 選択内容を確認します。
   * 停止したロジック アプリを起動するには、[概要] ペインのツール バーで **[起動]** を選択します。

   > [!NOTE]
   > ロジック アプリが既に停止している場合は、 **[起動]** オプションのみが表示されます。 ロジック アプリが既に実行されている場合は、 **[停止]** オプションのみが表示されます。
   > ロジック アプリはいつでも再起動できます。

1. 操作が成功したか失敗したかを確認するには、メインの Azure ツール バーで、 **[通知]** の一覧 (ベルのアイコン) を開きます。

<a name="stop-start-multiple-logic-apps"></a>

### <a name="stop-or-start-multiple-logic-apps"></a>複数のロジック アプリの停止または起動

複数のロジック アプリを同時に停止または起動できますが、最初に停止せずに複数のロジック アプリを再起動することはできません。

1. Azure portal のメイン検索ボックスに「`logic apps`」と入力し、 **[ロジック アプリ]** を選択します。

1. **[ロジック アプリ]** ページで、ロジック アプリの **[状態]** 列を確認します。

1. チェック ボックスの列で、停止または起動するロジック アプリを選択します。

   * 選択した実行中のロジック アプリを停止するには、[概要] ウィンドウのツール バーで **[無効化または停止]** を選択します。 選択内容を確認します。
   * 選択した停止中のロジック アプリを起動するには、[概要] ウィンドウのツール バーで **[有効化または開始]** を選択します。

1. 操作が成功したか失敗したかを確認するには、メインの Azure ツール バーで、 **[通知]** の一覧 (ベルのアイコン) を開きます。

<a name="disable-enable-workflows"></a>

## <a name="disable-or-enable-workflows"></a>ワークフローの無効化または有効化

次にトリガー条件が満たされたときにトリガーが発動しないようにするには、ワークフローを無効にします。 1 つのワークフローを無効化または有効化できますが、複数のワークフローを同時に無効化または有効化することはできません。 ワークフローを無効にすると、ワークフロー インスタンスに次のような影響が生じます。

* Azure Logic Apps サービスによって、進行中および保留中の実行がすべてその完了まで続行されます。 このプロセスは、ボリュームやバックログによっては、完了までに時間がかかる場合があります。

* Azure Logic Apps は、新しいワークフロー インスタンスを作成することも実行することもありません。

* トリガーは、次にその条件が満たされたときに起動されません。 ただし、トリガーの状態には、ワークフローが無効化されたポイントが記憶されます。 そのため、ワークフローを再度有効にすると、前回の実行以降のすべての未処理の項目に対してトリガーが起動されます。

  前回の実行以降の未処理の項目に対してトリガーが起動しないようにするには、ワークフローを再度有効にする前に、トリガーの状態をクリアします。

  1. ワークフローで、ワークフローのトリガーの任意の部分を編集します。
  1. 変更を保存します。 この手順により、トリガーの現在の状態がリセットされます。
  1. [ワークフローを再度アクティブ化します](#disable-enable-workflows)。

> [!NOTE]
> ワークフローの無効化とロジック アプリの停止の各操作には、さまざまな影響があります。 詳細については、[ロジック アプリの停止の考慮事項](#considerations-stop-logic-apps)に関するページを確認してください。

<a name="disable-workflow"></a>

### <a name="disable-workflow"></a>ワークフローを無効にする

1. ロジック アプリのメニューの **[ワークフロー]** で、 **[ワークフロー]** を選択します。 チェック ボックスの列で、無効にするワークフローを選択します。

1. **[ワークフロー]** ペインのツール バーで、 **[無効化]** を選択します。

1. 操作が成功したか失敗したかを確認するには、メインの Azure ツール バーで、 **[通知]** の一覧 (ベルのアイコン) を開きます。

<a name="enable-workflow"></a>

### <a name="enable-workflow"></a>ワークフローを有効にする

1. ロジック アプリのメニューの **[ワークフロー]** で、 **[ワークフロー]** を選択します。 チェック ボックスの列で、有効にするワークフローを選択します。

1. **[ワークフロー]** ペインのツール バーで、 **[有効化]** を選択します。

1. 操作が成功したか失敗したかを確認するには、メインの Azure ツール バーで、 **[通知]** の一覧 (ベルのアイコン) を開きます。

<a name="delete"></a>

## <a name="delete-logic-apps-or-workflows"></a>ロジック アプリまたはワークフローを削除する

[1 つまたは複数のロジック アプリを同時に削除する](#delete-logic-apps)ことができます。 シングルテナント ベースのロジック アプリには複数のワークフローを含めることができるため、ロジック アプリ全体を削除するか、[ワークフローのみを削除する](#delete-workflows)ことができます。

<a name="delete-logic-apps"></a>

### <a name="delete-logic-apps"></a>ロジック アプリを削除する

ロジック アプリを削除すると、進行中および保留中の実行は直ちに取り消されますが、アプリによって使用されているストレージ上でクリーンアップ タスクは実行されません。

1. Azure portal のメイン検索ボックスに「`logic apps`」と入力し、 **[ロジック アプリ]** を選択します。

1. **[ロジック アプリ]** 一覧のチェック ボックスの列で、削除する 1 つまたは複数のロジック アプリを選択します。 ツールバーの **[削除]** を選択します。

1. 確認ボックスが表示されたら、「`yes`」と入力して、 **[削除]** を選択します。

1. 操作が成功したか失敗したかを確認するには、メインの Azure ツール バーで、 **[通知]** の一覧 (ベルのアイコン) を開きます。

<a name="delete-workflows"></a>

### <a name="delete-workflows"></a>ワークフローの削除

ワークフローを削除すると、ワークフロー インスタンスに次のような影響が生じます。

* Azure Logic Apps サービスは、進行中および保留中の実行を直ちに取り消しますが、ワークフローによって使用されているストレージ上でクリーンアップ タスクは実行されます。

* Azure Logic Apps は、新しいワークフロー インスタンスを作成することも実行することもありません。

* ワークフローを削除してから同じワークフローを再作成しても、再作成されたワークフローに、削除したワークフローと同じメタデータが割り当てられることはありません。 メタデータを最新の情報に更新するために、削除したワークフローの呼び出し元となったワークフローを再保存する必要があります。 これにより、呼び出し元は、再作成されたワークフローの正しい情報を取得します。 それ以外の場合、再作成したワークフローの呼び出しは、`Unauthorized` エラーで失敗します。 この動作は、統合アカウントのアーティファクトを使用するワークフローや、Azure 関数を呼び出すワークフローにも当てはまります。

1. Azure Portal でロジック アプリを開きます。

1. ロジック アプリのメニューの **[ワークフロー]** で、 **[ワークフロー]** を選択します。 チェック ボックスの列で、削除する 1 つまたは複数のワークフローを選択します。

1. ツールバーの **[削除]** を選択します。

1. 操作が成功したか失敗したかを確認するには、メインの Azure ツール バーで、 **[通知]** の一覧 (ベルのアイコン) を開きます。

<a name="recover-deleted"></a>

## <a name="recover-deleted-logic-apps"></a>削除されたロジック アプリの復旧

ソース管理を使用している場合は、削除された **ロジック アプリ(Standard)** リソースをシングルテナント Azure Logic Apps にシームレスに再デプロイできます。 ただし、ソース管理を使用していない場合は、次の手順を試して、削除されたロジック アプリを復旧してください。

> [!NOTE]
> 削除されたロジック アプリを復旧する前に、次の考慮事項を確認してください。
>
> * **Workflow Standard** ホスティング プランを使用する削除済み **ロジック アプリ (Standard)** リソースのみを復旧できます。 
> 削除された **ロジック アプリ (従量課金)** リソースは復旧できません。
>
> * ワークフローが Request トリガーで始まる場合、復旧されたロジック アプリのコールバック URL は、削除されたロジック アプリの URL とは異なります。
>
> * 削除されたロジック アプリからの実行履歴は、復旧されたロジック アプリでは使用できません。

1. ロジック アプリのストレージ アカウントがまだ存在することを確認してください。 ストレージ アカウントが削除されている場合は、[まず、削除されたストレージ アカウントを復旧する](../storage/common/storage-account-recover.md)する必要があります。

1. ストレージ アカウント メニューの **[セキュリティとネットワーク]** で、 **[アクセス キー]** を選択します。

1. **[アクセス キー]** ページ で、アカウントのプライマリ接続文字列をコピーし、後で使用するために保存します。次に例を示します。

   `DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accesskey>;EndpointSuffix=core.windows.net`

1. ストレージ アカウント メニューの **[データ ストレージ]** で、 **[ファイル共有]** を選択し、ロジック アプリに関連付けられているファイル共有の名前をコピーして、後で使用するために保存します。

1. 同じホスティング プランと価格レベルを使用して、新しい **ロジック アプリ (Standard)** リソースを作成します。 新しい名前を使用するか、削除されたロジック アプリの名前を再利用できます。

1. 続行する前に、ロジック アプリを停止します。 ロジック アプリのメニューで、 **[概要]** を選択します。 **[概要]** ページのツール バーで、 **[停止]** を選択します。

1. ロジック アプリ メニューの **[設定]** で、 **[構成]** を選択します。

1. **[構成]** ページで、次のアプリケーション設定の値を更新し、完了したら変更を保存します。

   | アプリ設定 | 置換値 |
   |-------------|-------------------|
   | `AzureWebJobsStorage` | 既存の値を、ストレージ アカウントから前にコピーした接続文字列に置き換えます。 |
   | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | 既存の値を、ストレージ アカウントから前にコピーした文字列に置き換えます。 |
   | `WEBSITE_CONTENTSHARE` | 既存の値を、前にコピーしたファイル共有名に置き換えます。 |
   |||

1. ロジック アプリのメニューの **[ワークフロー]** で、 **[接続]** を選択します。

1. 各接続を開き、 **[設定]** で **[アクセス ポリシー]** を選択します。

1. 削除されたロジック アプリのアクセス ポリシーを削除し、置換ロジック アプリの新しいアクセス ポリシーを追加します。

1. ロジック アプリの **[構成]** ページに戻り、削除されたロジック アプリに存在していたカスタム設定を追加します。

1. 完了したら、ロジック アプリを再起動します。

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>問題とエラーのトラブルシューティング

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>前に作成したワークフローのデザイナー ピッカーに新しいトリガーとアクションがない

シングルテナントの Azure Logic Apps では、Azure 関数の操作、Liquid の操作、XML の操作 ( **[XML の検証]** や **[XML の変換]** など) に対する組み込みアクションがサポートされています。 ただし、以前に作成したロジック アプリでは、ロジック アプリで古いバージョンの拡張機能バンドル `Microsoft.Azure.Functions.ExtensionBundle.Workflows` が使用されている場合、これらの操作がデザイナーに表示されないことがあります。

この問題を解決するには、次の手順に従って古いバージョンを削除し、拡張機能バンドルを最新バージョンに自動的に更新できるようにします。

> [!NOTE]
> この特定の解決方法が適用されるのは、Azure portal を使用して作成した **ロジック アプリ (Standard)** リソースだけであり、Visual Studio Code と Azure Logic Apps (Standard) 拡張機能を使用して作成およびデプロイしたロジック アプリは対象外です。 [サポートされているトリガーとアクションが Visual Studio Code のデザイナーに表示されない場合](create-single-tenant-workflows-visual-studio-code.md#missing-triggers-actions)に関するページを参照してください。

1. Azure portal でロジック アプリを停止します。

   1. ロジック アプリのメニューで、 **[概要]** を選択します。

   1. **[概要]** ペインのツール バーで、 **[停止]** を選択します。

1. ロジック アプリのメニューの **[開発ツール]** で、 **[高度なツール]** を選択します。

1. **[高度なツール]** ペインで、 **[移動]** を選択します。これにより、ロジック アプリ用の Kudu 環境が開きます。

1. Kudu のツール バーで、 **[Debug console]\(デバッグ コンソール\)** メニューを開き、 **[CMD]** を選択します。

   コンソール ウィンドウが開き、コマンド プロンプトを使用してバンドル フォルダーを参照できるようになります。 または、コンソール ウィンドウの上に表示されるディレクトリ構造を参照することもできます。

1. 次のフォルダーを参照します。そこには、既存のバンドルのバージョン管理されたフォルダーが含まれています。

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 既存のバンドルのバージョン フォルダーを削除します。 コンソール ウィンドウで、次のコマンドを実行します。`{bundle-version}` を既存のバージョンに置き換えます。

   `rm -rf {bundle-version}`

   例: `rm -rf 1.1.3`

   > [!TIP]
   > "アクセス許可が拒否されました" や "ファイルが使用されています" などのエラーが発生する場合は、ブラウザーでページを更新し、フォルダーが削除されるまで前の手順をもう一度試してください。

1. Azure portal でロジック アプリの **[概要]** ページに戻り、 **[再起動]** を選択します。

   ポータルにより、最新のバンドルが自動的に取得されて使用されます。

## <a name="next-steps"></a>次の手順

このシナリオに関するお客様からのご意見をお待ちしております。

* バグまたは問題については、[GitHub で問題を作成してください](https://github.com/Azure/logicapps/issues)。
* 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/lafeedback)。
