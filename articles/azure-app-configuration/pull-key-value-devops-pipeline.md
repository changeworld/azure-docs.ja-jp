---
title: Azure Pipelines を使用して App Configuration に設定をプルする
description: Azure Pipelines を使用して App Configuration ストアへのキー値をプルする方法について説明します
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 2bee0e603d3cde38e56146900d682cc5e2b40bdc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056475"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines を使用して App Configuration に設定をプルする

[Azure App Configuration](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) タスクは、App Configuration ストアからキー値をプルし、それを Azure パイプライン変数として設定します。この変数は後続のタスクで使用できます。 このタスクは、キー値を構成ファイルから App Configuration ストアにプッシュする [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) タスクを補足するものです。 詳細については、「[Azure Pipelines を使用して App Configuration に設定をプッシュする](push-kv-devops-pipeline.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- App Configuration ストア - [Azure portal](https://portal.azure.com) で無料リソースを作成する
- Azure DevOps プロジェクト - [無料プロジェクトを作成する](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App Configuration タスク - [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.) から無料でダウンロードする 
- [ノード 10](https://nodejs.org/en/blog/release/v10.21.0/) - セルフホステッド エージェントでタスクを実行しているユーザーの場合。 

## <a name="create-a-service-connection"></a>サービス接続を作成する

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>ロールの割り当ての追加

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>ビルドでの使用

このセクションでは、Azure DevOps ビルド パイプラインで Azure App Configuration タスクを使用する方法について説明します。

1. **[パイプライン]**  >  **[パイプライン]** の順にクリックして、ビルド パイプラインのページに移動します。 ビルド パイプラインのドキュメントについては、「[最初のパイプラインの作成](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser)」を参照してください。
      - 新しいビルド パイプラインを作成している場合は、プロセスの最後の手順で、 **[確認]** タブでパイプラインの右側にある **[アシスタントを表示する]** を選択します。
      ![新しいパイプラインの [アシスタントを表示する] ボタンを示すスクリーンショット。](./media/new-pipeline-show-assistant.png)
      - 既存のビルド パイプラインを使用している場合は、右上にある **[編集]** ボタンをクリックします。
      ![スクリーンショットは、既存のパイプラインの [編集] ボタンを示しています。](./media/existing-pipeline-show-assistant.png)
1. **Azure App Configuration** タスクを検索します。
![スクリーンショットは、[タスクの追加] ダイアログの検索ボックスに「Azure App Configuration」と入力されている状態を示しています。](./media/add-azure-app-configuration-task.png)
1. キー値を App Configuration ストアからプルするために必要なタスクのパラメーターを構成します。 パラメーターの説明は、以下の「**パラメーター**」セクション、および各パラメーターの横にあるヒントを参照してください。
      - **[Azure サブスクリプション]** パラメーターを前の手順で作成したサービス接続の名前に設定します。
      - **[App Configuration 名]** を App Configuration ストアのリソース名に設定します。
      - 残りのパラメーターは既定値のままにします。
![スクリーンショットは、App Configuration タスクのパラメーターを示しています。](./media/azure-app-configuration-parameters.png)
1. ビルドを保存してキューに登録します。 ビルド ログには、タスクの実行中に発生したすべてのエラーが表示されます。

## <a name="use-in-releases"></a>リリースでの使用

このセクションでは、Azure DevOps リリース パイプラインで Azure App Configuration タスクを使用する方法について説明します。

1. **[パイプライン]**  >  **[リリース]** の順に選択して、リリース パイプラインのページに移動します。 リリース パイプラインのドキュメントについては、「[リリース パイプライン](/azure/devops/pipelines/release)」を参照してください。
1. 既存のリリース パイプラインを選択します。 既存のリリース パイプラインがない場合は、 **[新しいパイプライン]** をクリックして新規作成します。
1. 右上隅にある **[編集]** ボタンを選択して、リリース パイプラインを編集します。
1. **[タスク]** ドロップダウンから、タスクを追加する **ステージ** を選択します。 ステージの詳細については、[こちら](/azure/devops/pipelines/release/environments)を参照してください。
![[タスク] ドロップダウンで選択したステージを示すスクリーンショット。](./media/pipeline-stage-tasks.png)
1. 新しいタスクを追加するジョブの横にある **+** をクリックします。
![スクリーンショットは、ジョブの横にあるプラス ボタンを示しています。](./media/add-task-to-job.png)
1. **Azure App Configuration** タスクを検索します。
![スクリーンショットは、[タスクの追加] ダイアログの検索ボックスに「Azure App Configuration」と入力されている状態を示しています。](./media/add-azure-app-configuration-task.png)
1. キー値を App Configuration ストアからプルするために必要なタスク内のパラメーターを構成します。 パラメーターの説明は、以下の「**パラメーター**」セクション、および各パラメーターの横にあるヒントを参照してください。
      - **[Azure サブスクリプション]** パラメーターを前の手順で作成したサービス接続の名前に設定します。
      - **[App Configuration 名]** を App Configuration ストアのリソース名に設定します。
      - 残りのパラメーターは既定値のままにします。
1. リリースを保存してキューに登録します。 リリース ログに、タスクの実行中に発生したすべてのエラーが表示されます。

## <a name="parameters"></a>パラメーター

Azure App Configuration タスクによって、次のパラメーターが使用されます。

- **Azure サブスクリプション**:利用可能な Azure サービス接続を含むドロップダウン。 利用可能な Azure サービス接続の一覧を最新の情報に更新するには、テキストボックスの右側にある **[Azure サブスクリプションの更新]** ボタンを押します。
- **App Configuration の名前**: 選択したサブスクリプションで利用可能な Configuration ストアが読み込まれるドロップダウン。 利用可能な Configuration ストアの一覧を最新の情報に更新するには、テキストボックスの右側にある **[App Configuration 名の更新]** ボタンを押します。
- **キー フィルター**:このフィルターを使用して、Azure App Configuration から要求されるキー値を選択できます。 "*" の値を指定すると、すべてのキー値が選択されます。 詳細については、「[キー値の照会](concept-key-value.md#query-key-values)」を参照してください。
- **ラベル**:App Configuration ストアからキー値を選択するときに使用するラベルを指定します。 ラベルが指定されていない場合は、ラベルのないキー値が取得されます。 ","、"*" の文字は入力できません。
- **キー プレフィックスのトリミング**:変数として設定する前に、App Configuration キーからトリミングする必要がある 1 つ以上のプレフィックスを指定します。 複数のプレフィックスは、改行文字で区切ることができます。

## <a name="use-key-values-in-subsequent-tasks"></a>後続のタスクでキー値を使用する

App Configuration からフェッチされるキー値は、環境変数としてアクセスできるパイプライン変数として設定されます。 環境変数のキーは、プレフィックスをトリミングした後に (指定されている場合)、App Configuration から取得されるキー値のキーです。

たとえば、後続のタスクが PowerShell スクリプトを実行する場合、次のようにキー 'myBuildSetting' を持つキー値を使用できます。
```powershell
echo "$env:myBuildSetting"
```
値がコンソールに出力されます。

> [!NOTE]
> App Configuration 内の Azure Key Vault 参照が解決され、[シークレット変数](/azure/devops/pipelines/process/variables#secret-variables)として設定されます。 Azure Pipelines では、シークレット変数はログからマスクされます。 これらは環境変数としてタスクに渡されないので、代わりに入力として渡す必要があります。 

## <a name="troubleshooting"></a>トラブルシューティング

予期しないエラーが発生した場合、パイプライン変数 `system.debug` を `true`に設置すると、デバッグ ログを有効にすることができます。

## <a name="faq"></a>よく寄せられる質問

**複数のキーとラベルはどのように構成したらよいですか?**

複数のラベル (default と dev など) を構成する必要がある場合もあります。 このシナリオを実装するために、1 つのパイプラインで複数の App Configuration タスクを使用できます。 後の手順でタスクによってフェッチされるキー値は、前の手順の値よりも優先されます。 前述の例では、1 つ目のタスクを使用して default のラベルを持つキー値を選択し、2 番目のタスクで dev ラベルを持つキー値を選択できます。 dev ラベルを持つキーは、default ラベルを持つ同じキーよりも優先されます。
