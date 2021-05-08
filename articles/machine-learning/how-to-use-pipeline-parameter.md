---
title: デザイナーでパイプライン パラメーターを使用して汎用性のあるパイプラインを作成する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーでパイプライン パラメーターを使用する方法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 03/19/2021
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 09eabffb0e01ee6c5ea6b541378773a7d60397a3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080724"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>デザイナーでパイプライン パラメーターを使用して汎用性のあるパイプラインを作成する

デザイナーでパイプライン パラメーターを使用して、柔軟なパイプラインを作成します。 パイプライン パラメーターを使用すると、実行時に値を動的に設定して、パイプライン ロジックをカプセル化し、資産を再利用できます。

パイプライン パラメーターが特に役立つのは、パイプライン実行の再送信、[モデルの再トレーニング](how-to-retrain-designer.md)、または[バッチ予測の実行](how-to-run-batch-predictions-designer.md)をする場合です。

この記事では、次のことを行う方法について説明します。

> [!div class="checklist"]
> * パイプライン パラメーターを作成する
> * パイプライン パラメーターを削除および管理する
> * パイプライン パラメーターの調整中にパイプライン実行をトリガーする

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。

* デザイナーのガイド付き概要を知るには、[デザイナーのチュートリアル](tutorial-designer-automobile-price-train-score.md)を完了してください。 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>パイプライン パラメーターを作成する

デザイナーでパイプライン パラメーターを作成するには、3 つの方法があります。
- [設定] パネルでパイプライン パラメーターを作成し、それをモジュールにバインドする。
- モジュール パラメーターをパイプライン パラメーターにレベル上げする。
- データセットをパイプライン パラメーターにレベル上げする

> [!NOTE]
> パイプライン パラメーターでは、`int`、`float`、`string` などの基本データ型のみがサポートされています。

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>オプション 1: [設定] パネルでパイプライン パラメーターを作成する

このセクションでは、[設定] パネルでパイプライン パラメーターを作成します。

この例では、**Clean missing data** モジュールを使用して、パイプラインで欠落データを埋める方法を定義するパイプライン パラメーターを作成します。

1. パイプライン ドラフトの名前の横にある **歯車アイコン** を選択して、 **[設定]** パネルを開きます。

1. **[パイプライン パラメーター]** セクションで、 **+** アイコンを選択します。

1.  パラメーターの名前と既定値を入力します。 

    たとえば、パラメーター名として `replace-missing-value`、既定値として `0` と入力します。

   ![パイプライン パラメーターの作成方法を示すスクリーンショット](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


パイプライン パラメーターを作成したら、動的に設定する[モジュール パラメーターにそれを接続する](#attach-module-parameter-to-pipeline-parameter)必要があります。

### <a name="option-2-promote-a-module-parameter"></a>オプション 2: モジュール パラメーターをレベル上げする

モジュール値のパイプライン パラメーターを作成する最も簡単な方法は、モジュール パラメーターをレベル上げすることです。 モジュール パラメーターをパイプライン パラメーターにレベル上げするには、次の手順を使用します。

1. パイプライン パラメーターを接続するモジュールを選択します。
1. モジュールの詳細ペインで、指定するパラメーターの上にマウスを移動します。
1. 表示される省略記号 ( **...** ) を選択します。
1. **[Add to pipeline parameter]\(パイプライン パラメーターに追加する\)** を選択します。

    ![モジュール パラメーターをパイプライン パラメーターにレベル上げする方法を示すスクリーンショット](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. パラメーター名と既定値を入力します。
1. **[保存]** を選びます。

これで、このパイプラインを送信するたびに、このパラメーターに新しい値を指定できるようになりました。

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>オプション 3: データセットをパイプライン パラメーターにレベル上げする

変数データセットを使用してパイプラインを送信する場合は、データセットをパイプラインパラメーターにレベル上げする必要があります。

1. パイプライン パラメーターに変換するデータ セットを選択します。

1. データセットの詳細パネルで、 **[Set as pipeline parameter]\(パイプライン パラメーターとして設定\)** チェックボックスをオンにします。

   ![データセットをパイプライン パラメーターとして設定する方法を示すスクリーンショット](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

これで、次にパイプラインを実行したときに、パイプライン パラメーターを使用して、別のデータセットを指定できるようになりました。

## <a name="attach-module-parameter-to-pipeline-parameter"></a>モジュール パラメーターをパイプライン パラメーターに接続する 

このセクションでは、モジュール パラメーターをパイプライン パラメーターに接続する方法について説明します。

パイプライン実行をトリガーするときに同時に値を変更する必要がある場合は、重複したモジュールの同じモジュール パラメーターを同じパイプライン パラメーターに接続します。

次の例では、重複した **Clean Missing Data** モジュールがあります。 各 **Clean Missing Data** モジュールに対して、パイプライン パラメーター **replace-missing-value** に **置換値** を接続します。

1. **[Clean Missing Data]\(見つからないデータのクリーンアップ\)** モジュールを選択します。

1. キャンバスの右側にあるモジュールの詳細ペインで、 **[Cleaning Mode]\(整理モード\)** を [Custom substitution value]\(カスタムの置換値\) に設定します。

1. **[Replacement value]\(置換値\)** フィールドの上にマウスを移動します。

1. 表示される省略記号 ( **...** ) を選択します。

1. パイプライン パラメーター `replace-missing-value` を選択します。

   ![パイプライン パラメーターの接続方法を示すスクリーンショット](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

**[Replacement value]\(置換値\)** フィールドがパイプライン パラメーターに正常に接続されました。 モジュールの **[Replacement value]\(置換値\)** は、使用できません。

 ![パイプライン パラメーターに接続した後に使用不可能であることを示すスクリーンショット](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)


## <a name="update-and-delete-pipeline-parameters"></a>パイプライン パラメーターを更新および削除する

このセクションでは、パイプライン パラメーターを更新および削除する方法について説明します。

### <a name="update-pipeline-parameters"></a>パイプライン パラメーターを更新する

モジュール パイプライン パラメーターを更新するには、次の手順を使用します。

1. キャンバスの上部にある歯車アイコンを選択します。
1. **[パイプライン パラメーター]** セクションで、すべてのパイプライン パラメーターの名前と既定値を表示して更新できます。

### <a name="delete-a-dataset-pipeline-parameter"></a>データセット パイプライン パラメーターを削除する

データセット パイプライン パラメーターをデタッチするには、次の手順を使用します。

1. データセット モジュールを選択します。
1. **[Set as pipeline parameter]\(パイプライン パラメーターとして設定\)** オプションをオフにします。


### <a name="delete-module-pipeline-parameters"></a>モジュール パイプライン パラメーターを削除する

モジュール パイプライン パラメーターを削除するには、次の手順を使用します。

1. キャンバスの上部にある歯車アイコンを選択します。

1. パイプライン パラメーターの横にある省略記号 ( **...** ) を選択します。

    このビューには、パイプライン パラメーターが接続されているモジュールが表示されます。 パイプライン パラメーターを削除するには、まずすべてのモジュール パラメーターからそれをデタッチする必要があります。

    ![モジュールに適用されている現在のパイプライン パラメーターを示すスクリーンショット](media/how-to-use-pipeline-parameter/current-pipeline-parameter.png)

1. キャンバスで、パイプライン パラメーターが依然として接続されているモジュールを選択します。
1. 右側にあるモジュールのプロパティ ペインで、パイプライン パラメーターが接続されているフィールドを見つけます。
1. 接続されたフィールドの上にマウスを移動します。 次に、表示される省略記号 ( **...** ) を選択します。
1. **[Detach from pipeline parameter]\(パイプライン パラメーターからデタッチする\)** を選択します。

    ![パイプライン パラメーターからのデタッチを示すスクリーンショット](media/how-to-use-pipeline-parameter/detach-from-pipeline-parameter.png)

1. すべてのフィールドからパイプライン パラメーターをデタッチするまで、前の手順を繰り返します。
1. パイプライン パラメーターの横にある省略記号 ( **...** ) を選択します。
1. **[パラメーターの削除]** を選択して、パイプライン パラメーターを削除します。

    ![パイプライン パラメーターの削除を示すスクリーンショット](media/how-to-use-pipeline-parameter/delete-pipeline-parameter.png)

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>パイプライン パラメーターを指定してパイプライン実行をトリガーする 

このセクションでは、パイプライン パラメーターの設定と同時にパイプライン実行を送信する方法について説明します。

### <a name="resubmit-a-pipeline-run"></a>パイプライン実行を再送信する

パイプライン パラメーターを指定してパイプラインを送信した後、異なるパラメーターを指定してパイプライン実行を再送信できます。

1. パイプラインの詳細ページにアクセスします。 **[Pipeline run overview]\(パイプライン実行の概要\)** ウィンドウで、現在のパイプラインのパラメーターと値を確認できます。

1. **[再送信]** を選択します。
1. **[パイプライン実行のセットアップ]** で、新しいパイプライン パラメーターを指定します。 

![パイプライン パラメーターを指定したパイプラインの再送信を示すスクリーンショット](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>公開されたパイプラインを使用する

パイプラインを公開して、そのパイプライン パラメーターを使用することもできます。 **公開されたパイプライン** は、コンピューティング リソースにデプロイされたパイプラインであり、クライアント アプリケーションから REST エンドポイント経由で呼び出すことができます。

公開されたエンドポイントは、再トレーニングとバッチ予測のシナリオに特に役立ちます。 詳細については、[デザイナーでモデルを再トレーニングする方法](how-to-retrain-designer.md)または[デザイナーでのバッチ予測の実行](how-to-run-batch-predictions-designer.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、デザイナーでパイプライン パラメーターを作成する方法について学習しました。 次は、パイプライン パラメーターを使用して、[モデルを再トレーニング](how-to-retrain-designer.md)したり、[バッチ予測](how-to-run-batch-predictions-designer.md)を実行したりする方法を確認します。

また、[SDK を使用してプログラムでパイプラインを使用](how-to-deploy-pipelines.md)する方法についても学習できます。
