---
title: Azure Chaos Studio を使用してカオス実験を作成して実行する
description: Chaos Studio の実験を 10 分以内に作成して実行する手順について理解する
services: chaos-studio
author: prashabora
ms.topic: article
ms.date: 11/10/2021
ms.author: prashabora
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: ec89106c1aee40bc11e6ee2246ef2e01cb8fd466
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282103"
---
# <a name="quickstart-create-and-run-a-chaos-experiment-using-azure-chaos-studio"></a>クイック スタート: Azure Chaos Studio を使用してカオス実験を作成して実行する 
はじめに、Chaos Studio で VM シャットダウンのサービス直接実験を使用して、実際の同様の障害に対するサービスの回復力を高めます。 

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Linux 仮想マシン。 仮想マシンがない場合は、[次の手順に従って作成](../virtual-machines/linux/quick-create-portal.md)できます。

## <a name="register-the-chaos-studio-resource-provider"></a>Chaos Studio リソース プロバイダーを登録する
Chaos Studio を初めて使用する場合は、まず、リソースをオンボードして実験を作成する前に、Chaos Studio リソース プロバイダーを登録する必要があります。 これは、Chaos Studio を使用するサブスクリプションごとに行う必要があります。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. **サブスクリプション** を検索し、サブスクリプション管理ページを開きます。
3. Chaos Studio を使用するサブスクリプションをクリックします。
4. 左側のナビゲーションで、 **[リソース プロバイダー]** をクリックします。
5. 表示されるリソース プロバイダーの一覧で、**Microsoft.Chaos** を検索します。
6. Microsoft.Chaos プロバイダーをクリックし、 **[登録]** ボタンをクリックします。

## <a name="enable-chaos-studio-on-the-virtual-machine-you-created"></a>作成した仮想マシンで Chaos Studio を有効にする
1. [Azure Portal](https://portal.azure.com)を開きます。
2. 検索バーで **Chaos Studio (プレビュー)** を検索します。
3. **[ターゲット]** をクリックし、作成した VM に移動します。

4. 作成した VM の横にあるチェックボックスをオンにし、 **[Enable targets]\(ターゲットを有効にする\)** をクリックし、ドロップダウン メニューから **[Enable service-direct targets]\(サービス直接ターゲットを有効にする\)** をクリックします。

   ![Azure portal の [ターゲット] ビュー](images/quickstart-virtual-machine-enabled.png)

5. 選択したリソースが正常に有効化されたことを示す通知が表示されます。
   
   ![ターゲットが正常に有効化されたことを示す通知](images/tutorial-service-direct-targets-enable-confirm.png)

## <a name="create-an-experiment"></a>実験の作成

1. **[実験]** をクリックします。                
   ![実験に進む](images/quickstart-left-experiment.png)

2. **[Add an experiment]\(実験を追加する\)** をクリックします。

   ![Azure portal で実験を追加する](images/add-an-experiment.png)

3. **[サブスクリプション]** 、 **[リソース グループ]** 、カオス実験をデプロイする **[場所]** の情報を入力します。 実験に **名前** を付けます。 **[Next : Experiment designer >]\(次へ: 実験デザイナー >\)** をクリックします

   ![実験の基本を追加する](images/quickstart-service-direct-add-basics.png)

4. これで、Chaos Studio 実験デザイナーが表示されます。 **ステップ** と **ブランチ** にわかりやすい名前を付け、 **[障害の追加]** をクリックします。

   ![実験デザイナー](images/quickstart-service-direct-add-designer.png)

5. ドロップダウンから **[VM Shutdown]\(VM シャットダウン\)** を選択し、 **[期間]** に、障害が継続する時間を分単位で入力します。 

   ![障害のプロパティ](images/quickstart-service-direct-add-fault.png)

6. **[Next: Target resources >]\(次へ: ターゲット リソース >\)** をクリックします。
   ![対象の追加](images/quickstart-service-direct-add-targets.png)

7. **[追加]** をクリックします。

   ![Addt](images/quickstart-add-target.png)

8. 実験に問題がないことを確認し、 **[確認と作成]** 、 **[作成]** の順にクリックします。

   ![実験を作成する](images/quickstart-review-and-create.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>仮想マシンに実験アクセス許可を付与する
1. 仮想マシンに移動し、 **[アクセス制御 (IAM)]** をクリックします。
   ![ロール割り当ての追加](images/quickstart-access-control.png)
2. **[追加]** をクリックします。

   ![[追加] ボタン](images/add.png)

3. **[ロールの割り当ての追加]** をクリックします

   ![[ロール割り当ての追加] ボタン](images/add-role-assignment.png)

4. **仮想マシン共同作成者** を検索して、ロールを選択します。 **[次へ]** をクリックします。

   ![VM のロールを選択する](images/quickstart-virtual-machine-contributor.png)
5. **[メンバーを選択する]** をクリックし、実験名を検索します。 実験を選択し、 **[選択]** をクリックします。 
   ![実験を選択する](images/quickstart-select-experiment-role-assignment.png)
 
6. **[レビューと割り当て]** をクリックし、もう一度 **[レビューと割り当て]** をクリックします。



## <a name="run-the-chaos-experiment"></a>カオス実験を実行する

1. Azure portal を開きます。
    * @microsoft.com アカウントを使用している場合は、[このリンクをクリックします](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true)。
    * 外部アカウントを使用している場合は、[このリンクをクリックします](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}})。
2. 実験名の横にあるチェックボックスをオンにし、 **[Start Experiment]\(実験の開始\)** をクリックします。
    ![実験の開始](images/quickstart-experiment-start.png)

3. **[はい]** をクリックして、カオス実験を開始することを確認します。

    ![実験を開始することを確認する](images/start-experiment-confirmation.png)
4. (省略可能) 実験名をクリックすると、実験の実行状態の詳細ビューが表示されます。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

1. 実験名の横にあるチェックボックスをオンにし、 **[削除]** をクリックします。

   ![削除する実験を選択する](images/quickstart-delete-experiment.png)

2. **[はい]** をクリックして実験の削除を確認します。

3. Azure portal の検索バーで、作成した VM を検索します。

   ![VM の選択](images/quickstart-cleanup.png)

4. リソースに対して課金されないようにするには、 **[削除]** をクリックします。

   ![VM を削除する](images/quickstart-cleanup-virtual-machine.png)


## <a name="next-steps"></a>次のステップ
これで、VM シャットダウンのサービスダイレクト実験を実施したので、次のことを行う準備ができました。
- [エージェントベースの障害を使用する実験を作成する](chaos-studio-tutorial-agent-based-portal.md)
