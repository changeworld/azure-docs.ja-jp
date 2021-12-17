---
title: Azure Synapse Analytics における継続的インテグレーションとデリバリー
description: 継続的インテグレーションと継続的デリバリー (CI/CD) を使用して、Azure Synapse Analytics ワークスペース内の環境間で変更をデプロイする方法について説明します。
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3269bfed98264574d5b44759cceae6e1613eb5f8
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729249"
---
# <a name="continuous-integration-and-delivery-for-an-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics ワークスペースの継続的インテグレーションとデリバリー

継続的インテグレーション (CI) は、チーム メンバーが変更をバージョン コントロールにコミットするたびに行われるコードのビルドとテストを自動化するプロセスです。 継続的デリバリー (CD) は、複数のテスト環境またはステージング環境から運用環境にビルド、テスト、構成、デプロイを行うプロセスです。

Azure Synapse Analytics ワークスペースでは、CI/CD を使用して、すべてのエンティティをある環境 (開発、テスト、運用) から別の環境に移動します。 ワークスペースを別のワークスペースに昇格するプロセスには 2 つの部分があります。 まず、[Azure Resource Manager テンプレート (ARM テンプレート)](../../azure-resource-manager/templates/overview.md) を使用して、ワークスペースのリソース (プールとワークスペース) を作成または更新します。 次に、Azure DevOps または GitHub で Azure Synapse CI/CD ツールを使用して、SQL スクリプトとノートブック、Spark ジョブ定義、パイプライン、データセット、データ フローのような成果物を移行します。 

この記事では、Azure DevOps のリリース パイプラインと GitHub Actions を使用して、複数の環境への Azure Synapse ワークスペースのデプロイを自動化する方法について説明します。

## <a name="prerequisites"></a>必須コンポーネント

複数の環境への Azure Synapse ワークスペースのデプロイを自動化するには、次の前提条件と構成が満たされている必要があります。

### <a name="azure-devops"></a>Azure DevOps

- リリース パイプラインを実行するための Azure DevOps プロジェクトを準備します。
- リポジトリを表示できるようにするため、[コードをチェックインするユーザーに組織レベルでの Basic アクセス権を付与します](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page&preserve-view=true)。
- Azure Synapse リポジトリに対する所有者権限を付与します。
- セルフホステッド Azure DevOps VM エージェントを作成したこと、または Azure DevOps ホステッド エージェントを使用していることを確認します。
- [リソース グループの Azure Resource Manager サービス接続を作成する](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml&preserve-view=true)ためのアクセス許可を付与します。
- Azure Active Directory (Azure AD) の管理者は、[Azure DevOps 組織に Azure DevOps Synapse Workspace Deployment Agent 拡張機能をインストールする](/azure/devops/marketplace/install-extension)必要があります。
- パイプラインを実行するための既存のサービス アカウントを作成または指名します。 サービス アカウントの代わりに個人用アクセス トークンを使用できますが、そのユーザー アカウントが削除されるとパイプラインが機能しなくなります。

### <a name="github"></a>GitHub

- Azure Synapse ワークスペースの成果物とワークスペース テンプレートを含む GitHub リポジトリを作成します。 
- セルフホステッド ランナーを作成したこと、または GitHub ホスト ランナーを使用していることを確認します。

### <a name="azure-active-directory"></a>Azure Active Directory

- サービス プリンシパルを使用している場合は、Azure AD でデプロイに使用するサービス プリンシパルを作成します。 
- マネージド ID を使用している場合は、Azure の VM でシステム割り当てマネージド ID をエージェントまたはランナーとして有効にし、Synapse 管理者として Azure Synapse Studio に追加する必要があります。
- Azure AD 管理者ロール使用して、これらのアクションを完了します。

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> 次の前提条件は、同じパイプライン、ARM テンプレート、または Azure CLI を使用して自動化およびデプロイできますが、この記事ではその手順を説明していません。

- 開発に使用される "ソース" ワークスペースは、Azure Synapse Studio の Git リポジトリを使用して構成する必要があります。 詳細については、「[Azure Synapse Studio でのソース管理](source-control.md#configuration-method-2-manage-hub)」を参照してください。

- デプロイ先の空のワークスペースをセットアップします。

  1. 新しい Azure Synapse ワークスペースを作成します。
  1. 新しいワークスペースがホストされるリソース グループに、VM エージェントとサービス プリンシパルの共同作成者権限を付与します。
  1. このワークスペースでは、Git リポジトリ接続を構成しないでください。
  1. Azure portal で、新しい Azure Synapse ワークスペースを見つけて、自分と Azure DevOps パイプライン Azure Synapse ワークスペースを実行するユーザーに所有者権限を付与します。 
  1. Azure DevOps VM エージェントとサービス プリンシパルをワークスペースの共同作成者ロールに追加します。 (このロールは継承されているはずですが、継承されていることを確認します。)
  1. Azure Synapse ワークスペースで、 **[Studio]**  >  **[管理]**  >  **[アクセスの制御]** に移動します。 Azure DevOps VM エージェントとサービス プリンシパルをワークスペース管理者グループに追加します。
  1. ワークスペースに使用するストレージ アカウントを開きます。 **[ID とアクセスの管理]** ペインで、VM エージェントとサービス プリンシパルをストレージ BLOB データ共同作成者ロールに追加します。
  1. サポート サブスクリプションでキー コンテナーを作成し、既存のワークスペースと新しいワークスペースの両方にこのコンテナーに対する GET および LIST アクセス許可が少なくとも付与されるようにします。
  1. 自動デプロイを機能させるには、リンク サービスで指定されているすべての接続文字列がキー コンテナーにあることを確認します。

### <a name="other-prerequisites"></a>その他の前提条件
 
- Spark プールとセルフホステッド統合ランタイムは、ワークスペースのデプロイ タスクでは作成されません。 セルフホステッド統合ランタイムを使用するリンク サービスがある場合は、新しいワークスペースにランタイムを手動で作成します。
- 開発ワークスペースの項目が特定のプールにアタッチされている場合は、パラメーター ファイルでターゲットのワークスペースのプールに同じ名前を作成またはパラメーター化したことを確認します。  
- プロビジョニングされた SQL プールをデプロイしようとしたときに一時停止した場合、デプロイは失敗する可能性があります。

詳細については、[Azure Synapse Analytics での CI/CD パート 4 - リリース パイプライン](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)に関するページを参照してください。 


## <a name="set-up-a-release-pipeline-in-azure-devops"></a>Azure DevOps にリリース パイプラインを設定する

このセクションでは、Azure DevOps で Azure Synapse ワークスペースをデプロイする方法について説明します。 

1. [Azure DevOps](https://dev.azure.com/) で、リリース用に作成したプロジェクトを開きます。

1. 左側のメニューで、 **[パイプライン]**  >  **[リリース]** を選択します。

    :::image type="content" source="media/create-release-pipeline.png" alt-text="Azure DevOps のメニューで、[パイプライン]、[リリース] の順に選択したことを示すスクリーンショット。":::        
 
1. **[新しいパイプライン]** を選択します。 既存のパイプラインがある場合は、 **[新規]**  >  **[新しいリリース パイプライン]** を選択します。

1. **[空のジョブ]** テンプレートを選択します。

    :::image type="content" source="media/create-release-select-empty.png" alt-text="[空のジョブ] テンプレートを選択したことを示すスクリーンショット。":::

1. **[ステージ名]** に、環境の名前を入力します。

1. **[成果物の追加]** を選択し、開発環境の Azure Synapse Studio で構成した Git リポジトリを選択します。 プールとワークスペースの ARM テンプレートを管理する Git リポジトリを選択します。 ソースとして GitHub を使用する場合は、GitHub アカウントとプル リポジトリのサービス接続を作成します。 詳細については、[サービスの接続](/azure/devops/pipelines/library/service-endpoints)に関する記事を参照してください。

    :::image type="content" source="media/release-creation-github.png" alt-text="新しい成果物の発行ブランチを追加するために GitHub を選択したことを示すスクリーンショット。":::

1. リソース ARM テンプレート ブランチを選択します。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    :::image type="content" source="media/release-creation-arm-template-branch.png" lightbox="media/release-creation-arm-template-branch.png" alt-text="リソース ARM テンプレート ブランチを設定したことを示すスクリーンショット。":::

1. 成果物の [[既定のブランチ]](source-control.md#configure-publishing-settings) では、リポジトリの **発行ブランチ** を選択します。 既定では、発行ブランチは `workspace_publish` です。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    :::image type="content" source="media/release-creation-publish-branch.png" alt-text="成果物のブランチの設定を示すスクリーンショット。":::

### <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-a-resource"></a>リソースを作成および更新するための ARM テンプレートのステージ タスクを設定する 

Azure Synapse ワークスペース、Spark および SQL プール、キー コンテナーなどのリソースをデプロイするための ARM テンプレートがある場合は、それらのリソースを作成または更新するための Azure Resource Manager デプロイ タスクを追加します。

1. ステージ ビューで、 **[ステージ タスクを表示します]** を選択します。

    :::image type="content" source="media/release-creation-stage-view.png" alt-text="ステージ ビューの設定を示すスクリーン ショット。":::

1. 新しいタスクを作成します。 **[ARM テンプレートのデプロイ]** を探して **[追加]** を選択します。

1. デプロイの **[タスク]** タブでは、ワークスペースのサブスクリプション、リソース グループ、場所を選択します。 必要であれば資格情報を指定します。

1. **[アクション]** で **[リソース グループの作成または更新]** を選択します。

1. **[テンプレート]** で省略記号 ( **[...]** ) ボタンを選択します。 ワークスペースの ARM テンプレートに移動します。

1. **[テンプレート パラメーター]** で、 **[…]** を選択して パラメーター ファイルを選択します。

    :::image type="content" source="media/pools-resource-deploy.png" lightbox="media/pools-resource-deploy.png" alt-text="ワークスペースとプールのデプロイを示すスクリーンショット。":::

1. **[テンプレート パラメーターのオーバーライド]** で、 **[...]** を選択し、ワークスペースに使用するパラメーター値を入力します。 

1. **[配置モード]** で **[増分]** を選択します。

1. (省略可能) 付与のために **Azure PowerShell** を追加して、ワークスペースのロール割り当てを更新します。 リリース パイプラインを使用して Azure Synapse ワークスペースを作成する場合は、パイプラインのサービス プリンシパルが既定のワークスペース管理者として追加されます。PowerShell を実行して、他のアカウントにワークスペースへのアクセスを許可することができます。 

    :::image type="content" source="media/release-creation-grant-permission.png" lightbox="media/release-creation-grant-permission.png" alt-text="PowerShell スクリプトの実行によるアクセス許可の付与を示すスクリーンショット。":::

> [!WARNING]
> 完全なデプロイ モードでは、新しい ARM テンプレートで指定されていないリソース グループのリソースは "*削除されます*"。 詳しくは、「[Azure Resource Manager のデプロイ モード](../../azure-resource-manager/templates/deployment-modes.md)」を参照してください。

### <a name="set-up-a-stage-task-for-azure-synapse-artifacts-deployment"></a>Azure Synapse 成果物のデプロイのステージ タスクを設定する 

[Synapse ワークスペース デプロイ](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy)拡張機能を使用して、Azure Synapse ワークスペース内のその他の項目をデプロイします。 デプロイできる項目には、データセット、SQL スクリプトとノートブック、Spark ジョブ定義、データ フロー、パイプライン、リンク サービス、資格情報、統合ランタイムが含まれます。  

1. [Visual Studio Marketplace](https://marketplace.visualstudio.com/azuredevops) で拡張機能を探して取得します。

    :::image type="content" source="media/get-extension-marketplace.png" alt-text="Visual Studio Marketplace に表示された Synapse ワークスペース デプロイ拡張機能を示すスクリーンショット。":::

1. 拡張機能をインストールする Azure DevOps 組織を選択します。 

    :::image type="content" source="media/install-extension.png" alt-text="Synapse ワークスペース デプロイ拡張機能をインストールする組織の選択を示すスクリーンショット。":::

1. Azure DevOps パイプラインのサービス プリンシパルにサブスクリプションのアクセス許可が付与され、ワークスペースの Synapse ワークスペース管理者として割り当てられていることを確認します。 

1. 新しいタスクを作成するには、 **[Synapse ワークスペースのデプロイ]** を探して **[追加]** を選択します。

    :::image type="content" source="media/add-extension-task.png" alt-text="Synapse ワークスペースのデプロイを検索してタスクを作成することを示すスクリーンショット。":::

1. タスクで、 **[テンプレート]** の横にある **[…]** を選択して テンプレート ファイルを選択します。

1. **[テンプレート パラメーター]** の横にある **[…]** を選択して  パラメーター ファイルを選択します。

1. ワークスペースの接続、リソース グループ、名前を選択します。 

1. **[テンプレート パラメーターのオーバーライド]** の横にある **[…]** を選択します。 . ワークスペースに使用するパラメーター値を入力します (リンク サービスで使用される接続文字列とアカウント キーを含む)。 詳細については、[Azure Synapse Analytics の CI/CD](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434) に関する記事をご覧ください。

    :::image type="content" source="media/create-release-artifacts-deployment.png" lightbox="media/create-release-artifacts-deployment.png" alt-text="ワークスペースの Synapse デプロイ タスクの設定を示すスクリーンショット。":::

> [!IMPORTANT]
> CI/CD のシナリオでは、各環境内の統合ランタイム (IR) の種類は同じである必要があります。 たとえば、開発環境にセルフホステッド統合ランタイムがある場合、テストや運用などの他の環境環境でも、同じ統合ランタイムをセルフホステッドする必要があります。 同様に、複数のステージ間で統合ランタイムを共有している場合は、開発、テスト、運用など、すべての環境で、統合ランタイムをリンクおよびセルフホステッドする必要があります。

### <a name="create-a-release-for-deployment"></a>デプロイのリリースを作成する 

すべての変更を保存したら、 **[リリースの作成]** を選択してリリースを手動で作成できます。 リリースの作成を自動化する方法の詳細については、[Azure DevOps のリリース トリガー](/azure/devops/pipelines/release/triggers)に関するページを参照してください。

:::image type="content" source="media/release-creation-manually.png" lightbox="media/release-creation-manually.png" alt-text="[新しいリリース パイプライン] ペインが表示されているスクリーンショット。[リリースの作成] が強調表示されています。":::

## <a name="set-up-a-release-in-github-actions"></a>GitHub Actions でリリースを設定する 

このセクションでは、Azure Synapse ワークスペースのデプロイに GitHub Actions を使用して GitHub ワークフローを作成する方法について説明します。

[Azure Resource Manager テンプレートの GitHub Actions](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) を使用して、ワークスペースとコンピューティング プールのための ARM テンプレートの Azure へのデプロイを自動化できます。

### <a name="workflow-file"></a>ワークフロー ファイル

GitHub Actions ワークフローは、お使いのリポジトリの */.github/workflows/* パスの YAML (.yml) ファイルで定義します。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

この .yml ファイルには 2 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルを定義します。 <br /> 2.GitHub シークレットを作成します。 |
|**デプロイする** | ワークスペースの成果物をデプロイします。 |

### <a name="configure-github-actions-secrets"></a>GitHub Actions のシークレットを構成する

GitHub Actions のシークレットは、暗号化された環境変数です。 このリポジトリに対するコラボレーター権限を持つユーザーは、これらのシークレットを使用して、リポジトリ内のアクションを操作できます。

1. GitHub リポジトリで、 **[Settings]\(設定\)** タブを選択し、 **[Secrets]\(シークレット\)**  >  **[New repository secret]\(新しいリポジトリ シークレット\)** を選択します。

    :::image type="content" source="media/create-secret-new.png" lightbox="media/create-secret-new.png" alt-text="新しいリポジトリ シークレットを作成するために選択する GitHub の要素を示すスクリーンショット。":::

1. クライアント ID の新しいシークレットを追加し、デプロイにサービス プリンシパルを使用する場合は、新しいクライアント シークレットを追加します。 サブスクリプション ID とテナント ID をシークレットとして保存することもできます。 

### <a name="add-your-workflow"></a>ワークフローを追加する

GitHub リポジトリで、 **[Actions]\(アクション\)** に移動します。 

1. **[Set up a workflow yourself]\(ワークフローを自分でセットアップする\)** を選択します。
1. ワークフロー ファイルで、`on:` セクションの後にあるすべてのものを削除します。 たとえば、残りのワークフローは次の例のようになります。 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. ワークフローの名前を変更します。 **[Marketplace]\(マーケットプレース\)** タブで Synapse ワークスペースのデプロイ アクションを検索して、アクションを追加します。 

    :::image type="content" source="media/search-action.png" lightbox="media/search-action.png" alt-text="[Marketplace]\(マーケットプレース\) タブでの Synapse ワークスペースのデプロイ タスクの検索を示すスクリーンショット。":::

1. 必要な値とワークスペース テンプレートを設定します。

    ```yaml
    name: workspace deployment

    on:
        push:
            branches: [ publish_branch ]
    jobs:
        release:
            # You also can use the self-hosted runners.
            runs-on: windows-latest
            steps:
            # Checks out your repository under $GITHUB_WORKSPACE, so your job can access it.
            - uses: actions/checkout@v2
            - uses: azure/synapse-workspace-deployment@release-1.0
            with:
              TargetWorkspaceName: 'target workspace name'
              TemplateFile: './path of the TemplateForWorkspace.json'
              ParametersFile: './path of the TemplateParametersForWorkspace.json'
              OverrideArmParameters: './path of the parameters.yaml'
              environment: 'Azure Public'
              resourceGroup: 'target workspace resource group'
              clientId: ${{secrets.CLIENTID}}
              clientSecret:  ${{secrets.CLIENTSECRET}}
              subscriptionId: 'subscriptionId of the target workspace'
              tenantId: 'tenantId'
              DeleteArtifactsNotInTemplate: 'true'
              managedIdentity: 'False'
    ``` 

1. 変更をコミットする準備ができました。 **[Start commit]\(コミットの開始\)** を選択し、タイトルを入力して、説明 (省略可能) を追加します。 次に、 **[Commit new file]\(新しいファイルのコミット\)** を選択します。

    :::image type="content" source="media/commit-workflow.png" lightbox="media/commit-workflow.png" alt-text="GitHub でのワークフローのコミットを示すスクリーンショット。":::

   ファイルは、リポジトリの *.github/workflows* フォルダーに表示されます。

   > [!NOTE]
   > マネージド ID は、Azure 上のセルフホステッド VM でのみサポートされています。 ランナーをセルフホステッドに設定してください。 VM に対してシステム割り当てマネージド ID を有効にし、Synapse 管理者として Azure Synapse Studio に追加します。

### <a name="review-your-deployment"></a>デプロイを確認する

1. GitHub リポジトリで、 **[Actions]\(アクション\)** に移動します。
1. ワークフローの実行の詳細なログを表示するには、最初の結果を開きます。

    :::image type="content" source="media/review-deploy-status.png" lightbox="media/review-deploy-status.png" alt-text="GitHub のリポジトリの [Actions]\(アクション\) でワークスペースのデプロイ ログを選択したことを示すスクリーンショット。":::

## <a name="create-custom-parameters-in-the-workspace-template"></a>ワークスペース テンプレートのカスタム パラメーターを作成する 

自動 CI/CD を使用していて、デプロイ時に一部のプロパティを変更するが、既定ではプロパティがパラメーター化されない場合は、既定のパラメーター テンプレートをオーバーライドできます。

既定のパラメーター テンプレートをオーバーライドするには、Git コラボレーション ブランチのルート フォルダーに *template-parameters-definition.json* という名前のカスタム パラメーター テンプレートを作成します。 このとおりのファイル名を使用する必要があります。 Azure Synapse ワークスペースでは、コラボレーション ブランチから発行すると、このファイルを読み取り、その構成を利用してパラメーターが生成されます。 Azure Synapse ワークスペースでそのファイルが見つからない場合は、既定のパラメーター テンプレートが使用されます。

### <a name="custom-parameter-syntax"></a>カスタムのパラメーター構文

次のガイドラインを使用して、カスタム パラメーター ファイルを作成できます。

* 関連するエンティティ型の下にプロパティ パスを入力します。
* プロパティ名を `*` に設定すると、そのプロパティの下にあるすべてのプロパティ (再帰的にではなく、最初のレベルまでのみ) をパラメーター化することを指示します。 この構成には例外を設定できます。
* プロパティの値を文字列として設定すると、プロパティをパラメーター化することを指示します。 「`<action>:<name>:<stype>`」の形式を使用します。
   *  `<action>` には、次のいずれかの文字を指定できます。
      * `=` は、パラメーターの既定値として現在の値を保持することを意味します。
      * `-` は、パラメーターの既定値を保持しないことを意味します。
      * `|` は、接続文字列またはキーに対する Azure Key Vault からのシークレットの特殊なケースです。
   * `<name>` は、パラメーターの名前です。 空白の場合は、プロパティの名前になります。 値が `-` 文字で始まる場合、名前は短縮されます。 たとえば、`AzureStorage1_properties_typeProperties_connectionString` は `AzureStorage1_connectionString` に短縮されます。
   * `<stype>` は、パラメーターの型です。 `<stype>` が空白の場合、既定の型は `string` です。 サポートされる値は `string`、`securestring`、`int`、`bool`、`object`、`secureobject`、および `array` です。
* ファイルに配列を指定すると、テンプレート内の一致するプロパティが配列であることを指示します。 Azure Synapse では、指定された定義を使用して、配列内のすべてのオブジェクトを反復処理します。 2 番目のオブジェクトである文字列は、各反復処理のパラメーターの名前として使用されるプロパティ名です。
* 定義をリソース インスタンスに固有にすることはできません。 定義はその型のすべてのリソースに適用されます。
* 既定では、(Key Vault シークレットなどの) セキュリティで保護されたすべての文字列と、(接続文字列、キー、トークンなどの) セキュリティで保護された文字列がパラメーター化されます。

### <a name="parameter-template-definition-example"></a>パラメーター テンプレート定義の例 

ここに、パラメーター テンプレート定義の例を示します。

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

ここでは、前のテンプレートの記述方法について、リソースの種類別に説明します。

**`notebooks`**

- パス `properties/bigDataPool/referenceName` 内のすべてのプロパティは、既定値を使用してパラメーター化されます。 各ノートブック ファイルに対して、アタッチされた Spark プールをパラメーター化できます。 

**`sqlscripts`**

- パス `properties/content/currentConnection` で、`poolName` および `databaseName` プロパティは、テンプレートに既定値を指定せずに文字列としてパラメーター化されます。 

**`pipelines`**

- パス `activities/typeProperties/waitTimeInSeconds` 内のすべてのプロパティがパラメーター化されます。 `waitTimeInSeconds` という名前のコードレベルのプロパティを持つパイプライン内のアクティビティ (たとえば、`Wait` アクティビティ) はすべて、既定の名前を使用して数値としてパラメーター化されます。 このプロパティには、Resource Manager テンプレートの既定値は設定されません。 代わりに、Resource Manager のデプロイ時にプロパティを入力する必要があります。
- (たとえば `Web` アクティビティ内の) `headers` プロパティは、`object` 型 (オブジェクト) でパラメーター化されます。 `headers` プロパティには、ソース ファクトリと同じ値である既定値があります。

**`integrationRuntimes`**

- パス `typeProperties` のすべてのプロパティは、それぞれの既定値を使用してパラメーター化されます。 たとえば、`IntegrationRuntimes` 型のプロパティの下には `computeProperties` と `ssisProperties` という 2 つのプロパティがあります。 両方のプロパティの型は、それぞれの既定の値と型 (Object) で作成されます。

**`triggers`**

- `typeProperties` の下では、2 つのプロパティがパラメーター化されます。
  - `maxConcurrency` プロパティには既定値があり、`string` 型です。 `maxConcurrency` プロパティの既定のパラメーター名は `<entityName>_properties_typeProperties_maxConcurrency` です。
  - `recurrence` プロパティもパラメーター化されます。 `recurrence` プロパティの下にあるすべてのプロパティは、既定の値とパラメーター名を持つ文字列としてパラメーター化するように設定されます。 例外は、`int` 型としてパラメーター化される `interval` プロパティです。 パラメーター名の末尾には `<entityName>_properties_typeProperties_recurrence_triggerSuffix` が付きます。 同様に、`freq` プロパティは文字列であり、文字列としてパラメーター化されます。 ただし、`freq` プロパティは既定値なしでパラメーター化されます。 名前は短縮され、`<entityName>_freq` などのサフィックスが付けられます。

**`linkedServices`**

- リンクされたサービスは一意です。 リンクされたサービスとデータセットにはさまざまな型があるため、型固有のカスタマイズを行うことができます。 前の例では、`AzureDataLakeStore` 型のすべてのリンク サービスに対して、特定のテンプレートが適用されます。 他のすべて (`*` 文字を使用して識別されます) には、別のテンプレートが適用されます。
- `connectionString` プロパティは、`securestring` 値としてパラメーター化されます。 既定値はありません。 パラメーター名は短縮され、末尾に `connectionString` が付けられます。
- `secretAccessKey` プロパティは (たとえば、Amazon S3 のリンク サービスでは) `AzureKeyVaultSecret` 値としてパラメーター化されます。 このプロパティは、Azure Key Vault シークレットとして自動的にパラメーター化され、構成済みのキー コンテナーからフェッチされます。 キー コンテナー自体もパラメーター化できます。

**`datasets`**

- データセット内の型をカスタマイズすることもできますが、明示的な \* レベルの構成は必要ありません。 前の例では、`typeProperties` の下にあるすべてのデータセット プロパティがパラメーター化されます。

## <a name="best-practices-for-cicd"></a>CI/CD のベスト プラクティス

Azure Synapse ワークスペースで Git 統合を使用していて、変更を開発からテストを経て運用環境に移行する CI/CD パイプラインがある場合は、次のベスト プラクティスをお勧めします。

-   **開発ワークスペースのみを Git と統合します**。 Git 統合を使用する場合は、"*開発*" Azure Synapse ワークスペースのみを Git と統合します。 テスト ワークスペースと運用ワークスペースへの変更は CI/CD を介してデプロイされるので、Git 統合は必要ありません。
-   **成果物を移行する前にプールを準備します**。 SQL スクリプトまたはノートブックが開発ワークスペース内のプールにアタッチされている場合は、他の環境のプールに同じ名前を使用します。 
-   **コードとしてのインフラストラクチャのシナリオでバージョン管理を同期する**。 インフラストラクチャ (ネットワーク、仮想マシン、ロード バランサー、接続トポロジ) を記述型モデルで管理するには、DevOps チームがソース コードに使用するものと同じバージョン管理を使用します。 
-   **Azure Data Factory のベスト プラクティスを確認してください**。 Data Factory を使用する場合は、[Data Factory の成果物のベスト プラクティス](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)に関するページを参照してください。

## <a name="troubleshoot-artifacts-deployment"></a>成果物のデプロイに関するトラブルシューティング 

### <a name="use-the-synapse-workspace-deployment-task"></a>Synapse ワークスペース デプロイ タスクを使用する

Azure Synapse では、Data Factory とは異なり、一部の成果物は Resource Manager リソースではありません。 ARM テンプレート デプロイ タスクを使用して Azure Synapse の成果物をデプロイすることはできません。 代わりに、Synapse ワークスペース デプロイ タスクを使用します。
 
### <a name="unexpected-token-error-in-release"></a>リリースにおける予期しないトークン エラー

パラメーター ファイルにエスケープされていないパラメーター値がある場合、リリース パイプラインでファイルの解析に失敗し、`unexpected token` エラーが生成 されます。 パラメーターをオーバーライドすること、または Key Vault を使用してパラメーター値を取得することをお勧めします。 また、二重エスケープ文字を使用して問題を解決することもできます。
