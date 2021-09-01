---
title: Visual Studio でサーバーレス アプリの例を作成する
description: Azure クイックスタート テンプレートを使用して、Visual Studio で Azure Logic Apps と Azure Functions を使用してサーバーレス アプリの例を作成、配置、管理します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: df395fcffc1a7e675921f2ff397d488c301c703a
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363859"
---
# <a name="create-an-example-serverless-app-with-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Visual Studio で Azure Logic Apps と Azure Functions を使用してサーバーレス アプリの例を作成する

Azure Logic Apps や Azure Functions などの Azure のサービスおよび機能を使用して、クラウド ベースの "サーバーレス" アプリをすばやく作成、構築、配置できます。 Azure Logic Apps を使用すると、少量のコードまたはコードなしのアプローチを使用してワークフローをすばやく、かつ簡単に構築して、組み合わされたタスクの調整を簡略化できます。 サービス間のやり取りをコード化することなく、またグルー コードを維持したり、新しい API や仕様を学習したりしなくても、さまざまなサービス (クラウド、オンプレミス、またはハイブリッド) を統合できます。 Azure Functions を使用すると、イベント ドリブン モデルを使用して開発を高速化できます。 独自のコードを自動的に実行することによって、イベントに応答するトリガーを使用できます。 バインドを使用して他のサービスをシームレスに統合できます。

この記事では、Azure クイックスタート テンプレートを使用して、マルチテナント Azure で実行されるサーバーレス アプリの例を作成する方法について説明します。 このテンプレートは、Azure Resource Manager の配置テンプレートを含む Azure リソース グループ プロジェクトを作成します。 このテンプレートでは、定義済みのワークフローに、定義した Azure 関数への呼び出しが含まれている基本的なロジック アプリ リソースを定義します。 このワークフロー定義には、次のコンポーネントが含まれています。

* HTTP 要求を受信する Request トリガー。 このトリガーを起動するには、トリガーの URL に要求を送信します。
* 後で定義できる Azure 関数を呼び出す Azure Functions アクション。
* その関数からの結果が含まれている HTTP 応答を返す Response アクション。

詳細については、次の記事を参照してください。

* [サーバーレス コンピューティング: サーバーレス テクノロジの概要](https://azure.microsoft.com/overview/serverless-computing/)
* [Azure Logic Apps の概要](logic-apps-overview.md)
* [Azure Functions について](../azure-functions/functions-overview.md)
* [Azure Serverless: Azure Logic Apps と Azure Functions を使用してクラウド ベースのアプリおよびソリューションを構築するための概要](logic-apps-serverless-overview.md)

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* 次のツールをダウンロードしてインストールします (まだ行っていない場合)。

  * [Visual Studio 2019、2017、または 2015 (Community または他のエディション)](https://aka.ms/download-visual-studio)。 このクイックスタートでは、Visual Studio Community 2019 (無料) を使用します。

    > [!IMPORTANT]
    > Visual Studio 2019 または 2017 をインストールする場合は、必ず **[Azure の開発]** ワークロードを選択してください。

  * [Microsoft Azure SDK for .NET (バージョン 2.9.1 以降)](https://azure.microsoft.com/downloads/)。 [Azure SDK for .NET](/dotnet/azure/intro) の詳細を参照してください。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)。

  * 使用している Visual Studio バージョン用の Azure Logic Apps Tools。 [この拡張機能を Visual Studio 内からインストールする方法を確認する](/visualstudio/ide/finding-and-using-visual-studio-extensions)か、または Visual Studio Marketplace から Azure Logic Apps Tools の各バージョンをダウンロードできます。

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)
    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)
    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    > [!IMPORTANT]
    > インストールが完了したら、必ず Visual Studio を再起動してください。

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)。Azure 関数をローカルでデバッグできるようにします。 詳細については、「[Azure Functions Core Tools の操作](../azure-functions/functions-run-local.md)」を確認してください。

* 埋め込みのワークフロー デザイナーを使用している間のインターネットへのアクセス。

  デザイナーが Azure にリソースを作成し、ワークフローで[マネージド コネクタ](../connectors/managed.md)からプロパティやデータを読み取るには、インターネット接続が必要です。 たとえば、SQL コネクタを使用する場合、デザイナーはサーバー インスタンスをチェックして、既定のプロパティやカスタム プロパティが使用可能かどうか確認します。

## <a name="create-a-resource-group-project"></a>リソース グループ プロジェクトを作成する

最初に、サーバーレス アプリ用のコンテナーとして Azure リソース グループ プロジェクトを作成します。 Azure では、"*リソース グループ*" は、アプリ全体のリソースを整理するために使用する論理的なコレクションです。 その後、これらのリソースを 1 つの資産として管理したり配置したりできます。 Azure のサーバーレス アプリの場合、リソース グループには Azure Logic Apps "*および*" Azure Functions のリソースが含まれています。 詳細については、[リソース マネージャーの用語](../azure-resource-manager/management/overview.md#terminology)に関するページを確認してください。

1. Visual Studio を開き、Azure アカウントでサインインします (求められた場合)。

1. 開始ウィンドウが開いた場合は、 **[新しいプロジェクトの作成]** を選択します。

   ![[新しいプロジェクトの作成] が選択された Visual Studio の開始ウィンドウを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/start-window.png)

1. 開始ウィンドウが開かない場合は、 **[ファイル]** メニューから、 **[新規]**  >  **[プロジェクト]** の順に選択します。

   ![[新規] メニューと [プロジェクト] が選択された開かれている [ファイル] メニューを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/create-new-project-visual-studio.png)

1. **[新しいプロジェクトの作成]** ウィンドウが開いたら、検索ボックスで、C# または Visual Basic の **[Azure リソース グループ]** プロジェクト テンプレートを選択します。 準備ができたら、 **[次へ]** を選択します。 この例では C# で続行します。

   ![検索ボックスに "リソース グループ" が含まれており、[Azure リソース グループ] プロジェクト テンプレートが選択された [新しいプロジェクトの作成] ウィンドウを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/start-window-find-project-template.png)

1. **[新しいプロジェクトを構成する]** ウィンドウが開いたら、プロジェクトに関する情報 (名前など) を指定します。 完了したら **[作成]** を選択します。

   ![[新しいプロジェクトを構成する] ウィンドウとプロジェクトの詳細を示すスクリーンショット。](./media/create-serverless-apps-visual-studio/start-window-create-new-project-details.png)

1. **[Azure テンプレートの選択]** ウィンドウが開いたら、 **[この場所からテンプレートを参照します]** の一覧から、テンプレートの場所として **[Azure クイックスタート (github.com/Azure/azure-quickstart-templates)]** を選択します。

1. 検索ボックスに「 `logic-app-and-function-app`」と入力します。 結果から、**quickstarts\microsoft.logic\logic-app-and-function-app** という名前のテンプレートを選択します。 終了したら、 **[OK]** を選択します。

   ![テンプレートの場所として [Azure クイックスタート] が選択され、[quickstarts\microsoft.logic\logic-app-and-function-app] が選択された [Azure テンプレートの選択] ウィンドウを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/select-template.png)

   Visual Studio によって、プロジェクト用のソリューション コンテナーを含むリソース グループ プロジェクトが作成されます。

   ![作成されたプロジェクトとソリューションを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/create-serverless-solution.png)

1. 次に、ソリューションを Azure にデプロイします。

   > [!IMPORTANT]
   > 必ず配置手順を完了するようにしてください。 そうしないと、Visual Studio でデザイナーを使用してロジック アプリのワークフローを開いたり、確認または編集したりできません。

## <a name="deploy-your-solution"></a>ソリューションのデプロイ

Visual Studio でデザイナーを使用してロジック アプリを開くには、そのアプリを Azure に配置しておく必要があります。 それにより、デザイナーは、ロジック アプリのワークフローで使用されるサービスやリソースへの接続を作成できます。

1. ソリューション エクスプローラーでリソース プロジェクトのショートカット メニューから、 **[配置]**  >  **[新規]** の順に選択します。

   ![プロジェクトのショートカット メニューが開かれ、[配置] メニューが開かれて [新規] が選択されたソリューション エクスプローラーを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/deploy.png)

1. **[リソース グループに配置する]** ウィンドウが開いたら、次の手順に従って配置情報を指定します。

   1. ウィンドウによって現在のサブスクリプションが検出されたら、その Azure サブスクリプションを確認するか、または必要に応じて別のサブスクリプションを選択します。

   1. Azure に新しいリソース グループを作成します。 **[リソース グループ]** の一覧から、 **[新規作成]** を選択します。

      ![[新規作成] が選択された [リソース グループに配置する] ウィンドウを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/create-resource-group-start.png)

   1. **[リソース グループの作成]** ウィンドウが開いたら、次の情報を指定します。

      | プロパティ | 説明 |
      |----------|-------------|
      | リソース グループ名 | リソース グループに付ける名前 |
      | リソース グループの場所 | ロジック アプリ リソースをホストする Azure データセンターのリージョン |
      |||

      この例では、米国西部リージョンにリソース グループを作成して続行します。

      ![新しいリソース グループの情報が表示された [リソース グループの作成] ウィンドウを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/create-resource-group.png)

   1. ソリューションの作成と配置を完了します。たとえば、次のようにします。

      ![展開の設定](./media/create-serverless-apps-visual-studio/deploy-to-resource-group.png)

1. **[パラメーターの編集]** ウィンドウが表示された場合は、配置されたロジック アプリ リソースと関数アプリのリソースに使用するリソース名を指定した後、変更を保存します。

   > [!IMPORTANT]
   > ロジック アプリと関数アプリには必ずグローバルに一意の名前を使用してください。

   ![ロジック アプリ リソースと関数アプリのリソースの名前が表示された [パラメーターの編集] ウィンドウを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/logic-function-app-name-parameters.png)

   Visual Studio がリソース グループへの配置を開始すると、ソリューションの配置の状態が Visual Studio の **[出力]** ウィンドウに表示されます。 展開が終了すると、ロジック アプリが Azure portal に存在するようになります。

## <a name="open-and-edit-your-deployed-logic-app"></a>配置されたロジック アプリを開いて編集する

1. ソリューション エクスプローラーで、**azuredeploy.json** ファイルのショートカット メニューから、 **[ロジック アプリ デザイナーで開く]** を選択します。

   ![[ロジック アプリ デザイナーで開く] が選択された [azuredeploy.json] ショートカット メニューを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/open-designer.png)

   > [!TIP]
   > このコマンドが Visual Studio 2019 にない場合は、Visual Studio に最新の更新プログラムが適用されていることを確認してください。

1. ワークフロー デザイナーが開いたら、ワークフローを編集するか、またはステップを追加して続行できます。 完了したら、変更を忘れずに **azuredeploy.json** ファイルに保存します。

   ![デザイナーでのロジック アプリ ワークフローを示すスクリーンショット。](./media/create-serverless-apps-visual-studio/opened-logic-app-workflow.png)

## <a name="create-an-azure-functions-project"></a>Azure Functions プロジェクトを作成する

ソリューション内から C# ベースの Azure 関数を作成するには、「[クイック スタート: Visual Studio を使用して Azure で初めての C# 関数を作成する](../azure-functions/functions-create-your-first-function-visual-studio.md)」に従って C# クラス ライブラリ プロジェクトを作成します。 それ以外の場合、他の言語を使用して Azure Functions プロジェクトと関数を作成するには、次のクイック スタートに従います。

* [クイックスタート: Visual Studio Code を使用して Azure に Java 関数を作成する](../azure-functions/create-first-function-vs-code-java.md)
* [クイックスタート: Visual Studio Code を使用して Azure に JavaScript 関数を作成する](../azure-functions/create-first-function-vs-code-node.md)
* [クイックスタート: Visual Studio Code を使用して Azure で PowerShell 関数を作成する](../azure-functions/create-first-function-vs-code-powershell.md)
* [クイック スタート: Visual Studio Code を使用して Azure に Python 関数を作成する](../azure-functions/create-first-function-vs-code-python.md)
* [クイック スタート: Visual Studio Code を使用して Azure に TypeScript 関数を作成する](../azure-functions/create-first-function-vs-code-typescript.md)

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio から関数を展開する

ソリューション内の配置テンプレートでは、**azuredeploy.json** ファイル内の変数によって指定されている Git リポジトリから、ソリューション内に存在する任意の Azure 関数を配置できます。 ソリューションで Functions プロジェクトを作成する場合は、そのプロジェクトを Git ソース管理 (GitHub や Azure DevOps など) にチェックインしてから、テンプレートによって Azure 関数が配置されるように **azuredeploy.json** ファイル内の `repo` 変数を更新できます。

## <a name="manage-logic-apps-and-view-run-history"></a>ロジック アプリを管理して実行履歴を表示する

ロジック アプリ リソースが既に Azure に配置されている場合は、これらのアプリを Visual Studio から編集または管理したり、実行履歴を表示したり、無効にしたりすることができます。 詳細については、「[Visual Studio でロジック アプリを管理する](manage-logic-apps-with-visual-studio.md)」を確認してください。

## <a name="next-steps"></a>次のステップ

* Azure Logic Apps と Azure Functions を使用した別の例については、「[チュートリアル: Azure Logic Apps、Azure Functions、Azure Storage を使用してメール処理のタスクを自動化する](tutorial-process-email-attachments-workflow.md)」を試してください。