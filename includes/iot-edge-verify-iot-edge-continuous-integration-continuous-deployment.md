---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: b5450e4846c3c49c89830ae65c50a95ee0c8d6eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803434"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>ビルド パイプラインとリリース パイプラインの IoT Edge CI/CD を検証する

ビルド ジョブをトリガーするには、コミットをソース コード リポジトリにプッシュするか、手動でトリガーできます。 このセクションでは、CI/CD パイプラインを手動でトリガーして、その動作をテストします。 その後、デプロイが成功したことを確認します。

1. 左側のウィンドウ メニューから、 **[パイプライン]** を選択し、この記事の冒頭で作成したビルド パイプラインを開きます。

2. 右上にある **[パイプラインの実行]** ボタンを選択すると、ビルド パイプラインのビルド ジョブをトリガーできます。

    ![[パイプラインの実行] ボタンを使用してビルド パイプラインを手動でトリガーする](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. **[パイプラインの実行]** の設定をレビューします。 次に、 **[実行]** を選択します。

    ![パイプラインの実行オプションを指定し、[実行] を選択する](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. **[エージェントジョブ 1]** を選択して、実行の進行状況を監視します。 ジョブを選択すると、ジョブの出力ログをレビューできます。 

    ![ジョブのログ出力をレビューする](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. ビルド パイプラインが正常に完了すると、**dev** ステージへのリリースがトリガーされます。 **dev** へのリリースが成功すると、IoT Edge デバイスを対象とする IoT Edge デプロイが作成されます。

    ![dev へのリリース](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. リリース ログを確認するには、**[dev]** ステージをクリックします。

    ![リリース ログ](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)

7. パイプラインでエラーが発生した場合は、まずログを確認します。 ログを表示するには、パイプラインの実行の概要に移動し、ジョブとタスクを選択します。 特定のタスクが失敗している場合は、そのタスクのログを確認します。 ログを構成および使用するための詳細な手順については、「[ログを確認してパイプラインの問題を診断する](/azure/devops/pipelines/troubleshooting/review-logs)」を参照してください。
