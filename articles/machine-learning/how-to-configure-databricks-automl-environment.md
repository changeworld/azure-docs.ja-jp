---
title: autoML と Azure Databricks を使用して開発する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning と Azure Databricks に開発環境をセットアップすることについて説明します。 Databricks 用の Azure ML SDK と、autoML を含む Databricks を使用します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef8ee7718aabb443fda6cd7b276ee53472261913
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423851"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Azure Machine Learning 内に Azure Databricks と autoML を含む開発環境をセットアップする 

Azure Databricks と自動 ML を使用する開発環境を Azure Machine Learning 内に構成する方法について説明します。

Azure Databricks は、Azure クラウド内のスケーラブルな Apache Spark プラットフォームで大規模な集中型機械学習ワークフローを実行する場合に最適です。 これは、CPU または GPU ベースのコンピューティング クラスターにより、コラボレーションに適した Notebook ベースの環境を提供するものです。

その他の機械学習開発環境については、[Python 開発環境のセットアップ](how-to-configure-environment.md)に関するページを参照してください。


## <a name="prerequisite"></a>前提条件

Azure Machine Learning ワークスペース。 Azure Machine Learning ワークスペースがない場合は、[Azure portal](how-to-manage-workspace.md)、[Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)、[Azure Resource Manager テンプレート](how-to-create-workspace-template.md)を使用して作成できます。


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Machine Learning と autoML を使用する Azure Databricks

Azure Databricks は、Azure Machine Learning と、その autoML 機能と統合されています。 

Azure Databricks は、以下のように使用できます。

+ Spark MLlib を使用してモデルをトレーニングし、そのモデルを ACI/AKS にデプロイするため。
+ Azure ML SDK を使用して[自動機械学習](concept-automated-ml.md)機能と一緒に。
+ [Azure Machine Learning パイプライン](concept-ml-pipelines.md)からのコンピューティング先として。

## <a name="set-up-a-databricks-cluster"></a>Databricks クラスターをセットアップする

[Databricks クラスター](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)を作成します。 一部の設定は、Databricks に自動化された機械学習用の SDK をインストールする場合にのみ適用されます。

**クラスターの作成には数分かかります。**

次の設定を使用します。

| 設定 |適用対象| 値 |
|----|---|---|
| クラスター名 |常時| yourclustername |
| Databricks Runtime のバージョン |常時| Runtime 7.1 (scala 2.21、spark 3.0.0) - ML 以外|
| Python バージョン |常時| 3 |
| worker の種類 <br>(同時実行反復処理の最大数を決定) |自動化された ML<br>のみ| メモリ最適化 VM 優先 |
| ワーカー |常時| 2 以上 |
| 自動スケールの有効化 |自動化された ML<br>のみ| オフ |

クラスターが実行中になるまで待機してから、次に進んでください。

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Databricks に Azure ML SDK を追加する

クラスターが実行されたら、[ライブラリを作成](https://docs.databricks.com/user-guide/libraries.html#create-a-library)して、適切な Azure Machine Learning SDK パッケージをクラスターにアタッチします。 

自動 ML を使用するには、[AutoML を含む Azure ML SDK の追加](#add-the-azure-ml-sdk-with-automl-to-databricks)に関するセクションに進んでください。


1. ライブラリを保存する現在のワークスペース フォルダーを右クリックします。 **[作成]**  >  **[ライブラリ]** の順に選択します。
    
    > [!TIP]
    > 前のバージョンの SDK がある場合は、クラスターのインストール済みライブラリでその選択を解除し、ごみ箱に移動します。 新しいバージョンの SDK をインストールし、クラスターを再起動します。 再起動後に問題がある場合は、クラスターをデタッチし、再アタッチします。

1. 次のオプションを選択します (他の SDK のインストールはサポートされていません)

   |SDK&nbsp;パッケージ&nbsp;extras|source|PyPi&nbsp;名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks 用| Python Egg または PyPI のアップロード | azureml-sdk[databricks]|

   > [!WARNING]
   > 他の SDK extras はインストールできません。 [`databricks`] オプションのみを選択します。

   * **[すべてのクラスターに自動的にアタッチする]** は選択しないでください。
   * クラスター名の横にある **[アタッチ]** を選択します。

1. 状態が **[アタッチ済み]** に変わるまで、エラーを監視します。これには数分かかる場合があります。  このステップが失敗した場合は、次の操作を行います。

   次のようにして、クラスターを再起動してみます。
   1. 左側のウィンドウで、 **[クラスター]** を選択します。
   1. テーブルでクラスター名を選択します。
   1. **[ライブラリ]** タブで **[再起動]** を選択します。

   インストールが成功すると次のようになります。 

  ![Databricks 用の Azure Machine Learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>AutoML を含む Azure ML SDK を Databricks に追加する
クラスターが Databricks Runtime 7.1 以降 (ML *以外*) で作成された場合は、ノートブックの最初のセルで次のコマンドを実行し、AML SDK をインストールします。

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Databricks Runtime 7.0 以前の場合は、[init スクリプト](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md)を使用して Azure Machine Learning SDK をインストールします。

### <a name="automl-config-settings"></a>AutoML 構成の設定

AutoML 構成では、Azure Databricks を使用するときに次のパラメーターを追加してください。

- ```max_concurrent_iterations``` は、ご利用のクラスター内のワーカー ノードの数に基づきます。
- ```spark_context=sc``` は、既定の Spark コンテキストに基づきます。

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Azure Databricks と連携動作する ML ノートブック

実際に試してみましょう。
+ サンプル ノートブックがたくさんありますが、**Azure Databricks で動作するのは [これらのサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)のみです。**

+ これらのサンプルは、ワークスペースから直接インポートしてください。 次を参照してください。![インポートの選択](./media/how-to-configure-environment/azure-db-screenshot.png)
![パネルのインポ―ト](./media/how-to-configure-environment/azure-db-import.png)

+ [トレーニング コンピューティングとして Databricks を使用してパイプラインを作成](how-to-create-your-first-pipeline.md)する方法を確認します。

## <a name="next-steps"></a>次のステップ

- MNIST データセットを使用して Azure Machine Learning で[モデルをトレーニング](tutorial-train-models-with-aml.md)します。
- [Azure Machine Learning SDK for Python のリファレンス](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)に関するページを参照してください。
