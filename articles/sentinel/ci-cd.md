---
title: リポジトリからカスタム コンテンツをデプロイする
description: この記事では、カスタム コンテンツを保存して Microsoft Sentinel にデプロイできる GitHub または Azure DevOps リポジトリとの接続を作成する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0b321f268f74c020b35e79d44e39a66e53337642
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724503"
---
# <a name="deploy-custom-content-from-your-repository-public-preview"></a>リポジトリからカスタム コンテンツをデプロイする (パブリック プレビュー)

> [!IMPORTANT]
>
> Microsoft Sentinel **リポジトリ** ページは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Microsoft Sentinel の "*コンテンツ*" は、Microsoft Sentinel で取り込み、監視、アラート、ハンティングなどを行うお客様を支援するセキュリティ情報およびイベント管理 (SIEM) です。 たとえば、Microsoft Sentinel のコンテンツにはデータ コネクタ、パーサー、ブック、分析ルールなどがあります。 詳細については、「[Microsoft Sentinel のコンテンツとソリューションについて](sentinel-solutions.md)」を参照してください。

Microsoft Sentinel コンテンツ ハブで提供されている、すぐに利用できる (組み込み) コンテンツを使用して、独自のニーズに合わせてカスタマイズしたり、独自のカスタム コンテンツを最初から作成したりできます。

カスタム コンテンツを作成したら、独自の Microsoft Sentinel ワークスペース、または GitHub や Azure DevOps のリポジトリを含む外部ソース管理リポジトリにそれを格納して管理できます。 この記事では、Microsoft Sentinel と外部ソース管理リポジトリ間の接続を作成および管理する方法について説明します。 外部リポジトリでコンテンツを管理することで、Microsoft Sentinel の外部でコンテンツを更新し、ワークスペースに自動的にデプロイさせることができます。

> [!TIP]
> この記事では、特定の種類のコンテンツを最初から作成する方法については説明 *しません*。 詳細については、各コンテンツ タイプに関する関連の [Microsoft Sentinel GitHub wiki](https://github.com/Azure/Azure-Sentinel/wiki#get-started) を参照してください。
>

## <a name="prerequisites-and-scope"></a>前提条件とスコープ

Microsoft Sentinel ワークスペースを外部ソース管理リポジトリに接続する前に、次があることを確認してください。

- 関連する [Azure Resource Manager (ARM) テンプレート](../azure-resource-manager/templates/index.yml)で、ワークスペースにデプロイするカスタム コンテンツ ファイルを含む GitHub または Azure DevOps リポジトリへのアクセス。

    Microsoft Sentinel では、現在、GitHub および Azure DevOps のリポジトリとの接続のみがサポートされています。

- Microsoft Sentinel ワークスペースを含むリソース グループの **所有者** ロール。 Microsoft Sentinel とソース管理リポジトリ間の接続を作成するために、**所有者** ロールが必要です。

### <a name="maximum-connections-and-deployments"></a>最大接続数とデプロイ数

- 現在、Microsoft Sentinel ワークスペースは **5 つの接続** に制限されています。

- 各 Azure リソース グループは、そのデプロイ履歴で **800 デプロイ** に制限されます。 リソース グループに大量の ARM テンプレート デプロイがある場合は、`Deployment QuotaExceeded` エラーが表示される場合があります。 詳細については、Azure Resource Manager テンプレート ドキュメントの「[DeploymentQuotaExceeded](/azure/azure-resource-manager/templates/deployment-quota-exceeded)」を参照してください。

### <a name="validate-your-content"></a>コンテンツの検証

リポジトリ接続経由で、Microsoft Sentinel にコンテンツをデプロイすると、データが正しい ARM テンプレート形式であることが検証される以外に、そのコンテンツは検証されません。

通常の検証プロセスを使用して、コンテンツ テンプレートを検証することが推奨されます。 [Microsoft Sentinel GitHub 検証プロセス](https://github.com/Azure/Azure-Sentinel/wiki#test-your-contribution)とツールを利用して、独自の検証プロセスを設定できます。

## <a name="connect-a-repository"></a>リポジトリの接続

この手順では、GitHub または Azure DevOps リポジトリを Microsoft Sentinel ワークスペースに接続する方法について説明します。Microsoft Sentinel ではなく、そこでカスタム コンテンツを保存して管理できます。

各接続では、分析ルール、自動化ルール、ハンティング クエリ、パーサー、プレイブック、ブックなど、多くの種類のカスタム コンテンツをサポートできます。 詳細については、「[Microsoft Sentinel のコンテンツとソリューションについて](sentinel-solutions.md)」を参照してください。

**接続を作成するには**:

1. 接続に使用する資格情報で、ソース管理アプリにサインインしていることを確認します。  現在、別の資格情報を使用してサインインしている場合は、まずサインアウトします。

1. Microsoft Sentinel で、左側の **[コンテンツ管理]** の下の **[リポジトリ]** を選択します。

1. **[新規追加]** を選択し、 **[新しい接続の作成]** ページで、接続のわかりやすい名前と説明を入力します。

1. **[ソース管理]** ドロップダウンから、接続先のリポジトリの種類を選択し、 **[承認]** を選択します。

1. 接続の種類に応じて、次のいずれかのタブを選択します。

    # <a name="github"></a>[GitHub](#tab/github)

    1. 入力を求められたら、GitHub 資格情報を入力します。

        接続を初めて追加すると、新しいブラウザー ウィンドウまたはタブが表示され、Microsoft Sentinel への接続を認可するように求められます。 同じブラウザーで GitHub アカウントに既にログインしている場合は、GitHub 資格情報が自動的に設定されます。

    1. **[リポジトリ]** 領域が **[新しい接続の作成]** ページに表示され、そこで接続先の既存のリポジトリを選択できます。 一覧からリポジトリを選択し、 **[リポジトリの追加]** を選択します。

        特定のリポジトリに初めて接続すると、新しいブラウザー ウィンドウまたはタブが表示され、リポジトリに **Azure-Sentinel** アプリをインストールするように求められます。 複数のリポジトリがある場合は、**Azure-Sentinel** アプリをインストールするリポジトリを選択してインストールします。

        GitHub に移動し、アプリのインストールを続行します。

    1. **Azure-Sentinel** アプリがリポジトリにインストールされると、 **[新しい接続の作成]** ページの **[ブランチ]** ドロップダウンにブランチが設定されます。 Microsoft Sentinel ワークスペースに接続するブランチを選択します。

    1. **[コンテンツの種類]** ドロップダウンから、デプロイするコンテンツの種類を選択します。

        - パーサーとハンティング クエリの両方で **Saved Searches** API を使用してコンテンツを Microsoft Sentinel にデプロイします。 これらのコンテンツの種類のいずれかを選択し、ブランチに他の種類のコンテンツも含まれる場合は、両方のコンテンツの種類がデプロイされます。

        - 他のすべてのコンテンツ タイプについては、 **[新しい接続の作成]** ウィンドウでコンテンツ タイプを選択すると、そのコンテンツだけが Microsoft Sentinel にデプロイされます。 他の種類のコンテンツはデプロイされません。

    1. **[作成]** を選択して接続を作成します。 たとえば、次のように入力します。

        :::image type="content" source="media/ci-cd/create-new-connection-github.png" alt-text="新しい GitHub リポジトリ接続のスクリーンショット。":::

    # <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

    > [!NOTE]
    > Azure DevOps 接続を作成するとき、[ゲストユーザー](../active-directory/external-identities/what-is-b2b.md)は、自分の Azure Active Directory テナントにないワークスペースに現在接続できません。 このようなクロステナント シナリオは、Azure DevOps 接続ではまだサポートされていません。
    >

    現在の Azure 資格情報を使用して、Azure DevOps に自動的に承認されます。 有効な接続を確保するには、Microsoft Sentinel から接続しているのと[同じ Azure DevOps アカウントが承認されていることを確認する](https://aex.dev.azure.com/)か、InPrivate ブラウザーウィンドウを使用して接続を作成します。
    
    1.  Microsoft Sentinel で、表示されるドロップダウン リストから、 **[組織]** 、 **[プロジェクト]** 、 **[リポジトリ]** 、 **[ブランチ]** 、 **[コンテンツ タイプ]** を選択します。

        - パーサーとハンティング クエリの両方で **Saved Searches** API を使用してコンテンツを Microsoft Sentinel にデプロイします。 これらのコンテンツの種類のいずれかを選択し、ブランチに他の種類のコンテンツも含まれる場合は、両方のコンテンツの種類がデプロイされます。

        - 他のすべてのコンテンツ タイプについては、 **[新しい接続の作成]** ウィンドウでコンテンツ タイプを選択すると、そのコンテンツだけが Microsoft Sentinel にデプロイされます。 他の種類のコンテンツはデプロイされません。

    1. **[作成]** を選択して接続を作成します。 たとえば、次のように入力します。

        :::image type="content" source="media/ci-cd/create-new-connection-devops.png" alt-text="新しい GitHub リポジトリ接続のスクリーンショット。":::

    ---

    > [!NOTE]
    > 同じリポジトリとブランチがある重複する接続を 1 つの Microsoft Sentinel ワークスペースに作成することはできません。
    >

接続が作成されると、リポジトリに新しいワークフローまたはパイプラインが生成され、リポジトリに格納されているコンテンツが Microsoft Sentinel ワークスペースにデプロイされます。

デプロイの時間は、デプロイするコンテンツの量によって異なる場合があります。 

### <a name="view-the-deployment-status"></a>デプロイの状態を表示する

- **GitHub 内**: リポジトリの **[アクション]** タブ上。そこに表示されているワークフロー **.yaml** ファイルを選択して、詳細なデプロイ ログと特定のエラー メッセージ (関連する場合) にアクセスします。
- **Azure DevOps 内**: リポジトリの **[パイプライン]** タブ上。

デプロイの完了後:

- リポジトリに格納されているコンテンツは、Microsoft Sentinel ワークスペースの関連する Microsoft Sentinel ページに表示されます。

- **[リポジトリ]** ページの接続の詳細が、接続のデプロイ ログへのリンクで更新されます。 たとえば、次のように入力します。

    :::image type="content" source="media/ci-cd/deployment-logs-link.png" alt-text="GitHub リポジトリ接続のデプロイ ログのスクリーンショット。":::

### <a name="customize-the-deployment-workflow"></a>デプロイ ワークフローのカスタマイズ

既定のコンテンツ デプロイでは、そのブランチ内の何かに対してプッシュが行われるたびに、接続されているリポジトリ ブランチから関連するすべてのカスタム コンテンツがデプロイされます。

GitHub または Azure DevOps からのコンテンツ デプロイの既定の構成がすべての要件を満たしていない場合は、ニーズに合わせてエクスペリエンスを変更できます。

たとえば、異なるデプロイ トリガーを構成したり、特定のルート フォルダーからのコンテンツのみをデプロイしたりすることができます。

接続の種類に応じて、次のいずれかのタブを選択します。

# <a name="github"></a>[GitHub](#tab/github)

**GitHub デプロイ ワークフローをカスタマイズするには**:

1. GitHub で、リポジトリに移動し、`.github/workflows` ディレクトリでワークフローを見つけます。

    ワークフロー名はワークフロー ファイルの最初の行に表示され、既定の名前付け規則は `Deploy Content to <workspace-name> [<deployment-id>]` になります。

    例: `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. ページの右上にある鉛筆ボタンを選択して編集のためにファイルを開き、次のようにデプロイを変更します。

    - **デプロイ トリガーを変更するには**、コードの `on` セクションを更新します。ここには、ワークフローの実行をトリガーするイベントが記述されています。

        既定で、この構成は `on: push` に設定されています。つまり、既存のコンテンツへの変更とリポジトリへの新しいコンテンツの追加の両方を含め、接続されたブランチへのすべてのプッシュ時にワークフローがトリガーされます。 たとえば、次のように入力します。

        ```yml
        on:
            push:
                branches: [ main ]
                paths:
                - `**`
                - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
                - `.github/workflows/sentinel-deploy-<deployment-id>.yml`
        ```

        たとえば、ワークフローを定期的に実行するようにスケジュールしたり、異なるワークフロー イベントを組み合わせたりするために、これらの設定を変更する必要がある場合があります。

        詳細については、ワークフロー イベントの構成に関する [GitHub ドキュメント](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows#configuring-workflow-events)を参照してください。

    - **デプロイ パスを変更するには**:

        `on` セクションについての上記の既定の構成では、`paths` セクションの最初の行にあるワイルドカード (`**`) は、ブランチ全体がデプロイ トリガーのパスに含まれていることを示しています。

        この既定の構成は、コンテンツがブランチの任意の部分にプッシュされるといつでもデプロイ ワークフローがトリガーされることを意味します。

        ファイルの後半の `jobs` セクションには、既定の構成 `directory: '${{ github.workspace }}'` が含まれています。 この行は、フォルダー パスをフィルターせずに、 GitHub ブランチ全体がコンテンツ デプロイのパスに含まれることを示しています。

        特定のフォルダー パスのコンテンツのみをデプロイするには、それを `paths` と `directory` の両方の構成に追加します。 たとえば、`SentinelContent` という名前のルート フォルダーからのコンテンツのみをデプロイするには、次のようにコードを更新します。

        ```yml
        paths:
        - `SentinelContent/**`
        - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
        - `.github/workflows/sentinel-deploy-<deployment-id>.yml`

        ...
            directory: '${{ github.workspace }}/SentinelContent'
        ```

詳細については、GitHub Actions と GitHub ワークフローの編集に関する [GitHub ドキュメント](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#onpushpull_requestpaths)を参照してください。

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

**Azure DevOps デプロイ パイプラインをカスタマイズするには**:

1. Azure DevOps で、リポジトリに移動し、`.sentinel` ディレクトリ内のパイプライン定義ファイルを見つけます。

    パイプライン名はパイプライン ファイルの最初の行に表示され、既定の名前付け規則 `Deploy Content to <workspace-name> [<deployment-id>]` があります。

    例: `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. ページの右上にある鉛筆ボタンを選択して編集のためにファイルを開き、次のようにデプロイを変更します。

    - **デプロイ トリガーを変更するには**、コードの `trigger` セクションを更新します。ここには、ワークフローの実行をトリガーするイベントが記述されています。

        既定で、この構成は、既存のコンテンツへの変更とリポジトリへの新しいコンテンツの追加の両方を含め、接続されたブランチへのすべてのプッシュを検出するように設定されています。

        このトリガーを、スケジュール設定やプル要求トリガーなど、使用可能な任意の Azure DevOps トリガーに変更します。 詳細については、[Azure DevOps トリガーのドキュメント](/azure/devops/pipelines/yaml-schema)を参照してください。

    - **デプロイ パスを変更するには**:

        `trigger` セクションの既定の構成には次のコードが含まれます。これは、`main` ブランチがデプロイ トリガーのパスに含まれることを示しています。

        ```yml
        trigger:
            branches:
                include:
                - main
        ```

        この既定の構成は、コンテンツが `main` ブランチの任意の部分にプッシュされるといつでもデプロイ パイプラインがトリガーされることを意味します。

        特定のフォルダー パスのコンテンツのみをデプロイするには、必要に応じて、フォルダー名を `include` セクション、トリガーの場合、`steps` セクション、デプロイ パスの場合、以下に追加します。

        たとえば、`main` ブランチの `SentinelContent` という名前のルート フォルダーからのコンテンツのみをデプロイするには、次のように `include` と `workingDirectory` の設定をコードに追加します。

        ```yml
        paths:
            exclude:
            - .sentinel/*
            include:
            - .sentinel/sentinel-deploy-39d8ekc8-397-5963-49g8-5k63k5953829.yml
            - SentinelContent
        ....
        steps:
        - task: AzurePowerShell@5
          inputs:
            azureSubscription: `Sentinel_Deploy_ServiceConnection_0000000000000000`
            workingDirectory: `SentinelContent`
        ```

詳細については、Azure DevOps YAML スキーマに関する [Azure DevOps ドキュメント](/azure/devops/pipelines/yaml-schema)を参照してください。

---

> [!IMPORTANT]
> GitHub と Azure DevOps の両方で、トリガー パスとデプロイ パスのディレクトリの一貫性を維持してください。
>
## <a name="edit-or-delete-content-in-your-repository"></a>リポジトリ内のコンテンツを編集または削除する

ソース管理リポジトリへの接続を正常に作成すると、そのリポジトリ内のコンテンツが変更または追加されるたびに、デプロイ ワークフローが再度実行され、リポジトリ内のすべてのコンテンツが、接続されているすべての Microsoft Sentinel ワークスペースにデプロイされます。

Microsoft Sentinel 内ではなく、リポジトリ内の接続されているリポジトリに格納されているコンテンツ "*のみ*" 編集することが推奨されます。 たとえば、分析ルールを変更するには、GitHub または Azure DevOps で直接行います。

Microsoft Sentinel でコンテンツを編集した場合は、次回にリポジトリ コンテンツがワークスペースにデプロイされたときに、変更が上書きされるのを防ぐために、必ずソース管理リポジトリにエクスポートしてください。

コンテンツを削除する場合は、リポジトリと Azure portal の両方からそれを削除してください。 リポジトリからコンテンツを削除しても、Microsoft Sentinel ワークスペースから削除されません。

## <a name="remove-a-repository-connection"></a>リポジトリ接続を削除する

この手順では、Microsoft Sentinel からソース管理リポジトリへの接続を削除する方法について説明します。

**接続を削除するには**:

1. Microsoft Sentinel で、左側の **[コンテンツ管理]** の下の **[リポジトリ]** を選択します。
1. グリッドで、削除する接続を選択し、 **[削除]** を選択します。
1. **[はい]** を選択して、削除を確認します。

接続を削除後、接続によって以前にデプロイされたコンテンツは、Microsoft Sentinel ワークスペースに残ります。 接続の削除後にリポジトリに追加されたコンテンツはデプロイされません。

> [!TIP]
> 接続を削除するときに問題またはエラー メッセージを検出した場合は、ソース管理を確認して、接続に関連付けられていた GitHub ワークフローまたは Azure DevOps パイプラインが削除されたことを確認することが推奨されます。
>

### <a name="removing-the-microsoft-sentinel-app-from-your-github-repository"></a>GitHub リポジトリからの Microsoft Sentinel アプリの削除

GitHub リポジトリから Microsoft Sentinel アプリを削除する場合は、"*最初に*" Microsoft Sentinel **リポジトリ** ページから関連付けられているすべての接続を削除することが推奨されます。

各 Microsoft Sentinel アプリのインストールには、接続の追加と削除時の両方で使用される一意の ID があります。 ID が見つからない場合、または変更されている場合は、Microsoft Sentinel **リポジトリ** ページからの接続の削除と、GitHub リポジトリからの手動でのワークフロー削除の両方を行って、今後のコンテンツのデプロイを防ぐ必要があります。

## <a name="next-steps"></a>次の手順

Microsoft Sentinel で、すぐ利用できるコンテンツを使用する場合と同じように、カスタム コンテンツを使用します。

詳細については、次を参照してください。

- [Microsoft Sentinel ソリューションを検出してデプロイする (パブリック プレビュー)](sentinel-solutions-deploy.md)
- [Microsoft Sentinel データ コネクタ](connect-data-sources.md)
- [Advanced SIEM Information Model (ASIM) パーサー (パブリック プレビュー)](normalization-about-parsers.md)
- [収集されたデータを視覚化する](get-visibility.md)
- [脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)
- [Microsoft Sentinel を使用して脅威を追求する](hunting.md)
- [Microsoft Sentinel ウォッチリストを使用する](watchlists.md)
- [Microsoft Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md)