---
title: Power Automate からフローを Azure Logic Apps にエクスポートする
description: Azure Resource Manager テンプレートとしてエクスポートすることによって Power Automate から Azure Logic Apps にフローを移行する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 4512fd8a236c888937eb60a75d01cfb0ef2c1913
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792648"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Power Automate からフローをエクスポートして Azure Logic Apps にデプロイする

フローの機能を拡張するために、そのフローを [Power Automate](https://flow.microsoft.com) から [Azure Logic Apps](../logic-apps/logic-apps-overview.md) に移行することができます。 フローをロジック アプリの Azure Resource Manager テンプレートとしてエクスポートし、そのロジック アプリ テンプレートを Azure リソース グループにデプロイした後、Logic App Designer でそのロジック アプリを開くことができます。

> [!NOTE]
> すべての Power Automate コネクタが Azure Logic Apps で使用できるわけではありません。 Azure Logic Apps に[同等のコネクタ](../connectors/apis-list.md)があるフローをインポートできます。 たとえば、Button トリガー、Approval コネクタ、および Notification コネクタは Power Automate に固有です。
>
> Power Automate からエクスポートされた OpenAPI ベースのフローは、現在、ロジック アプリ テンプレートとしてのデプロイではサポートされていません。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Power Automate からエクスポートするフロー

## <a name="export-a-flow"></a>フローをエクスポートする

1. [Power Automate](https://flow.microsoft.com) にサインインし、 **[マイ フロー]** を選択します。 フローを探して選択します。 ツール バーで、省略記号 ( **...** ) ボタンをクリックします。 **[エクスポート]**  >  **[Logic Apps template (.json)]\(Logic Apps テンプレート (.json)\)** を選択します。

   ![フローをエクスポートする](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. 目的の場所にテンプレートを保存します。

詳細については、[Azure Logic Apps への成長](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)に関する記事を参照してください。

## <a name="deploy-template-by-using-the-azure-portal"></a>Azure portal を使用してテンプレートをデプロイする

1. Azure アカウントで [Azure portal](https://portal.azure.com) にサインインします。

1. Azure のメイン メニューで **[リソースの作成]** を選択します。 検索ボックスに「template deployment」と入力します。 **[Template deployment (deploy using custom templates)]\(テンプレートのデプロイ (カスタム テンプレートを使用してデプロイ)\)** を選択して、 **[作成]** を選択します。

   !["テンプレートのデプロイ" を選択](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. **[カスタム デプロイ]** で、 **[Build your own template in the editor]\(エディターで独自のテンプレートをビルド\)** を選択します。

   !["Build your own template in the editor" (エディターで独自のテンプレートをビルド) を選択](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. **[テンプレートの編集]** ツール バーで、 **[ファイルの読み込み]** を選択します。 Power Automate からエクスポートした JSON テンプレートを探して選択し、 **[開く]** を選択します。

   !["ファイルの読み込み" を選択](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. エディターにテンプレートの JSON、パラメーター、およびリソースが表示されたら、 **[保存]** を選択します。
  
   ![テンプレートを保存する](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. ここで、テンプレートに次の入力パラメーターを指定します。

   * 課金に使用する Azure サブスクリプション
   * Azure リソース グループ
   * Azure リソース グループの場所
   * ロジック アプリ リソースの名前
   * ロジック アプリ リソースの場所 (Azure リソース グループと異なる場合)
   * ロジック アプリで再利用できる、以前に作成された接続の名前

      最初のロジック アプリを作成する場合は、すべての接続が新しく作成されるため、既定の名前をそのまま使用できます。 それ以外の場合は、以前に作成した接続の名前を指定できます。これは、複数のロジック アプリで使用できます。

   テンプレートに関するこの情報を入力した後、必要な Azure リソースの作成とそれに応じた Azure サブスクリプションへの課金に関する Azure Marketplace の使用条件を確認して同意し、 **[購入]** を選択します。
  
   ![テンプレートの入力パラメーターを指定する](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure は、指定されたリソース グループに、テンプレートをロジック アプリとしてデプロイします。 Power Automate から移行するすべてのロジック アプリは、無効の状態でデプロイされます。

1. ロジック アプリをアクティブ化する前に、次の手順に従って新しい接続を承認します。

   1. 作成したロジック アプリを開きます。 ロジック アプリのメニューで **[ロジック アプリ デザイナー]** を選択します。

      承認が必要な接続ごとに、警告アイコンが表示されます。

      ![警告のアイコン](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 承認された接続が必要な手順ごとに、その手順を展開して **[Add new]\(新規追加\)** を選択します。

      ![新しい接続を追加する](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 各サービスにサインインするか、接続を承認するために必要な資格情報を指定します。

1. ロジック アプリを保存します。 ロジック アプリをアクティブ化する準備ができたら、ロジック アプリのメニューで **[概要]** を選択し、 **[Enable]\(有効化\)** を選択します。

   ![ロジック アプリを有効にする](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 重複したワークフローを実行しないようにするために、必ず、元のフローを非アクティブ化または削除してください。

## <a name="deploy-template-by-using-visual-studio"></a>Visual Studio を使用してテンプレートをデプロイする

ロジック アプリを作成するための[前提条件](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)を使用して Visual Studio をセットアップした場合は、エクスポートしたテンプレートを Visual Studio から Azure Logic Apps にデプロイできます。

1. Visual Studio で、Power Automate からエクスポートしたテンプレート ファイルを開きます。

1. Visual Studio で、[Azure リソース グループ] プロジェクトを作成し、 **[ロジック アプリ]** テンプレートを選択します。手順については、「[クイック スタート:Azure Logic Apps を使用して自動化されたタスク、プロセス、およびワークフローを作成する - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)」を参照してください。次に例を示します。

   ![Azure リソース グループ プロジェクトを作成する](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. **LogicApp.json** ファイルがまだ開いていない場合、ソリューション エクスプローラーからこのファイルを開きます。

1. エクスポートされたテンプレートから内容をコピーして、**LogicApp.json** ファイルの内容を上書きします。

1. ロジック アプリをデプロイする前に、次の手順に従って新しい接続を承認します。

   1. **LogicApp.json** のショートカット メニューを開き、 **[Open With Logic App Designer]\(Logic App Designer で開く\)** を選択します。

      ![Logic App Designer でテンプレートを開く](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. メッセージが表示されたら、ロジック アプリのデプロイに使用する Azure サブスクリプションとリソース グループを選択します。

      ![Azure サブスクリプションとリソース グループを選択する](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      ロジック アプリがデザイナーに表示された後、承認を必要とするすべての接続に警告アイコンが表示されます。

      ![警告アイコンがある接続](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 承認された接続が必要な手順ごとに、その手順を展開して **[Add new]\(新規追加\)** を選択します。

      ![新しい接続を追加する](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 各サービスにサインインするか、接続を承認するために必要な資格情報を指定します。

   1. ロジック アプリをデプロイする前に、ソリューションを保存します。

1. ソリューション エクスプローラーで、プロジェクトのショートカット メニューを開き、 **[デプロイ]**  >  **[新規作成]** の順に選択します。 メッセージに従って Azure アカウントでサインインします。

1. メッセージが表示されたら、Azure サブスクリプション、Azure リソース グループ、および、デプロイに使用するその他の設定 (テンプレートのパラメーター値を渡すために使用する[パラメーター ファイル](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)など) を確認して、 **[デプロイ]** を選択します。

   ![デプロイの設定を確認する](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. **[パラメーターの編集]** ボックスが表示された場合は、Azure 内のロジック アプリ リソースの名前を指定して **[保存]** を選択します。  

   ![デプロイのパラメーターを編集する](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   デプロイが開始されると、Visual Studio の **[出力]** ウィンドウにアプリのデプロイ状態が表示されます。 状態が表示されない場合、 **[Show output from]\(出力元の表示\)** の一覧を開いて、Azure リソース グループを選択します。 例:

   ![[出力] ウィンドウ](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   ロジック アプリでの接続にユーザーからの入力が必要な場合は、バックグラウンドで PowerShell ウィンドウが開き、必要なパスワードまたはシークレット キーの入力を求められます。 その情報を入力すると、デプロイが続行されます。

   ![接続の認証](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   デプロイが完了すると、Azure portal でロジック アプリが公開されますが、アクティブ化はされません。

1. Azure portal でロジック アプリをアクティブ化する準備ができたら、Logic App Designer でロジック アプリを探して開きます。 ロジック アプリのメニューで **[概要]** を選択し、 **[Enable]\(有効化\)** を選択します。

1. 重複したワークフローを実行しないようにするために、必ず、元のフローを非アクティブ化または削除してください。

これらのデプロイ手順の詳細については、次を参照してください: [クイック スタート:Azure Logic Apps を使用して自動化されたタスク、プロセス、およびワークフローを作成する - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>次の手順

* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)の詳細について学習します。
* [Azure Logic Apps](../logic-apps/logic-apps-overview.md) について学習します
