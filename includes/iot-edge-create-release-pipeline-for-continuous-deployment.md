---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 8009d98ddbfa778cf5f357248ecd943b810e06e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803418"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>継続的デプロイのためのリリース パイプラインを作成する

このセクションでは、ビルド パイプラインで成果物を削除すると自動的に実行されるように構成されたリリース パイプラインを作成します。その後、Azure Pipelines にデプロイ ログが表示されます。

新しいパイプラインを作成し、新しいステージを追加します。

1. **[パイプライン]** の **[リリース]** タブで、 **[+ 新しいパイプライン]** を選択します。 または、既にリリース パイプラインがある場合は、**[+ 新規]** ボタンを選択し、**[+ 新しいリリース パイプライン]** を選択します。  

    ![[+ 新しいパイプライン] ボタンを使用してリリース パイプラインを追加する](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. テンプレートを選択するよう求められたら、**[空のジョブ]** を選択します。

    ![リリース パイプラインの作成を空のジョブから開始する](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. **ステージ 1** という 1 つのステージを持つ新しいリリース パイプラインが初期化されます。 ステージ 1 の名前を **dev** に変更し、お使いの開発環境用の継続的デプロイ パイプラインとして扱います。 通常、継続的デプロイ パイプラインには、**dev**、**staging**、**prod** の複数のステージがあります。使用する DevOps 手法に応じて、別の名前を使用したり、さらに作成したりできます。 名前を変更した後、ステージの詳細ウィンドウを閉じます。

   上部にある "新しいリリース パイプライン" のテキストを選択して、リリース パイプラインの名前を変更することもできます。

4. ビルド パイプラインによって発行されているビルド成果物に、リリースをリンクします。 成果物領域で **[追加]** をクリックします。

   ![インターフェイスの成果物領域で [追加] をクリックします。](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. **[成果物の追加] ページ** で、 **[ソースの種類]** として **[ビルド]** を選択します。 作成したプロジェクトとビルド パイプラインを選択します。 **[ソース エイリアス]** は、必要に応じてわかりやすいものに変更できます。 その後、 **[追加]** を選択します。

   ![成果物の追加ページで [追加] を選択して成果物を作成する](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. 成果物トリガーを開き、切り替えを選択して継続的デプロイ トリガーを有効にします。 これで、新しいビルドが利用可能になるたびに新しいリリースが作成されるようになります。

   ![成果物トリガーを開き、継続的デプロイ トリガーを有効に切り替える](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. **dev** ステージは、1 つのジョブ、0 個のタスクで、事前に構成されています。 パイプライン メニューから **[タスク]** を選択し、**[dev]** ステージを選択します。 **[エージェント ジョブ]** を選択し、その **[表示名]** を **QA** に変更します。 エージェント ジョブの詳細を構成できますが、デプロイ タスクはプラットフォームに依存しないため、選択した **[エージェント プール]** で任意の **[エージェント仕様]** を使用できます。

   ![[タスク] タブで dev ステージのタスクを表示する](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. QA ジョブで、プラス記号 ( **+** ) を選択して 2 つのタスクを追加します。 **[Azure IoT Edge]** を検索して 2 回追加します。

9. 最初の **Azure IoT Edge** タスクを選択し、次の値を使用して構成します。

    | パラメーター | 説明 |
    | --- | --- |
    | 表示名 | 表示名は、アクション フィールドが変更されると自動的に更新されます。 |
    | アクション | [`Generate deployment manifest`] を選択します。 |
    | .template.json ファイル | パスを指定します: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`。 このパスはビルド パイプラインから発行されます。 |
    | 既定のプラットフォーム | ターゲットの IoT Edge デバイスに基づいて、モジュールの適切なオペレーティング システムを選択します。 |
    | [出力パス]| パス `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` を設定します。 このパスは、最終的な IoT Edge 配置マニフェスト ファイルです。 |

    これらの構成によって、`deployment.template.json` ファイル内のモジュール イメージ URL を置き換えることができます。 また、**[Generate deployment manifest]\(配置マニフェストの生成\)** は、`deployment.template.json` ファイルで定義したのと同じ値に変数を置き換えるのにも役立ちます。 VS/VS Code では、`.env` ファイルに実際の値を指定します。 Azure Pipelines では、 **[リリース パイプライン変数]** タブで値を設定します。 **[変数]** タブに移動し、次のように名前と値を構成します。

    * **ACR_ADDRESS**:Azure Container Registry の **ログイン サーバー** 値。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。
    * **ACR_PASSWORD**:Azure Container Registry のパスワード。
    * **ACR_USER**:Azure Container Registry のユーザー名。

    プロジェクトに他の変数がある場合は、このタブでその名前と値を指定できます。 **[デプロイ マニフェストの生成]** で認識できるのは `${VARIABLE}` フレーバー内の変数だけです。 `*.template.json` ファイルでこのフレーバーを使用していることを確認してください。
    
    ```json-interactive
    "registryCredentials": {
      "<ACR name>": { // Your Azure Container Registry **Registry name** value
        "username": "${ACR_USER}",
        "password": "${ACR_PASSWORD}",
        "address": "${ACR_ADDRESS}"
      }
    }
    ```
    
    ![[変数] タブでリリース パイプラインの変数を構成する](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. 2 つ目の **[Azure IoT Edge]** タスクを選択し、次の値を使用して構成します。

    | パラメーター | 説明 |
    | --- | --- |
    | 表示名 | 表示名は、アクション フィールドが変更されると自動的に更新されます。 |
    | アクション | [`Deploy to IoT Edge devices`] を選択します。 |
    | デプロイ ファイル | パス `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` を設定します。 このパスは IoT Edge デプロイ マニフェスト ファイルです。 |
    | Azure サブスクリプション | IoT Hub が含まれているサブスクリプションを選択します。|
    | IoT Hub 名 | IoT ハブを選択します。|
    | 1 つまたは複数のデバイスを選択 | リソース パイプラインを 1 つのデバイスまたは複数のデバイスのどちらにデプロイするかを選択します。 1 つのデバイスにデプロイする場合は、**IoT Edge デバイス ID** を入力します。 複数のデバイスにデプロイする場合は、デバイスの **ターゲット条件** を指定します。 ターゲット条件とは、IoT Hub 内の一連の IoT Edge デバイスと突き合わせるフィルターです。 デバイス タグを条件として使用する場合は、対応するデバイス タグを IoT Hub デバイス ツインに合わせて更新する必要があります。 詳細設定で、**IoT Edge デプロイ ID** と **IoT Edge デプロイの優先順位** を更新します。 複数のデバイスのデプロイを作成する方法の詳細については、[IoT Edge 自動デプロイについての理解](../articles/iot-edge/module-deployment-monitoring.md)に関するページを参照してください。 |
    | デバイス ID またはターゲット条件 | 以前の選択に応じて、複数のデバイスにデプロイするためのデバイス ID または[ターゲット条件](../articles/iot-edge/module-deployment-monitoring.md#target-condition)を指定します。 |
    | 詳細設定 | IoT Edge デプロイ ID には、`$(System.TeamProject)-$(Release.EnvironmentName)` を指定します。 この変数により、プロジェクトとリリース名がその IoT Edge デプロイ ID を使用してマッピングされます。 |
    

    パブリック クラウドに表示されないプライベート Docker Trusted Registry に存在するイメージを使用するタスクの場合は、**SKIP_MODULE_IMAGE_VALIDATION** 環境変数を `true` に設定して、イメージの検証をスキップできます。 

    ![dev ステージに Azure IoT Edge タスクを追加する](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. **[保存]** を選択して、新しいリリース パイプラインの変更内容を保存します。 メニューで **[パイプライン]** タブを選択して、パイプライン ビューに戻ります。
