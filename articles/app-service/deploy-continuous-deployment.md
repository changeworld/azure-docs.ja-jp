---
title: 継続的なデプロイを構成する
description: GitHub、Bitbucket、Azure Repos、またはその他のリポジトリから Azure App Service への CI/CD を有効にする方法について説明します。 ニーズに合ったビルド パイプラインを選択します。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bff93e0dcfb49871cefa68d16d7a7db85c0f69ce
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122638381"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service への継続的デプロイ

[Azure App Service](overview.md) により、最新の更新プログラムをプルすることで、[GitHub](https://help.github.com/articles/create-a-repo)、[Bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)、[Azure Repos](/azure/devops/repos/git/creatingrepo) のリポジトリからの継続的配置が可能になります。

> [!NOTE]
> Azure portal の **[開発センター (クラシック)]** ページ (デプロイ機能の以前のバージョン) は、2021 年 3 月に非推奨となりました。 この変更はアプリの既存のデプロイ設定には影響せず、ポータルの **[デプロイ センター]** ページで引き続きアプリのデプロイを管理できます。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-the-deployment-source"></a>デプロイ ソースを構成する

1. [Azure portal](https://portal.azure.com) で、App Service アプリの管理ページに移動します。

1. 左側のウィンドウで、 **[デプロイ センター]** を選択します。 次に、 **[設定]** を選択します。 

1. **[ソース]** ボックスで、いずれかの CI/CD オプションを選択します。

    ![デプロイ ソースの選択方法を示すスクリーンショット。](media/app-service-continuous-deployment/choose-source.png)

続行するには、ビルド プロバイダーに対応するタブを選択します。

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) は、既定のビルド プロバイダーです。 プロバイダーを変更するには、 **[プロバイダーの変更]**  >  **[App Service のビルド サービス]** (Kudu) > **[OK]** の順に選択します。

    > [!NOTE]
    > Azure Pipelines を App Service アプリのビルド プロバイダーとして使用するには、Azure Pipelines から直接 CI/CD を構成します。 App Service では構成しないでください。 **Azure Pipelines** のオプションは、正しい方向を示すだけです。

1. 初めて GitHub からデプロイする場合は、 **[承認]** を選択し、承認のプロンプトに従います。 別のユーザーのリポジトリからデプロイする場合は、 **[アカウントの変更]** を選択します。

1. GitHub で Azure アカウントを承認したら、 **[組織]** 、 **[リポジトリ]** 、 **[ブランチ]** の順に選択して CI/CD を構成します。 組織またはリポジトリが見つからない場合は、GitHub でさらにアクセス許可を有効にしなければならないことがあります。 詳細については、「[Organization のリポジトリに対するアクセスを管理する](https://docs.github.com/organizations/managing-access-to-your-organizations-repositories)」を参照してください。

1. GitHub Actions がビルド プロバイダーとして選択されている場合は、 **[ランタイム スタック]** および **[バージョン]** ドロップダウン リストで目的のワークフロー ファイルを選択できます。 Azure では、選択された GitHub リポジトリにこのワークフロー ファイルをコミットして、ビルドとデプロイのタスクを処理します。 変更を保存する前にこのファイルを確認するには、 **[ファイルのプレビュー]** を選択します。

    > [!NOTE]
    > App Service では、アプリの "[言語スタック設定](configure-common.md#configure-language-stack-settings)" を検出し、最も適切なワークフロー テンプレートを選択します。 別のテンプレートを選択すると、適切に実行されないアプリがデプロイされるおそれがあります。 詳細については、「[GitHub Actions ビルド プロバイダーのしくみ](#how-the-github-actions-build-provider-works)」を参照してください。

1. **[保存]** を選択します。
   
    選択したリポジトリおよびブランチでの新しいコミットが App Service アプリに継続的にデプロイされるようになりました。 コミットとデプロイは、 **[ログ]** タブで追跡できます。

# <a name="bitbucket"></a>[Bitbucket](#tab/bitbucket)

Bitbucket 統合では、ビルドの自動化のために App Service ビルド サービス (Kudu) が使用されます。

4. Bitbucket から初めてデプロイする場合は、 **[承認]** を選択し、承認のプロンプトに従います。 別のユーザーのリポジトリからデプロイする場合は、 **[アカウントの変更]** を選択します。

1. Bitbucket の場合、継続的にデプロイする Bitbucket の **チーム**、**リポジトリ**、**ブランチ** を選択します。

1. **[保存]** を選択します。
   
    選択したリポジトリおよびブランチでの新しいコミットが App Service アプリに継続的にデプロイされるようになりました。 コミットとデプロイは、 **[ログ]** タブで追跡できます。
   
# <a name="local-git"></a>[ローカル Git](#tab/local)

「[Azure App Service へのローカル Git デプロイ](deploy-local-git.md)」を参照してください。

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)
   
   > [!NOTE]
   > Azure Repos は、Windows アプリのデプロイ ソースとしてサポートされます。
   >

4. App Service ビルド サービス (Kudu) は、既定のビルド プロバイダーです。

    > [!NOTE]
    > Azure Pipelines を App Service アプリのビルド プロバイダーとして使用するには、Azure Pipelines から直接 CI/CD を構成します。 App Service では構成しないでください。 **Azure Pipelines** のオプションは、正しい方向を示すだけです。

1. 継続的にデプロイする **Azure DevOps の組織**、**プロジェクト**、**リポジトリ**、**ブランチ** を選択します。 

    DevOps 組織が一覧に表示されていない場合は、お使いの Azure サブスクリプションにまだリンクされていません。 詳細については、[Azure サービス接続の作成](/azure/devops/pipelines/library/connect-to-azure)に関するページを参照してください。

-----

## <a name="disable-continuous-deployment"></a>継続的なデプロイの無効化

1. [Azure portal](https://portal.azure.com) で、App Service アプリの管理ページに移動します。

1. 左側のウィンドウで、 **[デプロイ センター]** を選択します。 次に、 **[設定]**  >  **[接続解除]** の順に選択します。

    ![Azure portal の App Service アプリでクラウド フォルダーの同期を接続解除する方法を示すスクリーンショット。](media/app-service-continuous-deployment/disable.png)

1. 既定では、GitHub Actions ワークフロー ファイルはリポジトリに保持されますが、引き続きアプリへのデプロイをトリガーします。 ファイルをリポジトリから削除するには、 **[ワークフロー ファイルの削除]** を選択します。

1. **[OK]** を選択します。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>GitHub Actions ビルド プロバイダーのしくみ

GitHub Actions ビルド プロバイダーは、[GitHub の CI/CD](#configure-the-deployment-source) 用のオプションです。 次のアクションを完了すると、CI/CD が設定されます。

- GitHub Actions ワークフロー ファイルを GitHub リポジトリに保管して、App Service のビルドとデプロイのタスクを処理します。
- アプリの発行プロファイルを GitHub シークレットとして追加します。 ワークフロー ファイルは、このシークレットを使用して App Service に対する認証を行います。
- [ワークフロー実行ログ](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs)から情報をキャプチャし、それをアプリのデプロイ センターの **[ログ]** タブに表示します。

GitHub Actions ビルド プロバイダーは、次の方法でカスタマイズできます。

- GitHub リポジトリでワークフロー ファイルが生成された後に、そのファイルをカスタマイズします。 詳しくは、「[GitHub Actions のワークフロー構文](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)」を参照してください。 ワークフローが [azure/webapps-deploy](https://github.com/Azure/webapps-deploy) アクションを使用して App Service にデプロイされていることを確認してください。
- 選択したブランチが保護されている場合でも、構成を保存せずに引き続きワークフロー ファイルのプレビューを表示してから、手動でリポジトリに追加することができます。 この方法では、Azure portal とのログ統合は行われません。
- 発行プロファイルを使用するのではなく、Azure Active Directory で[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を使用してデプロイします。

#### <a name="authenticate-by-using-a-service-principal"></a>サービス プリンシパルを使って認証する

このオプション構成では、既定の認証が、生成されたワークフロー ファイル内の発行プロファイルに置き換えられます。

1. [Azure CLI](/cli/azure/) の [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) コマンドを使用すると、サービス プリンシパルが作成されます。 次の例では、 \<subscription-id> 、 \<group-name> 、および \<app-name> を独自の値に置き換えます。

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > セキュリティのために、サービス プリンシパルに最低限必要なアクセス権を付与してください。 前の例の範囲は、リソース グループ全体ではなく、特定の App Service アプリに限定されます。
    
1. 最上位レベル `{}` を含め、次の手順の JSON 出力全体を保存します。

1. [GitHub](https://github.com/) の自分のリポジトリで、 **[設定]**  >  **[シークレット]**  >  **[Add a new secret]\(新しいシークレットの追加\)** を選択します。

1. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` などの名前を付けます。

1. デプロイ センターによって生成されたワークフロー ファイルで、`azure/webapps-deploy` ステップを次の例のように修正します (これは Node.js ワークフロー ファイルから変更されています)。

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added.
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile.
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure.
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>他のリポジトリからデプロイする

Windows アプリの場合、ポータルで直接サポートされていないクラウド Git または Mercurial リポジトリ ([GitLab](https://gitlab.com/) など) からの継続的配置を手動で構成できます。 これを行うには、 **[ソース]** ドロップダウン リストで **[外部 Git]** を選択します。 詳細については、[手動の手順を使用した継続的デプロイの設定](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)に関するページを参照してください。

## <a name="more-resources"></a>その他のリソース

* [Azure Pipelines から Azure App Services にデプロイする](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [継続的配置に関する一般的な問題の調査](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell の使用](/powershell/azure/)
* [プロジェクト Kudu](https://github.com/projectkudu/kudu/wiki)
