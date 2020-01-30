---
title: 機械学習パイプラインのデバッグとトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK for Python で機械学習パイプラインのデバッグとトラブルシューティングを行います。 パイプラインの開発における陥りやすい落とし穴と、リモートからの実行前および実行中にスクリプトをデバッグするためのヒントについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: 991f7ebf51be5f805a8b12fa0af0fefeff0ef582
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309559"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>機械学習パイプラインのデバッグとトラブルシューティング
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) および [Azure Machine Learning デザイナー](https://docs.microsoft.com/azure/machine-learning/concept-designer)で[機械学習パイプライン](concept-ml-pipelines.md)をデバッグしてトラブルシューティングする方法について説明します。


## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK でのデバッグとトラブルシューティング
以降のセクションでは、パイプラインの構築時に陥りやすい落とし穴と、パイプラインで実行されているコードをデバッグするためのさまざまな方法の概要について説明します。 パイプラインが予期したとおりに実行されない場合は、次のヒントを参考にしてください。 
### <a name="testing-scripts-locally"></a>スクリプトのローカルでのテスト

パイプラインにおける最も一般的なエラーの 1 つは、アタッチされたスクリプト (データ クレンジング スクリプト、スコアリング スクリプトなど) が意図したとおりに実行されないこと、またはスクリプトに Azure Machine Learning Studio のワークスペースでデバッグが困難なリモート コンピューティング コンテキストの実行時エラーが含まれていることです。 

パイプライン自体をローカル環境で実行することはできませんが、ローカル コンピューター上の分離環境でスクリプトを実行すると、コンピューティングと環境のビルド プロセスを待つ必要がないため、デバッグが速くなります。 これを行うには、いくつかの開発作業が必要です。

* データがクラウド データストアにある場合は、データをダウンロードして、スクリプトで使用できるようにする必要があります。 小さなデータのサンプルの使用は、実行時間を短縮して、スクリプトの動作に関するフィードバックをすぐに得るために有効な方法です
* 中間パイプラインの手順をシミュレートしようとしている場合は、特定のスクリプトが前の手順から想定しているオブジェクトの種類を手動で構築しなければならない場合があります
* また、独自の環境を定義し、リモート コンピューティング環境で定義されている依存関係をレプリケートする必要もあります

ローカル環境で実行するスクリプトのセットアップが完了すると、次のようなデバッグ タスクの実行がはるかに容易になります。

* カスタム デバッグ構成のアタッチ
* 実行の停止とオブジェクトの状態の確認
* 実行時まで確認できない型や論理エラーの把握

> [!TIP] 
> スクリプトが想定どおりに実行されることを確認したら、次の手順として、複数ステップのパイプラインで実行する前に、単一ステップのパイプラインでスクリプトを実行することをお勧めします。

### <a name="debugging-scripts-from-remote-context"></a>リモート コンテキストからのスクリプトのデバッグ

スクリプトのローカルでのテストは、パイプラインの構築を開始する前に主なコード フラグメントや複雑なロジックをデバッグするための優れた方法です。ただし、特にパイプラインのステップ間の相互作用中に診断動作が生じる場合には、ある時点で実際のパイプライン自体の実行中にスクリプトをデバッグすることが必要になる可能性が高いです。 JavaScript コードをデバッグする場合と同様に、リモート実行時にオブジェクトの状態と予期される値を確認できるように、ステップ スクリプトで `print()` ステートメントを適宜使用することをお勧めします。

ログ ファイル `70_driver_log.txt` には、以下が含まれています。 

* スクリプトの実行中に出力されたすべてのステートメント
* スクリプトのスタック トレース 

ポータルでこのログ ファイルとその他のログ ファイルを見つけるには、まずワークスペースでパイプラインの実行をクリックします。

![パイプラインの実行の一覧ページ](./media/how-to-debug-pipelines/pipelinerun-01.png)

パイプラインの実行の詳細ページに移動します。

![パイプラインの実行の詳細ページ](./media/how-to-debug-pipelines/pipelinerun-02.png)

特定のステップのモジュールをクリックします。 **[ログ]** タブに移動します。その他のログには、環境のイメージ ビルド プロセスやステップ準備スクリプトに関する情報が含まれます。

![パイプラインの実行の詳細ページの [ログ] タブ](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> "*発行されたパイプライン*" の実行は、ワークスペースの **[エンドポイント]** タブにあります。 "*発行されていないパイプライン*" の実行は、 **[Experiments]\(実験\)** または **[パイプライン]** にあります。

### <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

次の表に、パイプライン開発時の一般的な問題と、考えられる解決策を示します。

| 問題 | 考えられる解決策 |
|--|--|
| `PipelineData` ディレクトリにデータを渡せない | パイプラインがステップの出力データを想定する場所に、スクリプトでディレクトリを作成したことを確認してください。 ほとんどの場合、入力引数によって出力ディレクトリが定義されます。ディレクトリを明示的に作成してください。 出力ディレクトリを作成するには、`os.makedirs(args.output_dir, exist_ok=True)` を使用します。 この設計パターンを示すスコアリング スクリプトの例については、[こちらのチュートリアル](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script)を参照してください。 |
| 依存関係のバグ | スクリプトをローカルで開発してテスト済みであるのに、パイプラインのリモート コンピューティングでの実行時に依存関係の問題が見つかった場合は、コンピューティング環境の依存関係とバージョンがテスト環境と一致していることを確認してください。 |
| コンピューティング ターゲットでのあいまいなエラー | コンピューティング ターゲットを削除して再作成すると、コンピューティング ターゲットでの特定の問題を解決できます。 |
| ステップを再利用しないパイプライン | ステップの再利用は既定で有効になっていますが、パイプライン ステップで無効にしていないか確認してください。 再利用が無効になっている場合は、ステップの `allow_reuse` パラメーターが `False` に設定されます。 |
| パイプラインが不必要に再実行される | 基になるデータまたはスクリプトが変更されたときにのみステップが再実行されるようにするには、各ステップのディレクトリを分離します。 複数のステップに同じソース ディレクトリを使用すると、不要に再実行される可能性があります。 パイプライン ステップ オブジェクトで `source_directory` パラメーターを使用して、そのステップの分離されたディレクトリを指定し、複数のステップで同じ `source_directory` パスを使用しないようにします。 |

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーでのデバッグとトラブルシューティング

このセクションでは、デザイナーでパイプラインをトラブルシューティングする方法の概要について説明します。
デザイナーで作成されたパイプラインの場合、作成ページまたはパイプラインの実行の詳細ページに**ログ ファイル**があります。

### <a name="access-logs-from-the-authoring-page"></a>作成ページからログにアクセスする

パイプラインの実行を送信し、作成ページを表示したままにした場合、モジュールごとに生成されたログ ファイルを確認できます。

1. 作成キャンバスで任意のモジュールを選択します。
1. プロパティ ペインで、 **[ログ]** タブに移動します。
1. ログ ファイル `70_driver_log.txt` を選択します

    ![作成ページのモジュールのログ](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>パイプラインの実行からログにアクセスする

パイプラインの実行の詳細ページの **[パイプライン]** または **[Experiments]\(実験\)** セクションにも特定の実行のログ ファイルがあります。

1. デザイナーで作成されたパイプラインの実行を選択します。
    ![パイプラインの実行ページ](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. プレビュー ペインで任意のモジュールを選択します。
1. プロパティ ペインで、 **[ログ]** タブに移動します。
1. ログ ファイル `70_driver_log.txt` を選択します

## <a name="next-steps"></a>次のステップ

* [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) パッケージおよび [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) パッケージについては、SDK リファレンスを参照してください。

* [デザイナーの例外とエラー コード](algorithm-module-reference/designer-error-codes.md)の一覧を参照してください。
