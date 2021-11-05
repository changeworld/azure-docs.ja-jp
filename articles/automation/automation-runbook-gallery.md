---
title: PowerShell ギャラリーの Azure Automation Runbook とモジュールを使用する
description: この記事では、Microsoft GitHub リポジトリおよび PowerShell ギャラリーからの Runbook とモジュールを使用する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: conceptual
ms.openlocfilehash: 1f6b3a47d7d8bcab77c80b71fcbc06c3381509c9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504204"
---
# <a name="use-existing-runbooks-and-modules"></a>既存の Runbook とモジュールを使用する

Azure Automation で独自の Runbook およびモジュールを作成するのではなく、マイクロソフトやコミュニティによって既に作成されているシナリオにアクセスできます。 PowerShell Runbook と Python Runbook は Azure portal 内の Runbook ギャラリーから、[モジュール](#modules-in-the-powershell-gallery)と [Runbook](#runbooks-in-the-powershell-gallery) (Azure に固有のものとそうでないものがあります) は PowerShell ギャラリーから取得できます。 [開発したシナリオ](#contribute-to-the-community)を共有することにより、コミュニティに貢献することもできます。

> [!NOTE]
> TechNet スクリプト センターは廃止されています。 Runbook ギャラリーのスクリプト センターにあるすべての Runbook が [Automation GitHub 組織](https://github.com/azureautomation)に移動されました。詳細については、「[GitHub への Azure Automation Runbook の移動](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)」を参照してください。

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Azure portal を使用して GitHub から Runbook をインポートする

> [!NOTE]
>- Azure portal の **[ギャラリーの参照]** オプションでは、ユーザー エクスペリエンスが強化されています。
>- **[プロセス オートメーション]**  >  **[Runbook]** ブレードでは、 **[Runbook のインポート]** または **[ギャラリーの参照]** オプションを使用して Runbook をインポートできます。 **[Runbook]** ページには、 **[ランタイム バージョン]** と **[Runbook の種類]** という 2 つの新しい列が表示されます。
 
1. Azure ポータルで、Automation アカウントを開きます。
1. **[プロセス オートメーション]** の **[Runbook]** ブレードを選択します。
1. **[Runbook]** ページで **[Runbook のインポート]** をクリックします。

   :::image type="content" source="./media/automation-runbook-gallery/import-runbook.png" alt-text="[Runbook のインポート] オプションから Runbook を選択しているスクリーンショット。":::

1. **[Runbook のインポート]** ページでは、 **[ファイルの参照]** を使用してローカル コンピューターに格納されているファイルをインポートするか、または **[ギャラリーで参照]** を使用して GitHub からファイルをインポートすることができます。
1. ファイルを選択します。
1. **[名前]** 、 **[ランタイム バージョン]** 、 **[説明]** を入力します。
1. **[インポート]** をクリックします。
   
   :::image type="content" source="./media/automation-runbook-gallery/import-runbook-upload-runbook-file.png" alt-text="ファイルまたはギャラリーから Runbook を選択しているスクリーンショット。":::

1. または、 **[Runbook]** ページで **[ギャラリーの参照]** を選択して、使用可能な Runbook を参照します。

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-option.png" alt-text="Runbook ブレードから [ギャラリーの参照] オプションを選択しているスクリーンショット。":::

1. リストの上にあるフィルターを使用して、発行元や種類で表示を絞り込んだり、並べ替えたりすることができます。 必要なギャラリー アイテムを探し、選択して詳細を表示します。

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Runbook ギャラリーの参照。" lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

1. **[選択]** をクリックして、選んだ Runbook を選択します。 
1. **[Runbook のインポート]** ページで、 **[名前]** を入力し、 **[ランタイム バージョン]** を選択します。
1. **[Runbook の種類]** と **[説明]** は自動的に設定されます。
1. **[インポート]** をクリックします。

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="ギャラリー アイテムのインポート。":::

7. Automation アカウントの **[Runbook]** タブに Runbook が表示されます。

 
## <a name="runbooks-in-the-powershell-gallery"></a>PowerShell ギャラリーの Runbook

> [!IMPORTANT]
> PowerShell ギャラリーから取得した Runbook の内容を検証する必要があります。 運用環境で Runbook をインストールおよび実行するときは細心の注意を払ってください。

[PowerShell ギャラリー](https://www.powershellgallery.com/packages)では、Microsoft やコミュニティからさまざまな Runbook が提供されており、それを Azure Automation にインポートできます。 使用するには、ギャラリーから Runbook をダウンロードするか、Azure portal でギャラリーから、または Automation アカウントから、Runbook を直接インポートできます。

> [!NOTE]
> PowerShell ギャラリーではグラフィカル Runbook はサポートされていません。

PowerShell ギャラリーから直接インポートできるのは、Azure portal を使用した場合のみです。 PowerShell を使用してこの機能を実行することはできません。 手順は、「[Azure portal を使用して GitHub から PowerShell Runbook をインポートする](#import-runbooks-from-github-with-the-azure-portal)」に示されている手順と同じですが、**ソース** は **PowerShell ギャラリー** になります。

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Runbook ギャラリーのソースの選択の表示。" lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>PowerShell ギャラリーのモジュール

PowerShell モジュールには、Runbook で使用できるコマンドレットが含まれています。 Azure Automation でインストールできる既存のモジュールは [PowerShell ギャラリー](https://www.powershellgallery.com)から入手できます。 このギャラリーは Azure portal から起動できます。また、モジュールを Azure Automation に直接インストールするか、手動でダウンロードしてインストールすることができます。

インポートするモジュールを Azure portal で探すこともできます。 Automation アカウント用のモジュールは、 **[共有リソース]** の下の **[モジュール]** に一覧表示されます。

> [!IMPORTANT] 
> Az モジュールを使用して実行するように設計されたスクリプトには、キーワード "AzureRm" を含めないでください。 たとえコメントであっても、このキーワードを含めると、AzureRm が読み込まれ、Az モジュールと競合する可能性があります。

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>PowerShell ギャラリーで使用できる一般的なシナリオ

以下の一覧には、一般的なシナリオをサポートするいくつかの Runbook が含まれています。 Azure Automation チームによって作成された Runbook の詳細な一覧については、[AzureAutomationTeam のプロファイル](https://www.powershellgallery.com/profiles/AzureAutomationTeam)のページを参照してください。

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - すべてのモジュールの最新バージョンを PowerShell ギャラリーから Automation アカウントにインポートします。
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - ジョブの状態とジョブ ストリームを含む Azure Automation ログを受信するように Azure Diagnostics と Log Analytics を構成します。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Windows Azure 仮想マシンからリモート ファイルをコピーします。
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - ローカル ファイルを Azure 仮想マシンにコピーします。

## <a name="contribute-to-the-community"></a>コミュニティに貢献する

Azure Automation コミュニティに貢献し、成長を支援することを強くお勧めします。 作成済みの優れた Runbook をコミュニティで共有しましょう。 ご協力をお願いいたします。

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>GitHub Runbook ギャラリーに Runbook を追加する

この GitHub ワークフローを使用して、新しい PowerShell Runbook または Python Runbook を Runbook ギャラリーに追加できます。

1. GitHub でパブリック リポジトリを作成し、Runbook とその他の必要なファイル (readme.md、description など) を追加します。
1. リポジトリがサービスによって検出されるように、トピック `azureautomationrunbookgallery` を追加します。これで、リポジトリを Automation Runbook ギャラリーに表示できます。
1. 作成した Runbook が PowerShell ワークフローの場合は、トピック `PowerShellWorkflow` を追加します。 Python 3 Runbook の場合は、`Python3` を追加します。 Azure Runbook に必要なトピックは他にありませんが、Runbook ギャラリーにおける分類と検索に使用可能なその他のトピックを追加することをお勧めします。

   >[!NOTE]
   >使用する可能性のある書式設定、ヘッダー、既存のタグ (例: `Azure Automation`、`Linux Azure Virtual Machines`) などの項目について、ギャラリー内の既存の Runbook を確認してください。

既存の Runbook に対する変更を提案するには、その Runbook に対して pull request を送信します。 

既存の Runbook を複製して編集する場合は、Runbook に別の名前を付けることをお勧めします。 古い名前を再利用すると、Runbook ギャラリーの一覧に同じ名前が 2 つ表示されます。

>[!NOTE]
>更新された Runbook と新しい Runbook の両方について、GitHub と Automation Runbook ギャラリー間の同期のための時間を少なくとも 12 時間は見込んでおいてください。

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>PowerShell ギャラリーに PowerShell Runbook を追加する

他のユーザーにも役に立つと思われる Runbook を PowerShell ギャラリーに追加してください。 PowerShell ギャラリーでは、PowerShell モジュールと PowerShell スクリプトを受け付けています。 Runbook を追加するには、[PowerShell ギャラリーにアップロード](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)します。

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Azure portal でモジュール ギャラリーからモジュールをインポートする

1. Azure ポータルで、Automation アカウントを開きます。
1. **[共有リソース]** の下にある **[モジュール]** を選択します。
1. **[モジュール]** ページで、 **[ギャラリーの参照]** を選択して、モジュールの一覧を開きます。

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="モジュール ギャラリーのビュー。" lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. [ギャラリーを参照] ページで、次のフィールドで検索できます。

   * モジュール名
   * Tags
   * Author
   * コマンドレット/DSC リソース名

1. 目的のモジュールを探し、選択して詳細を表示します。

   特定のモジュールにドリルダウンすると、詳細を表示できます。 この情報には、PowerShell ギャラリーへのリンク、必要な依存関係、モジュールに含まれるすべてのコマンドレットまたは DSC リソースが含まれています。

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="ギャラリーのモジュールの詳細ビュー。" lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. モジュールを Azure Automation に直接インストールするには、 **[インポート]** をクリックします。
1. [インポート] ペインに、インポートするモジュールの名前が表示されます。 すべての依存関係がインストールされている場合は、 **[OK]** ボタンがアクティブになります。 依存関係が存在しない場合は、このモジュールをインポートする前に、その依存関係をインポートする必要があります。
1. [インポート] ペインで **[OK]** をクリックしてモジュールをインポートします。 Azure Automation がモジュールをアカウントにインポートしている間に、モジュールとコマンドレットについてのメタデータが抽出されます。 各アクティビティを抽出する必要があるため、このアクションには数分かかる場合があります。
1. モジュールをデプロイ中であることが最初に通知され、プロセスが完了すると完了通知が表示されます。
1. モジュールがインポートされた後は、使用可能なアクティビティを確認できます。 Runbook のモジュール リソースと DSC リソースを使用できます。

> [!NOTE]
> PowerShell Core だけをサポートするモジュールは、Azure Automation でサポートされておらず、Azure portal にインポートしたり、PowerShell ギャラリーから直接展開したりすることはできません。

## <a name="request-a-runbook-or-module"></a>Runbook またはモジュールを要求する

[ユーザーの声](https://feedback.azure.com/d365community/forum/8ddd03a2-0225-ec11-b6e6-000d3a4f0858)に要求を送信できます。  Runbook の作成について支援が必要な場合、または PowerShell について質問がある場合は、[Microsoft Q&A 質問ページ](/answers/topics/azure-automation.html)に質問を投稿してください。

## <a name="next-steps"></a>次のステップ

* PowerShell Runbook の使用を開始するには、「[チュートリアル:PowerShell Runbook を作成する](./learn/powershell-runbook-managed-identity.md)」を参照してください。
* Runbook を操作するには、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」を参照してください。
* PowerShell スクリプトの詳細については、[PowerShell のドキュメント](/powershell/scripting/overview)を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
