---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89301151"
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