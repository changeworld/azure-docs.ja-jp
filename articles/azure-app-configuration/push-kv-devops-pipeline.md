---
title: Azure Pipelines を使用して App Configuration に設定をプッシュする
description: Azure Pipelines を使用して App Configuration ストアにキー値をプッシュする方法について説明します
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: 2f5ac3579489637e5adcdfaa40fac90b5ff6e8b4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427154"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines を使用して App Configuration に設定をプッシュする

[Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) タスクは、キー値を構成ファイルから App Configuration ストアにプッシュします。 このタスクによって、App Configuration ストアから設定をプルしたり、App Configuration ストアに設定をプッシュしたりできるようになるため、パイプライン内で完全なサークル機能が有効になります。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- App Configuration リソース - [Azure portal](https://portal.azure.com)で無料リソースを作成する。
- Azure DevOps プロジェクト - [無料プロジェクトを作成する](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App Configuration Push タスク - [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) から無料でダウンロードする。
- [ノード 10](https://nodejs.org/en/blog/release/v10.21.0/) - セルフホステッド エージェントでタスクを実行しているユーザーの場合。

## <a name="create-a-service-connection"></a>サービス接続を作成する

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>ロールの割り当ての追加

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>ビルドでの使用

このセクションでは、Azure DevOps ビルド パイプラインで Azure App Configuration Push タスクを使用する方法について説明します。

1. **[パイプライン]**  >  **[パイプライン]** の順にクリックして、ビルド パイプラインのページに移動します。 ビルド パイプラインについては、[こちらのドキュメント](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)を参照してください。
      - 新しいビルド パイプラインを作成している場合は、プロセスの最後の手順を行うときに、 **[確認]** タブでパイプラインの右側にある **[アシスタントを表示する]** を選択します。
      ![新しいパイプラインの [アシスタントを表示する] ボタンを示すスクリーンショット。](./media/new-pipeline-show-assistant.png)
      - 既存のビルド パイプラインを使用している場合は、右上にある **[編集]** ボタンをクリックします。
      ![既存のパイプラインの [編集] ボタンを示すスクリーンショット。](./media/existing-pipeline-show-assistant.png)
1. **Azure App Configuration Push** タスクを検索します。
![検索ボックスに「Azure App Configuration Push」と入力された [タスクの追加] ダイアログを示すスクリーンショット。](./media/add-azure-app-configuration-push-task.png)
1. キー値を構成ファイルから App Configuration ストアにプッシュするために必要なタスクのパラメーターを構成します。 パラメーターの説明は、以下の「**パラメーター**」セクション、および各パラメーターの横にあるヒントを参照してください。
![App Configuration Push タスクのパラメーターを示すスクリーンショット。](./media/azure-app-configuration-push-parameters.png)
1. ビルドを保存してキューに登録します。 ビルド ログには、タスクの実行中に発生したすべてのエラーが表示されます。

## <a name="use-in-releases"></a>リリースでの使用

このセクションでは、Azure DevOps リリース パイプラインで Azure App Configuration Push タスクを使用する方法について説明します。

1. **[パイプライン]**  >  **[リリース]** の順に選択して、リリース パイプラインのページに移動します。 リリース パイプラインについては、[こちらのドキュメント](/azure/devops/pipelines/release)を参照してください。
1. 既存のリリース パイプラインを選択します。 ない場合は、 **[+ 新規]** を選択して、新規作成します。
1. 右上隅にある **[編集]** ボタンを選択して、リリース パイプラインを編集します。
1. **[タスク]** ドロップダウンから、タスクを追加する **ステージ** を選択します。 ステージの詳細については、[こちら](/azure/devops/pipelines/release/environments)を参照してください。
![[タスク] ドロップダウンで選択したステージを示すスクリーンショット。](./media/pipeline-stage-tasks.png)
1. 新しいタスクを追加するジョブの横にある **+** をクリックします。
![ジョブの横にあるプラス ボタンを示すスクリーンショット。](./media/add-task-to-job.png)
1. **[タスクの追加]** ダイアログで、検索ボックスに「**Azure App Configuration Push**」と入力して選択します。
1. キー値を構成ファイルから App Configuration ストアにプッシュするために必要なタスク内のパラメーターを構成します。 パラメーターの説明は、以下の「**パラメーター**」セクション、および各パラメーターの横にあるヒントを参照してください。
1. リリースを保存してキューに登録します。 リリース ログに、タスクの実行中に発生したすべてのエラーが表示されます。

## <a name="parameters"></a>パラメーター

App Configuration Push タスクによって、次のパラメーターが使用されます。

- **Azure サブスクリプション**:利用可能な Azure サービス接続を含むドロップダウン。 利用可能な Azure サービス接続の一覧を最新の情報に更新するには、テキストボックスの右側にある **[Azure サブスクリプションの更新]** ボタンを押します。
- **App Configuration の名前**: 選択したサブスクリプションで利用可能な Configuration ストアが読み込まれるドロップダウン。 利用可能な Configuration ストアの一覧を最新の情報に更新するには、テキストボックスの右側にある **[App Configuration 名の更新]** ボタンを押します。
- **構成ファイルのパス**: 構成ファイルへのパス。 **[構成ファイルのパス]** パラメーターは、ファイル リポジトリのルートから始まります。 ビルド成果物を参照して、構成ファイルを選択することができます (テキストボックスの右側にある `...` ボタン)。 サポートされているファイル形式は、yaml、json、properties です。 json 形式の構成ファイルの例を次に示します。
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **区切り記号**: .json および .yml ファイルをフラット化するために使用する区切り記号。
- **深さ**: .json および .yml ファイルをフラット化する深さ。
- **プレフィックス**: App Configuration ストアにプッシュされる各キーの先頭に追加される文字列。
- **ラベル**:App Configuration ストア内で、各キー値にラベルとして追加される文字列。
- **コンテンツの種類**: App Configuration ストア内で、各キー値にコンテンツの種類として追加される文字列。
- **タグ**:`{"tag1":"val1", "tag2":"val2"}` 形式の JSON オブジェクト。これは、App Configuration ストアにプッシュされる各キー値に追加されるタグを定義します。
- **指定されたプレフィックスとラベルを含むストア内の他のすべてのキー値を削除する**: 既定値は、**オフ** です。
  - **オン**: 指定されたプレフィックスとラベルの両方と一致する App Configuration ストア内のすべてのキー値を削除した後、構成ファイルの新しいキー値をプッシュします。
  - **オフ**: すべてのキー値を構成ファイルから App Configuration ストアにプッシュし、App Configuration ストア内の他のすべてのものをそのまま残します。



## <a name="troubleshooting"></a>トラブルシューティング

予期しないエラーが発生した場合、パイプライン変数 `system.debug` を `true`に設置すると、デバッグ ログを有効にすることができます。

## <a name="faq"></a>よく寄せられる質問

**複数の構成ファイルをアップロードするには、どうすればよいですか?**

同じパイプライン内で Azure App Configuration Push タスクの複数のインスタンスを作成して、複数の構成ファイルを App Configuration ストアにプッシュします。

**このタスクを使用して Key Vault 参照を作成するにはそうすればよいですか?**

Key Vault 参照を作成するには、"Content Type" パラメーターを *application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8* に設定します。 構成ファイルに含まれているキーと値の一部が Key Vault 参照でない場合は、Key Vault 参照および通常のキーと値を別々の構成ファイルに格納して、それらを個別にプッシュしてください。

**キー値を Configuration ストアにプッシュしようとすると、409 エラーが発生するのはなぜですか?**

409 競合エラー メッセージは、タスクで、App Configuration ストア内のロックされたキー値を削除または上書きしようとしたときに発生します。
