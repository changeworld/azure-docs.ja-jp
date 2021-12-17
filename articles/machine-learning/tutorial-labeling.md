---
title: チュートリアル:画像分類のラベル付けプロジェクトを作成する
titleSuffix: Azure Machine Learning
description: 多クラス画像分類モデルで使用できるように画像にラベルを付けるプロセスを管理する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 10/21/2021
ms.custom: data4ml
ms.openlocfilehash: 148b779e56b14652bfb8b9c4eaf0e68585203cf8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061534"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>チュートリアル:多クラス画像分類のラベル付けプロジェクトを作成する 


このチュートリアルでは、機械学習モデルを構築するためのデータとして使用する画像にラベルを付ける (タグ付けとも呼ばれます) プロセスを管理する方法について説明します。 Azure Machine Learning でのデータのラベル付けは、パブリック プレビュー段階にあります。

画像を分類できるように機械学習モデルをトレーニングする場合は、数百または数千もの画像に正しくラベルを付ける必要があります。  Azure Machine Learning では、特定分野のエキスパートからなる社内チームがデータにラベルを付ける際の進捗を管理できます。
 
このチュートリアルでは猫と犬の画像を使用します。  各画像は猫か犬のいずれかであるため、これは "<bpt id="p1">*</bpt>多クラス<ept id="p1">*</ept>" ラベル付けプロジェクトです。 学習内容は次のとおりです。

> [!div class="checklist"]
>
> * Azure ストレージ アカウントを作成し、このアカウントに画像をアップロードします。
> * Azure Machine Learning ワークスペースを作成します。
> * 多クラス画像ラベル付けプロジェクトを作成します。
> * データにラベルを付けます。  この作業は自分で実行してもラベラーが実行してもかまいません。
> * データのレビューとエクスポートを行ってプロジェクトを完了します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、サービス内の簡単に使用できるオブジェクトに結び付けます。

<bpt id="p1">[</bpt>ワークスペースを作成する方法<ept id="p1">](how-to-manage-workspace.md)</ept>は多数あります。 このチュートリアルでは、Azure リソースを管理するための Web ベースのコンソールである Azure portal を使用してワークスペースを作成します。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>ラベル付けプロジェクトを開始する

次に、Azure Machine Learning Studio でデータ ラベル付けプロジェクトを管理します。これは、あらゆるスキル レベルのデータ サイエンス実務者がデータ サイエンス シナリオを実行するための機械学習ツールを含む統合インターフェイスです。 Internet Explorer ブラウザーでは、Studio はサポートされません。

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. お使いのサブスクリプションと、作成したワークスペースを選択します。

### <a name="create-a-datastore"></a><bpt id="p1"><a name="create-datastore"></bpt><ept id="p1"></a></ept>データストアを作成する

Azure Machine Learning データストアは、サブスクリプション ID やトークン承認などの接続情報を格納するために使用されます。 ここでは、データストアを使用して、このチュートリアル用の画像が含まれているストレージ アカウントに接続します。

1. ワークスペースの左側で <bpt id="p1">**</bpt>[データストア]<ept id="p1">**</ept> を選択します。

1. <bpt id="p1">**</bpt>[+ 新しいデータストア]<ept id="p1">**</ept> を選択します。

1. フォームに次の設定を入力します。

    フィールド|説明 
    ---|---
    Datastore name (データストア名) | データストアに名前を付けます。  ここでは <bpt id="p1">**</bpt>labeling_tutorial<ept id="p1">**</ept> を使用します。
    Datastore type (データストアの種類) | ストレージの種類を選択します。  ここでは、画像用の推奨ストレージである <bpt id="p1">**</bpt>Azure Blob Storage<ept id="p1">**</ept> を使用します。
    Account selection method (アカウントの選択方法) | <bpt id="p1">**</bpt>[手動で入力]<ept id="p1">**</ept> を選択します。
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    認証の種類 | <bpt id="p1">**</bpt>[SAS トークン]<ept id="p1">**</ept> を選択します。
    アカウント キー | `ZPlDx0bFHFEqwoy8/B/ZZg1YKi/+cIiPamOPUrRptWbvkO6d84n4loitnSMorv/AxrvE0s86cUr6rULWaSGA2A==`

1. <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択してデータストアを作成します。

### <a name="create-a-labeling-project"></a>ラベル付けプロジェクトを作成する

ラベルを付けるデータにアクセスできるようになったので、ラベル付けプロジェクトを作成します。

1. ページの上部で <bpt id="p1">**</bpt>[プロジェクト]<ept id="p1">**</ept> を選択します。

1. <bpt id="p1">**</bpt>[+ プロジェクトの追加]<ept id="p1">**</ept> を選択します。

    <bpt id="p1">:::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="</bpt>プロジェクトの作成<ept id=&quot;p1&quot;>":::</ept>

### <a name="project-details"></a>プロジェクトの詳細

1. <bpt id="p1">**</bpt>[プロジェクトの詳細]<ept id="p1">**</ept> フォームで次の情報を入力します。

    フィールド|説明 
    ---|---
    プロジェクト名 | プロジェクトに名前を付けます。  ここでは <bpt id="p1">**</bpt>tutorial-cats-n-dogs<ept id="p1">**</ept> を使用します。
    ラベル付けタスクの種類 | <bpt id="p1">**</bpt>[イメージ分類の複数クラス]<ept id="p1">**</ept> を選択します。  
    
    <bpt id="p1">**</bpt>[次へ]<ept id="p1">**</ept> を選択してプロジェクトの作成を続けます。

### <a name="add-workforce-optional"></a>従業員を追加する (省略可能)

<bpt id="p1">**</bpt>[次へ]<ept id="p1">**</ept> を選択して続行します。  このチュートリアルでは、外部の従業員を使用しません。

### <a name="select-or-create-a-dataset"></a>データセットを選択または作成する

1.   <bpt id="p1">**</bpt>[Select or create a dataset]\(データセットの選択または作成\)<ept id="p1">**</ept> フォームで、2 番目の <bpt id="p2">**</bpt>[データセットを作成する]<ept id="p2">**</ept> を選択し、 <bpt id="p3">**</bpt>[データストアから]<ept id="p3">**</ept> リンクを選択します。

1. <bpt id="p1">**</bpt>[Create dataset from datastore]\(データストアからデータセットを作成\)<ept id="p1">**</ept> フォームで次の情報を入力します。

    1. <bpt id="p1">**</bpt>[基本情報]<ept id="p1">**</ept> フォームで名前を追加します。ここでは <bpt id="p2">**</bpt>images-for-tutorial<ept id="p2">**</ept> を使用します。  必要に応じて説明を追加します。  **[次へ]** を選択します。
    1. <bpt id="p1">**</bpt>[データストアの選択]<ept id="p1">**</ept> フォームの <bpt id="p2">**</bpt>[以前に作成されたデータストア]<ept id="p2">**</ept> を選択してデータストア名をクリックし、 <bpt id="p3">**</bpt>[データストアの選択]<ept id="p3">**</ept> を選択します。
    1. 次のページで、現在選択されているデータストアが正しいことを確認します。 正しくない場合は、 <bpt id="p1">**</bpt>[以前に作成されたデータストア]<ept id="p1">**</ept> を選択して、前の手順を繰り返します。
    1. 引き続き <bpt id="p1">**</bpt>[データストアの選択]<ept id="p1">**</ept> フォームで、 <bpt id="p2">**</bpt>[参照]<ept id="p2">**</ept> を選択し、 <bpt id="p3">**</bpt>[MultiClass - DogsCats]<ept id="p3">**</ept> を選択します。  <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> を選択して <bpt id="p2">**</bpt>/MultiClass - DogsCats<ept id="p2">**</ept> をパスとして使用します。
    1. <bpt id="p1">**</bpt>[次へ]<ept id="p1">**</ept> を選択して詳細を確認し、 <bpt id="p2">**</bpt>[作成]<ept id="p2">**</ept> を選択してデータセットを作成します。
    1. リスト内のデータセット名 (例: <bpt id="p1">**</bpt>images-for-tutorial<ept id="p1">**</ept>) の横にある円を選択します。

1. <bpt id="p1">**</bpt>[次へ]<ept id="p1">**</ept> を選択してプロジェクトの作成を続けます。

### <a name="incremental-refresh"></a>増分更新

新しい画像をデータセットに追加する予定がある場合、増分更新によって、それらの新しい画像が検出されてプロジェクトに追加されます。  この機能を有効にすると、新しい画像がないかプロジェクトによって定期的にチェックされます。  このチュートリアルでは新しい画像をデータストアに追加する予定はないので、この機能はオフにしておきます。

**[次へ]** をクリックして続行します。

### <a name="label-classes"></a>ラベル クラス

1. <bpt id="p1">**</bpt>[ラベル クラス]<ept id="p1">**</ept> フォームで、ラベル名を入力し、 <bpt id="p2">**</bpt>[+ ラベルの追加]<ept id="p2">**</ept> を選択して次のラベルを入力します。  このプロジェクトで使用するラベルは、<bpt id="p1">**</bpt>Cat<ept id="p1">**</ept> (猫)、<bpt id="p2">**</bpt>Dog<ept id="p2">**</ept> (犬)、<bpt id="p3">**</bpt>Uncertain<ept id="p3">**</ept> (不明) です。

1. ラベルをすべて追加したら、 <bpt id="p1">**</bpt>[次へ]<ept id="p1">**</ept> を選択します。

### <a name="labeling-instructions"></a>ラベル付けの手順

1. <bpt id="p1">**</bpt>[ラベル付けの手順]<ept id="p1">**</ept> フォームでは、ラベラーに詳細な手順を提供する Web サイトへのリンクを指定できます。  このチュートリアルでは空白のままにします。

1. タスクの簡単な説明をフォームに直接追加することもできます。  「<bpt id="p1">**</bpt>ラベル付けのチュートリアル - 猫と犬<ept id="p1">**</ept>」と入力します。

1. **[次へ]** を選択します。

1. <bpt id="p1">**</bpt>[ML によるラベル付け]<ept id="p1">**</ept> セクションで、チェックボックスをオフのままにします。 ML によるラベル付けでは、このチュートリアルで使用するものよりも多くのデータが必要です。

1. **[プロジェクトの作成]** を選択します。

このページが自動的に更新されることはありません。 しばらくしてから、プロジェクトの状態が <bpt id="p1">**</bpt>[Created]\(作成済み\)<ept id="p1">**</ept> に変わるまでページを手動で更新します。

## <a name="start-labeling"></a>ラベル付けを開始する

これで Azure リソースの設定とデータ ラベル付けプロジェクトの構成が完了しました。 次に、データにラベルを追加します。

### <a name="tag-the-images"></a>画像にタグを付ける

チュートリアルのこのパートでは、ロールを "<bpt id="p1">*</bpt>プロジェクト管理者<ept id="p1">*</ept>" から "<bpt id="p2">*</bpt>ラベラー<ept id="p2">*</ept>" に切り替えます。  ワークスペースに対する共同作成者のアクセス権を持つユーザーは、だれでもラベラーになることができます。

1. <bpt id="p1">[</bpt>Machine Learning Studio<ept id="p1">](https://ml.azure.com)</ept> で、左側にある <bpt id="p2">**</bpt>[データのラベル付け]<ept id="p2">**</ept> を選択してプロジェクトを特定します。  

1. プロジェクトの <bpt id="p1">**</bpt>[ラベル リンク]<ept id="p1">**</ept> を選択します。

1. 指示を読み、 <bpt id="p1">**</bpt>[タスク]<ept id="p1">**</ept> を選択します。

1. 右側のサムネイル画像を選択すると、一度にラベル付けする画像の数が表示されます。 このすべての画像にラベルを付けないと、次に進むことはできません。 レイアウトを切り替えるのは、ラベルの付いていないデータの新しいページがある場合のみにしてください。 レイアウトを切り替えると、ページの進行中のタグ付け作業はクリアされます。

1. 1 つ以上の画像を選択し、それらに適用するタグを選択します。 画像の下にタグが表示されます。  ページのすべての画像に対し、選択してタグを付ける処理を続けます。  表示されているすべての画像を同時に選択するには、 <bpt id="p1">**</bpt>[すべて選択]<ept id="p1">**</ept> を選択します。 タグを適用する画像を少なくとも 1 つ選択してください。


    > [!TIP]
    > キーボードの数字キーを使用して、最初の 9 個のタグを選択できます。

1. ページ上のすべての画像にタグを付けたら、 <bpt id="p1">**</bpt>[送信]<ept id="p1">**</ept> を選択してそれらのラベルを送信します。

    ![イメージへのタグ付け](media/tutorial-labeling/catsndogs.gif)

1. 手元にあるデータのタグを送信すると、Azure によって作業キューの新しい画像セットを使ってページが更新されます。

## <a name="complete-the-project"></a>プロジェクトを完了する

ここで、ラベル付けプロジェクトのロールを "<bpt id="p1">*</bpt>プロジェクト管理者<ept id="p1">*</ept>" に戻します。

マネージャーは、ラベラーの作業をレビューすることができます。  

### <a name="review-labeled-data"></a>ラベル付けされたデータをレビューする

1. <bpt id="p1">[</bpt>Machine Learning Studio<ept id="p1">](https://ml.azure.com)</ept> で、左側にある <bpt id="p2">**</bpt>[データのラベル付け]<ept id="p2">**</ept> を選択してプロジェクトを特定します。  

1. プロジェクト名のリンクを選択します。

1. ダッシュボードにプロジェクトの進行状況が表示されます。

1. ページの上部で <bpt id="p1">**</bpt>[データ]<ept id="p1">**</ept> を選択します。

1. 左側で <bpt id="p1">**</bpt>[ラベルの付いたデータ]<ept id="p1">**</ept> を選択して、タグ付けされた画像を表示します。  

1. ラベルが合っていないと判断した場合は、画像を選択し、ページの下部で <bpt id="p1">**</bpt>[Reject]\(却下\)<ept id="p1">**</ept> を選択します。  タグが削除され、ラベル付けされていない画像のキューに画像が戻されます。

### <a name="export-labeled-data"></a>ラベル付けされたデータをエクスポートする

いつでも、Machine Learning の実験用にラベル データをエクスポートできます。 すべての画像にラベルが付けられるのを待たずに、ユーザーがエクスポートを何度か行ってさまざまなモデルをトレーニングすることがよくあります。

画像のラベルは、<bpt id="p1">[</bpt>COCO 形式<ept id="p1">](http://cocodataset.org/#format-data)</ept>または Azure Machine Learning データセットとしてエクスポートできます。 データセット形式を使用すれば、Azure Machine Learning でのトレーニングが容易になります。  

1. <bpt id="p1">[</bpt>Machine Learning Studio<ept id="p1">](https://ml.azure.com)</ept> で、左側にある <bpt id="p2">**</bpt>[データのラベル付け]<ept id="p2">**</ept> を選択してプロジェクトを特定します。  

1. プロジェクト名のリンクを選択します。

1. <bpt id="p1">**</bpt>[エクスポート]<ept id="p1">**</ept> を選択し、 <bpt id="p2">**</bpt>[Azure ML データセットとしてエクスポート]<ept id="p2">**</ept> を選択します。 

    エクスポートの状態は、 <bpt id="p1">**</bpt>[エクスポート]<ept id="p1">**</ept> ボタンのすぐ下に表示されます。 

1. ラベルがエクスポートされたら、左側で <bpt id="p1">**</bpt>[データセット]<ept id="p1">**</ept> を選択して結果を表示します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>機械学習の画像認識モデルをトレーニングする<ept id="p1">](how-to-use-labeled-dataset.md)</ept>
