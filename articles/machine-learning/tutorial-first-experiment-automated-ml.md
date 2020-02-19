---
title: 最初の自動 ML 実験を作成する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio で自動機械学習を使用して分類モデルをトレーニングおよびデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 02/04/2020
ms.openlocfilehash: 70fcdb1c22664a0bd3091fea88c8e23e3d1b81e5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048286"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>チュートリアル:自動化された機械学習を使用して最初の分類モデルを作成する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

このチュートリアルでは、コードを 1 行も記述せずに Azure Machine Learning Studio で初めての自動機械学習実験を作成する方法について説明します。 この例では、クライアントが金融機関に定期預金を申し込むかどうかを予測する分類モデルを作成します。

自動機械学習を使用することで、時間がかかるタスクを自動化することができます。 自動機械学習では、アルゴリズムとハイパーパラメーターのさまざまな組み合わせをすばやく反復し、選択された成功のメトリックに基づいて最適なモデルを効率的に発見します。

このチュートリアルでは、次のタスクを実施する方法について説明します。

> [!div class="checklist"]
> * Azure Machine Learning ワークスペースを作成します。
> * 自動機械学習の実験を実行します。
> * 実験の詳細を表示します。
> * モデルをデプロイします。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://aka.ms/AMLFree)を作成してください。

* [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) データ ファイルをダウンロードします。 **[y]** 列では、このチュートリアルの予測対象列として後で識別される定期預金に顧客が申し込んだかどうかが示されます。 

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、サービス内の簡単に使用できるオブジェクトに結び付けます。 

ワークスペースを作成するには、Azure リソースを管理するための Web ベースのコンソールである Azure Machine Learning Studio を使用します。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> お使いの**ワークスペース**と**サブスクリプション**をメモしておきます。 これらは、適切な場所に実験を作成するために必要になります。 

## <a name="create-and-run-the-experiment"></a>実験を作成して実行する

Azure Machine Learning Studio で、次の実験の設定を完了し、ステップを実行します。Azure Machine Learning Studio は、あらゆるスキル レベルのデータ サイエンス実務者がデータ サイエンス シナリオを実行するための機械学習ツールを含む統合インターフェイスです。 Internet Explorer ブラウザーでは、Studio はサポートされません。

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. お使いのサブスクリプションと、作成したワークスペースを選択します。

1. **[Get started]\(作業を開始する\)** を選択します。

1. 左側のウィンドウの **[Author]\(作成者\)** セクションで **[Automated ML]\(自動 ML\)** を選択します。

   これは初めての自動 ML 実験であるため、空のリストとドキュメントへのリンクが表示されます。

   ![Azure Machine Learning Studio](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. **[New automated ML run]\(新しい自動 ML の実行\)** を選択します。 

1. 新しいデータセットを作成するには、 **[+ データセットの作成]** ドロップダウンで **[From local files]\(ローカル ファイルから\)** を選択します。 

    1. **[基本情報]** フォームでデータセットに名前を付け、必要に応じて説明を入力します。 現在、Azure Machine Learning Studio の自動 ML でサポートされるデータセットは表形式のみであるため、データセットの種類は既定で "表形式" に設定されます。

    1. 左下の **[次へ]** を選択します。

    1. **[データストアとファイルの選択]** フォームで、ワークスペースの作成時に自動的に設定された既定のデータストア、**workspaceblobstore (Azure Blob Storage)** を選択します。 データ ファイルは、ここにアップロードすることで、ワークスペースから利用できるようになります。

    1. **[参照]** を選択します。
    
    1. ローカル コンピューター上の **bankmarketing_train.csv** ファイルを選択します。 これは、[前提条件](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)としてダウンロードしたファイルです。

    1. データセットに一意の名前を付け、必要に応じて説明を入力します。 

    1. 左下の **[次へ]** を選択して、ワークスペースの作成時に自動的に設定された既定のコンテナーにアップロードします。  
    
       アップロードが完了すると、ファイルの種類に基づいて [Settings and preview]\(設定とプレビュー\) フォームが事前設定されます。 
       
    1. **[Settings and preview]\(設定とプレビュー\)** フォームが次のように設定されていることを確認し、 **[次へ]** を選択します。
        
        フィールド|説明| チュートリアルの値
        ---|---|---
        ファイル形式|ファイルに格納されているデータのレイアウトと種類を定義します。| 区切り記号
        区切り記号|プレーンテキストまたは他のデータ ストリーム内の個別の独立した領域の間の境界を指定するための&nbsp;1 つ以上の文字。 |コンマ
        エンコード|データセットの読み取りに使用する、ビットと文字のスキーマ テーブルを識別します。| UTF-8
        列見出し| データセットの見出しがある場合、それがどのように処理されるかを示します。| すべてのファイルのヘッダーを同じものにする
        行のスキップ | データセット内でスキップされる行がある場合、その行数を示します。| なし

    1. **[スキーマ]** フォームを使用すると、この実験用にデータをさらに構成できます。 この例では、**day_of_week** 特徴のトグル スイッチを選択して、この実験に含めないようにします。 **[次へ]** を選択します。

        ![[プレビュー] タブの構成](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. **[詳細の確認]** フォームで、 **[基本情報]** および **[Settings and preview]\(設定とプレビュー\)** のフォームに入力された情報が一致していることを確認します。
    1. **[作成]** を選択して、データセットの作成を完了します。
    1. リストにデータセットが表示されたら、それを選択します。
    1. **[Data preview]\(データのプレビュー)** を確認して **[day_of_week]** が含まれていないことを確保してから、 **[OK]** を選択します。

    1. **[次へ]** を選択します。

1. 次のように **[Configure Run]\(構成の実行\)** フォームに入力します。
    1. この実験の名前として「`my-1st-automl-experiment`」と入力します。

    1. 予測するターゲット列として、 **[y]** を選択します。 この列には、クライアントが定期預金を申し込むかどうかが示されます。
    1. **[Create a new compute]\(新しいコンピューティングの作成\)** を選択し、コンピューティング先を構成します。 コンピューティング先とは、トレーニング スクリプトを実行したりサービスのデプロイをホストしたりするために使用されるローカルまたはクラウド ベースのリソース環境です。 この実験では、クラウド ベースのコンピューティングを使用します。 

        フィールド | 説明 | チュートリアルの値
        ----|---|---
        コンピューティング名 |コンピューティング コンテキストを識別する一意名。|automl-compute
        仮想マシンのサイズ&nbsp;&nbsp;| コンピューティングの仮想マシン サイズを選択します。|Standard_DS12_V2
        最小および最大ノード ([詳細設定] 内)| データをプロファイリングするには、1 つ以上のノードを指定する必要があります。|最小ノード: 1<br>最大ノード: 6
  
        1. **[作成]** を選択して、コンピューティング先を取得します。 

            **完了するまでに数分かかります。** 

        1. 作成後、ドロップダウン リストから新しいコンピューティング先を選択します。

    1. **[次へ]** を選択します。

1. **[Task type and settings]\(タスクの種類と設定\)** フォームで、機械学習のタスクの種類として **[分類]** を選択します。

    1. **[View additional configuration settings]\(追加の構成設定を表示\)** を選択し、次のようにフィールドを設定します。 これらは、トレーニング ジョブをより細かく制御するための設定です。 設定しない場合、実験の選択とデータに基づいて既定値が適用されます。

        >[!NOTE]
        > このチュートリアルでは、メトリック スコアまたはイテレーションあたりの最大コア数のしきい値は設定しません。 また、アルゴリズムのテストをブロックしません。
   
        追加の構成&nbsp;|説明|チュートリアル用の値&nbsp;&nbsp;
        ------|---------|---
        主要メトリック| 機械学習アルゴリズムを測定される評価メトリック。|AUC_weighted
        自動特徴付け| 前処理が有効になります。 これには、合成的特徴を生成するための自動データ クレンジング、準備、変換が含まれます。| [有効化]
        ブロックされたアルゴリズム | トレーニング ジョブから除外するアルゴリズム| なし
        終了条件| 条件が満たされると、トレーニング ジョブが停止します。 |トレーニング&nbsp;ジョブ時間 (時間):&nbsp;1 <br> メトリック&nbsp;スコアしきい値:&nbsp;なし
        検証 | クロス検証タイプとテストの回数を選択します。|検証タイプ:<br>&nbsp;k 分割交差検証&nbsp; <br> <br> 検証の数: 2
        コンカレンシー| イテレーションごとに実行される並列イテレーションの最大数| &nbsp;最大同時イテレーション数:&nbsp;5
        
        **[保存]** を選択します。

1. **[完了]** を選択して実験を実行します。 実験の準備が開始されると、 **[Run Detail]\(実行の詳細\)** 画面が開いて、一番上に **[Run status]\(実行の状態\)** が表示されます。

>[!IMPORTANT]
> 実験の実行の準備に、**10 から 15 分**かかります。
> 実行の開始後、**各イテレーションのためにさらに 2、3 分**かかります。  
> 実験の進行に応じて実行の状態を確認するには、 **[最新の情報に更新]** を定期的に選択します。
>
> 運用環境では、しばらく席を離れるかもしれません。 ただし、このチュートリアルでは、他のイテレーションが実行中でも、アルゴリズムのテストが終わりしだい、 **[モデル]** タブで調査することをお勧めします。 

##  <a name="explore-models"></a>モデルを調査する

**[モデル]** タブに移動し、テストされたアルゴリズム (モデル) を確認します。 既定では、モデルは完了時のメトリック スコアで並べ替えられます。 このチュートリアルでは、選択した **AUC_weighted** メトリックに基づいて最も高いスコアを獲得したモデルがリストの一番上に表示されます。

すべての実験モデルが終了するのを待っている間に、完了したモデルの **[アルゴリズム名]** を選択して、そのパフォーマンスの詳細を調査します。 

次の例では、 **[モデルの詳細]** と **[視覚化]** のタブを移動して、選択したモデルのプロパティ、メトリック、およびパフォーマンス グラフを表示しています。 

![イテレーションの実行の詳細](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>モデルをデプロイする

Azure Machine Learning Studio で自動機械学習を使用すると、わずかなステップで最良のモデルを Web サービスとしてデプロイすることができます。 デプロイとは、新しいデータを予測したり、潜在的な機会領域を特定したりできるようにモデルを統合することです。 

この実験における Web サービスへのデプロイは、定期預金の潜在顧客を特定するためのスケーラブルな反復 Web ソリューションを金融機関が持つことを意味します。 

実行が完了したら、 **[Run Detail]\(実行の詳細\)** ページに戻り、 **[モデル]** タブを選択します。

この実験では、**VotingEnsemble** は **AUC_weighted** メトリックに基づいて最適なモデルと見なされます。  このモデルをデプロイしますが、デプロイには完了まで約 20 分かかることにご留意ください。 デプロイ プロセスには、モデルを登録したり、リソースを生成したり、Web サービス用にそれらを構成したりすることを含む、いくつかの手順が伴います。

1. 左下隅の **[Deploy best model]\(最適なモデルのデプロイ\)** ボタンを選択します。

1. **[Deploy a model]\(モデルのデプロイ\)** ペインに次のように入力します。

    フィールド| Value
    ----|----
    デプロイ名| my-automl-deploy
    デプロイの説明| 初めての自動機械学習実験のデプロイ
    コンピューティングの種類 | Azure のコンピューティング インスタンス (ACI) を選択します。
    認証を有効にする| 無効。 
    Use custom deployments (カスタム デプロイを使用する)| 無効。 既定のドライバー ファイル (スコアリング スクリプト) と環境ファイルが自動的に生成されます。 
    
    この例では、 *[Advanced]\(詳細\)* メニューに指定されている既定値を使用します。 

1. **[デプロイ]** を選択します。  

    **[実行]** 画面の上部に成功を示す緑色のメッセージが表示され、 **[Recommended model]\(推奨モデル\)** ペインの **[Deploy status]\(デプロイ状態\)** にステータス メッセージが表示されます。 **[最新の情報に更新]** を定期的にクリックして、デプロイの状態を確認します。
    
これで、予測を生成するための実稼働 Web サービスが作成されました。 

新しい Web サービスの使い方、Azure Machine Learning サポートに組み込まれている Power BI を使用した予測のテスト方法の詳細については、[**次のステップ**](#next-steps)に進みます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

デプロイ ファイルはデータ ファイルと実験ファイルよりも大きいため、格納コストは高くなります。 アカウントのコストを最小限に抑える場合、またはワークスペースと実験ファイルを保持する場合は、デプロイ ファイルだけを削除します。 それ以外の場合で、いずれのファイルも使用する予定がない場合は、リソース グループ全体を削除します。  

### <a name="delete-the-deployment-instance"></a>デプロイ インスタンスの削除

他のチュートリアルや探索用にリソース グループとワークスペースを維持する場合は、Azure Machine Learning Studio からデプロイ インスタンスだけを削除します。 

1. [Azure Machine Learning Studio](https://ml.azure.com/) に移動します。 お使いのワークスペースに移動し、左側の **[アセット]** ウィンドウの下の **[エンドポイント]** を選択します。 

1. 削除するデプロイを選択し、 **[削除]** を選択します。 

1. **[続行]** を選択します。

### <a name="delete-the-resource-group"></a>リソース グループを削除します

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

この自動機械学習チュートリアルでは、Azure Machine Learning Studio を使用して分類モデルの作成とデプロイを行いました。 詳細と次の手順については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Web サービスを使用する](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ [特徴付け](how-to-create-portal-experiments.md#featurization)についてさらに理解を深める。
+ [データ プロファイル](how-to-create-portal-experiments.md#profile)についてさらに理解を深める。
+ [自動機械学習](concept-automated-ml.md)についてさらに理解を深める。
+ 分類メトリックとグラフの詳細については、「[自動化機械学習の結果の概要](how-to-understand-automated-ml.md#classification)」の記事を参照してください。

>[!NOTE]
> この Bank Marketing データセットは、[クリエイティブ コモンズ (CCO:パブリック ドメイン) ライセンス](https://creativecommons.org/publicdomain/zero/1.0/)により利用できます。 データベースの個々のコンテンツに含まれる権限は、[データベース コンテンツ ライセンス](https://creativecommons.org/publicdomain/zero/1.0/)によりライセンス供与され、[Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) で入手できます。 このデータセットのオリジナルは、[UCI Machine Learning データベース](https://archive.ics.uci.edu/ml/datasets/bank+marketing)から入手できます。<br><br>
> [Moro その他、2014 年] S. Moro、P. Cortez、Rita。 銀行のテレマーケティングの成功を予測するためのデータドリブン アプローチ。 意思決定支援システム、Elsevier、62:22-31、2014 年 6 月。
