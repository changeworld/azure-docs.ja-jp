---
title: Application Insights のリリース注釈 | Microsoft Docs
description: Application Insights を使用してデプロイやその他の重要なイベントを追跡する注釈を作成する方法について説明します。
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: cfd1c9b28a79d68983e49ef5d6dfd70dd357ab47
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071031"
---
# <a name="release-annotations-for-application-insights"></a>Application Insights のリリース注釈

注釈は、新しいビルドのデプロイ先やその他の重要なイベントを示します。 注釈により、変更内容がアプリケーションのパフォーマンスに影響を与えたかどうかを簡単に把握できます。 それらは、[Azure Pipelines](/azure/devops/pipelines/tasks/) ビルド システムで自動的に作成できます。 PowerShell から作成することにより、任意のイベントにフラグを設定する注釈を作成することもできます。

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines ビルドでのリリース注釈

リリース注釈は、Azure DevOps のクラウド ベースの Azure Pipelines サービスの機能です。

次のすべての条件が満たされた場合、デプロイ タスクによってリリース注釈が自動的に作成されます。

- デプロイ先のリソースは、(`APPINSIGHTS_INSTRUMENTATIONKEY` アプリ設定を通じて) Application Insights にリンクされる。
- Application Insights リソースは、デプロイ先のリソースと同じサブスクリプション内にある。
- 次の Azure DevOps パイプライン タスクのいずれかを使用している。

    | タスク コード                 | タスク名                     | バージョン     |
    |---------------------------|-------------------------------|--------------|
    | AzureAppServiceSettings   | Azure App Service の設定    | Any          |
    | AzureRmWebAppDeployment   | Azure App Service のデプロイ      | V3 以上 |
    | AzureFunctionApp          | Azure Functions               | Any          |
    | AzureFunctionAppContainer | コンテナー用の Azure Functions | Any          |
    | AzureWebAppContainer      | Azure Web App for Containers  | Any          |
    | AzureWebApp               | Azure Web アプリ                 | Any          |

> [!NOTE]
> 引き続き Application Insights 注釈のデプロイ タスクを使用している場合は、それを削除する必要があります。

### <a name="configure-release-annotations"></a>リリース注釈を構成する

前のセクションのデプロイ タスクのいずれかを使用できない場合は、デプロイ パイプラインにインライン スクリプト タスクを追加する必要があります。

1. 新規または既存のパイプラインに移動し、タスクを選択します。
    :::image type="content" source="./media/annotations/task.png" alt-text="ステージ内の選択されたタスクのスクリーンショット。" lightbox="./media/annotations/task.png":::
1. 新しいタスクを追加し、 **[Azure CLI]** を選択します。
    :::image type="content" source="./media/annotations/add-azure-cli.png" alt-text="新しいタスクを追加し、[Azure CLI] を選択しているスクリーンショット。" lightbox="./media/annotations/add-azure-cli.png":::
1. 関連する Azure サブスクリプションを指定します。  **[スクリプトの種類]** を *[PowerShell]* に、 **[スクリプトの場所]** を *[インライン]* に変更します。
1. [次のセクションの手順 2 の PowerShell スクリプト](#create-release-annotations-with-azure-cli)を **[インライン スクリプト]** に追加します。
1. 下の引数を **[スクリプトの引数]** に追加し、角かっこで囲まれたプレースホルダーを実際の値に置き換えます。 -releaseProperties は省略可能です。

    ```powershell
        -aiResourceId "<aiResourceId>" `
        -releaseName "<releaseName>" `
        -releaseProperties @{"ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
    ```

    :::image type="content" source="./media/annotations/inline-script.png" alt-text="[スクリプトの種類]、[スクリプトの場所]、[インライン スクリプト]、および [スクリプトの引数] が強調表示されている Azure CLI タスク設定のスクリーンショット。" lightbox="./media/annotations/inline-script.png":::

1. 保存します。

## <a name="create-release-annotations-with-azure-cli"></a>Azure CLI を使用してリリース注釈を作成する

Azure DevOps を使わずに、CreateReleaseAnnotation PowerShell スクリプトを使って、任意のプロセスから注釈を作成できます。

1. [Azure CLI](/cli/azure/authenticate-azure-cli) にサインインします。

2. 下のスクリプトのローカル コピーを作成し、CreateReleaseAnnotation.ps1 という名前を付けます。

    ```powershell
    param(
        [parameter(Mandatory = $true)][string]$aiResourceId,
        [parameter(Mandatory = $true)][string]$releaseName,
        [parameter(Mandatory = $false)]$releaseProperties = @()
    )
    
    $annotation = @{
        Id = [GUID]::NewGuid();
        AnnotationName = $releaseName;
        EventTime = (Get-Date).ToUniversalTime().GetDateTimeFormats("s")[0];
        Category = "Deployment";
        Properties = ConvertTo-Json $releaseProperties -Compress
    }
    
    $body = (ConvertTo-Json $annotation -Compress) -replace '(\\+)"', '$1$1"' -replace "`"", "`"`""

    az rest --method put --uri "$($aiResourceId)/Annotations?api-version=2015-05-01" --body "$($body) "
    ```

3. 次のコードで PowerShell スクリプトを呼び出します。角かっこのプレースホルダーは実際の値に置き換えます。 -releaseProperties は省略可能です。

    ```powershell
         .\CreateReleaseAnnotation.ps1 `
          -aiResourceId "<aiResourceId>" `
          -releaseName "<releaseName>" `
          -releaseProperties @{"ReleaseDescription"="<a description>";
              "TriggerBy"="<Your name>" }
    ```

|引数 | 定義 | Note|
|--------------|-----------------------|--------------------|
|aiResourceId | ターゲットの Application Insights リソースへのリソース ID。 | 例:<br> /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRGName/providers/microsoft.insights/components/MyResourceName|
|releaseName | 作成したリリース注釈に付ける名前。 | | 
|releaseProperties | カスタム メタデータを注釈にアタッチするために使用されます。 | オプション|

## <a name="view-annotations"></a>注釈を表示する

> [!NOTE]
> リリース注釈は、Application Insights の [メトリック] ペインでは現在使用できません。

これで、このリリース テンプレートを使用して新しいリリースをデプロイするたびに、注釈が Application Insights に送信されるようになります。 注釈は次の場所に表示できます。

- パフォーマンス

    :::image type="content" source="./media/annotations/performance.png" alt-text="[パフォーマンス] タブのスクリーンショット。[リリース プロパティ] タブを表示するためのリリース注釈 (青い矢印) が選択されています。" lightbox="./media/annotations/performance.png":::

- エラー

    :::image type="content" source="./media/annotations/failures.png" alt-text="[Failures]\(失敗\) タブのスクリーンショット。[リリース プロパティ] タブを表示するためのリリース注釈 (青い矢印) が選択されています。" lightbox="./media/annotations/failures.png":::
- 使用方法

    :::image type="content" source="./media/annotations/usage-pane.png" alt-text="リリース注釈が選択された棒グラフが示されている [ユーザー] タブのスクリーンショット。リリース注釈は、グラフの上に青い矢印として表示され、リリースが発生した瞬間を示しています。" lightbox="./media/annotations/usage-pane.png":::

- Workbooks

    視覚化により x 軸に時間を表示したログベースのブック クエリ。
    
    :::image type="content" source="./media/annotations/workbooks-annotations.png" alt-text="注釈が表示された時系列ログベース クエリを示すブック ペインのスクリーンショット。" lightbox="./media/annotations/workbooks-annotations.png":::
    
    ブックの注釈を有効にするには、 **[詳細設定]** に移動して **[コメントを表示する]** を選択します。
    
    :::image type="content" source="./media/annotations/workbook-show-annotations.png" alt-text="[コメントを表示する] チェック ボックスが強調表示されている [詳細設定] メニューのスクリーンショット。":::

注釈マーカーを選択すると、要求元、ソース管理のブランチ、リリース パイプライン、環境を含む、リリースに関する詳細が表示されます。

## <a name="next-steps"></a>次のステップ

* [作業項目を作成する](./diagnostic-search.md#create-work-item)
* [PowerShell でのオートメーション](./powershell.md)
