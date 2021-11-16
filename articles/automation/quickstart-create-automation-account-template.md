---
title: Resource Manager テンプレートを使用して Azure Automation アカウントを作成する
titleSuffix: Azure Automation
description: この記事では、Azure Resource Manager テンプレートを使用して Automation アカウントを作成する方法について説明します。
services: automation
ms.date: 08/27/2021
ms.topic: conceptual
ms.workload: infrastructure-services
ms.custom: mvc, subject-armqs, mode-arm
ms.openlocfilehash: 66fb9a88761b47f0cc704fcf20dd29dd7768777a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487877"
---
# <a name="create-an-azure-automation-account-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure Automation アカウントを作成する

Azure Automation は、Azure 環境と非 Azure 環境を一貫性をもって管理するクラウドベースのオートメーションと構成サービスを提供します。 この記事では、Automation アカウントを作成する Azure Resource Manager テンプレート (ARM テンプレート) をデプロイする方法について説明します。 ARM テンプレートを使用すると、他のデプロイ方法より手順が減ります。 JSON テンプレートでは、環境で標準構成として使用される可能性のあるパラメーターの既定値を指定します。 このテンプレートは、組織内の共有アクセス用に Azure ストレージ アカウントに格納できます。 テンプレートの操作の詳細については、[ARM テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)に関するページを参照してください。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

サンプル テンプレートを使用すると、次の手順が実行されます。

* Azure Monitor Log Analytics ワークスペースの作成を自動化します。
* Azure Automation アカウントの作成を自動化します。
* Automation アカウントを Log Analytics ワークスペースにリンクします。
* サンプルの Automation Runbook をアカウントに追加します。

> [!NOTE]
> ARM テンプレートを使用する場合、Automation の実行アカウントの作成はサポートされません。 ポータルからまたは PowerShell を使用して実行アカウントを手動で作成するには、[実行アカウントの作成](create-run-as-account.md)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Azure Automation と Azure Monitor を初めて使用する場合は、構成の詳細を理解しておくことが重要です。 理解することで、新しい Automation アカウントにリンクされた Log Analytics ワークスペースを作成、構成、および使用するときに、エラーを回避するのに役立ちます。

* [追加の詳細](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)を確認し、アクセス制御モード、価格レベル、保有期間、容量予約レベルなどのワークスペースの構成オプションについて十分に理解します。

* [ワークスペース マッピング](how-to/region-mappings.md)を確認し、サポートされているリージョンを、インラインまたはパラメーター ファイルで指定します。 サブスクリプション内で Log Analytics ワークスペースと Automation アカウントをリンクすることは、特定のリージョンでのみサポートされています。

* Azure Monitor ログを初めて使用し、ワークスペースをまだデプロイしていない場合は、[ワークスペースの設計ガイダンス](../azure-monitor/logs/design-logs-deployment.md)を確認してください。 このドキュメントは、アクセス制御について学習し、組織に推奨される設計実装戦略を理解するのに役立ちます。

## <a name="review-the-template"></a>テンプレートを確認する

この記事で使用されているテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-automation/)からのものです。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

テンプレート内に定義されている Azure リソース:

* [**Microsoft.OperationalInsights/workspaces**](/azure/templates/microsoft.operationalinsights/workspaces): Azure Log Analytics ワークスペースを作成します。
* [**Microsoft.Automation/automationAccounts**](/azure/templates/microsoft.automation/automationaccounts): Azure Automation アカウントを作成します。
* [**Microsoft.Automation/automationAccounts/runbooks**](/azure/templates/microsoft.automation/automationaccounts/runbooks): Azure Automation アカウント Runbook を作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 下の **[Azure に配置する]** ボタンを選択して Azure にサインインし、ARM テンプレートを開きます。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

1. 次の値を入力または選択します。

    |プロパティ |説明 |
    |---|---|
    |サブスクリプション |ドロップダウン リストから Azure サブスクリプションを選択します。|
    |Resource group |ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
    |リージョン |この値は自動的に設定されます。|
    |ワークスペース名 |新しい Log Analytics ワークスペースの名前を入力します。|
    |Sku | 既定値は、2018 年 4 月の価格モデルでリリースされた 1 GB あたりの価格レベルです。 2018 年 4 月の価格モデルを選択したサブスクリプションで Log Analytics ワークスペースを作成または構成する場合、有効な Log Analytics 価格レベルは `PerGB2018` のみです。|
    |データの保持 |既定値は 30 日です。|
    |場所 |この値には、リソース グループに使用される場所が自動入力されます。|
    |Automation アカウント名 | 新しい Automation アカウントの名前を入力します。|
    |サンプルのグラフィカル Runbook 名 | 現状のままにします。|
    |サンプルのグラフィカル Runbook の説明 | 現状のままにします。|
    |サンプルの PowerShell Runbook 名 | 現状のままにします。|
    |サンプルの PowerShell Runbook の説明 | 現状のままにします。|
    |サンプルの Python2Runbook 名 |現状のままにします。|
    |サンプルの Python2Runbook の説明 |現状のままにします。|
    |_artifacts の場所 |そのままにしてください。<sup>*</sup> 成果物の場所の URI。|
    |Sas トークンとしての _artifacts の場所 | 空白のままにします。 `_artifactsLocation` にアクセスするために必要な sasToken。 付属のスクリプトを使用してテンプレートをデプロイする場合は、`sasToken` が自動的に生成されます。|

    <sup>*</sup> PowerShell、CLI から ARM テンプレート、またはテンプレート機能をポータルで実行しようとすると、`_artifactsLocation` パラメーターが正しく設定されていない場合は、次のようなエラー メッセージが表示されます。
    
    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`
    
    このエラーを防ぐには、ポータルのテンプレート機能から実行するときに、`_artifactsLocation` パラメーターに `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json` の値を指定します。
    
    PowerShell から実行するときは、このパラメーターとその値 `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json` を含めます。
    
    Azure CLI から実行するときは、このパラメーターとその値 `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json` を含めます。
    
    PowerShell または CLI の詳細については、「**テンプレートを使用する**」セクションの「[Azure Automation アカウントを作成する (microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/)」を参照してください。

1. **[確認および作成]** 、 **[作成]** の順に選択します。 デプロイが完了するまでに数分かかる場合があります。 完了すると、次の画像のように出力されます。

    ![デプロイ完了時の結果の例](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. デプロイが完了すると、 **[リソースに移動]** リンクを含む **デプロイ成功** 通知を受け取ります。 **[リソース グループ]** ページに新しいリソースが一覧表示されます。 一覧から、新しい Automation アカウントを選択します。

1. 左側の **[プロセス オートメーション]** の **[Runbook]** を選択します。 **[Runbook]** ページには、Automation アカウントを使用して作成された 3 つのサンプル Runbook が一覧表示されます。

    ![Automation アカウントで作成されたチュートリアル Runbook](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

1. 左側の **[関連リソース]** から **[Linked workspace]\(リンクされたワークスペース\)** を選択します。 **[Linked workspace]\(リンクされたワークスペース\)** ページに、先ほど指定した対象の Automation アカウントにリンクされている Log Analytics ワークスペースが表示されます。

    ![Log Analytics ワークスペースにリンクされた Automation アカウント](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="next-steps"></a>次のステップ

Runbook ジョブの状態とジョブ ストリームを、リンクされた Log Analytics ワークスペースに送信するために、Automation アカウントの[診断設定を構成します](automation-manage-send-joblogs-log-analytics.md)。
