---
title: 継続的インテグレーションを自動化する
description: Azure Pipelines パイプライン リリースを使用して Azure Data Factory 内で継続的インテグレーションを自動化する方法について説明します。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c32b2e986e50fd2679b65b449eccdf5c7629a9a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219296"
---
# <a name="automate-continuous-integration-using-azure-pipelines-releases"></a>Azure Pipelines リリースを使用して継続的インテグレーションを自動化する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

以下は、Azure Pipelines リリースを設定し、複数の環境へのデータ ファクトリのデプロイを自動化するためのガイドです。

## <a name="requirements"></a>必要条件

-   [Azure Resource Manager サービス エンドポイント](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)を使用して Visual Studio Team Foundation Server または Azure Repos にリンクされた Azure サブスクリプション。

-   Azure Repos Git 統合で構成されたデータ ファクトリ。

-   各環境用のシークレットを格納する [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

## <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines リリースをセットアップする

1.  [Azure DevOps](https://dev.azure.com/) で、データ ファクトリで構成したプロジェクトを開きます。

1.  ページの左側で、 **[パイプライン]** を選択して **[リリース]** を選択します。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image6.png" alt-text="パイプラインの選択、リリース":::

1.  **[新しいパイプライン]** を選択するか、既存のパイプラインがある場合は **[新規]** 、 **[新しいリリース パイプライン]** の順に選択します。

1.  **[空のジョブ]** テンプレートを選択します。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image13.png" alt-text="空のジョブの選択":::

1.  **[ステージ名]** ボックスに、環境の名前を入力します。

1.  **[成果物の追加]** を選択し、開発データ ファクトリで構成した Git リポジトリを選択します。 **既定のブランチ** のリポジトリの [発行ブランチ](source-control.md#configure-publishing-settings)を選択します。 既定では、この発行ブランチは `adf_publish` です。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image7.png" alt-text="アーティファクトの追加":::

1.  Azure Resource Manager デプロイ タスクを追加します。

    a.  ステージ ビューで、 **[ステージ タスクを表示します]** を選択します。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image14.png" alt-text="ステージ ビュー":::

    b.  新しいタスクを作成します。 **[ARM テンプレートのデプロイ]** を探して **[追加]** を選択します。

    c.  デプロイ タスクでは、ターゲットのデータ ファクトリのサブスクリプション、リソース グループ、および場所を選択します。 必要であれば資格情報を指定します。

    d.  **[Action]\(アクション\)** 一覧で、 **[Create or update resource group]\(リソース グループの作成または更新\)** を選択します。

    e.  **[テンプレート]** ボックスの横にある省略記号ボタン ( **[…]** ) を選択します。 構成されている Git リポジトリの発行ブランチで生成される Azure Resource Manager テンプレートを参照します。 adf_publish ブランチの &lt;FactoryName&gt; フォルダーで、ファイル `ARMTemplateForFactory.json` を探します。

    f.  **[…]** を選択します ( **[Template parameters]\(テンプレート パラメーター\)** ボックスの横にあります)。これにより、パラメーター ファイルを選択します。 adf_publish ブランチの &gt;FactoryName&lt; フォルダーで、ファイル `ARMTemplateParametersForFactory.json` を探します。

    g.  **[…]** を選択します ( **[テンプレート パラメーターのオーバーライド]** ボックスの横にあります)。ターゲットのデータ ファクトリに望ましいパラメーター値を入力します。 Azure Key Vault から取得した資格情報の場合は、シークレットの名前を二重引用符で囲んで入力します。 たとえば、シークレットの名前が cred1 の場合は、この値に「 **"$ (cred1)"** 」と入力します。

    h. **[配置モード]** で **[Incremental]\(増分\)** を選択します。

    > [!WARNING]
    > 完全なデプロイ モードでは、リソース グループに存在していても、新しい Resource Manager テンプレート内で指定されていないリソースは、**削除** されます。 詳細については、「[Azure Resource Manager のデプロイ モード](../azure-resource-manager/templates/deployment-modes.md)」を参照してください。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image9.png" alt-text="データ ファクトリの運用環境デプロイ":::

1.  リリース パイプラインを保存します。

1. リリースをトリガーするには、 **[Create release]\(リリースの作成\)** を選択します。 リリースの作成を自動化するには、[Azure DevOps のリリース トリガー](/azure/devops/pipelines/release/triggers)に関するページを参照してください。

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image10.png" alt-text="[Create release]\(リリースの作成\) の選択":::

> [!IMPORTANT]
> CI/CD のシナリオでは、異なる環境内の統合ランタイム (IR) の種類は同じである必要があります。 たとえば、開発環境にセルフホステッド IR がある場合、テストや運用などの他の環境でも、IR の種類はセルフホステッドである必要があります。 同様に、複数のステージ間で統合ランタイムを共有している場合は、開発、テスト、運用など、すべての環境で、統合ランタイムをリンクされたセルフホステッドとして構成する必要があります。

## <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault からシークレットを取得する

Azure Resource Manager テンプレートに渡すシークレットがある場合は、Azure Pipelines リリースで Azure Key Vault を使うことをお勧めします。

シークレットを処理する方法は 2 つあります。

- シークレットをパラメーター ファイルに追加します。 詳しくは、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)」をご覧ください。

    発行ブランチにアップロードされたパラメーター ファイルのコピーを作成します。 次の形式を使用して、Key Vault から取得するパラメーターの値を設定します。

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    このメソッドを使用すると、シークレットはキー コンテナーから自動的にプルされます。

    パラメーター ファイルも発行ブランチ内に存在する必要があります。

- 前のセクションで説明されている Azure Resource Manager デプロイ タスクの前に、[Azure Key Vault タスク](/azure/devops/pipelines/tasks/deploy/azure-key-vault)を追加します。

    1.  **[タスク]** タブで、新しいタスクを作成します。 **Azure Key Vault** を検索して追加します。

    1.  Key Vault タスクで、キー コンテナーを作成したサブスクリプションを選択します。 必要に応じて資格情報を入力し、キー コンテナーを選択します。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image8.png" alt-text="Key Vault タスクの追加":::

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines エージェントにアクセス許可を与える

適切なアクセス許可が設定されていない場合、Azure Key Vault タスクがアクセス拒否エラーで失敗することがあります。 リリースのログをダウンロードし、Azure Pipelines エージェントにアクセス許可を与えるコマンドを含む .ps1 ファイルを探します。 コマンドは直接実行できます。 または、ファイルからプリンシパル ID をコピーし、Azure portal でアクセス ポリシーを手動で追加できます。 必要な最低限のアクセス許可は、`Get` と `List` です。

## <a name="updating-active-triggers"></a>アクティブなトリガーを更新する

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

>[!WARNING]
>最新バージョンの PowerShell および Data Factory モジュールを使用しない場合は、コマンドの実行中に逆シリアル化エラーが発生する可能性があります。 
>

アクティブなトリガーを更新しようとした場合、デプロイは失敗します。 アクティブなトリガーを更新するには、手動でそれらを停止し、デプロイ後に再起動する必要があります。 これは、Azure Powershell タスクを使用して実行できます。

1.  リリースの **[タスク]** タブで、 **[Azure PowerShell]** タスクを追加します。 タスク バージョンとして最新の Azure PowerShell バージョンを選択します。 

1.  お使いのファクトリがあるサブスクリプションを選択します。

1.  スクリプトの種類として **[スクリプト ファイル パス]** を選択します。 これにより、リポジトリへの PowerShell スクリプトの保存が求められます。 次の PowerShell スクリプトを使用すると、トリガーを停止できます。

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

(`Start-AzDataFactoryV2Trigger` 関数を使用して) 同様の手順を実行することで、デプロイ後にトリガーを再起動できます。

データ ファクトリ チームは、[サンプルのデプロイ前と後のスクリプト](continuous-integration-delivery-sample-script.md)を提供しています。 

## <a name="next-steps"></a>次のステップ

- [継続的インテグレーションとデリバリーの概要](continuous-integration-delivery.md)
- [Resource Manager テンプレートを各環境に手動でレベル上げする](continuous-integration-delivery-manual-promotion.md)
- [Resource Manager テンプレートでカスタム パラメーターを使用する](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [リンクされた Resource Manager テンプレート](continuous-integration-delivery-linked-templates.md)
- [修正プログラム運用環境の適用](continuous-integration-delivery-hotfix-environment.md)
- [サンプルのデプロイ前と後のスクリプト](continuous-integration-delivery-sample-script.md)
