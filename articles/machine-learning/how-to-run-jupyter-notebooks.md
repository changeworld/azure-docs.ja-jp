---
title: ワークスペースで Jupyter ノートブックを実行する方法
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオのワークスペースから離れずに Jupyter ノートブックを実行する方法について説明します。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 7bb1ce8141f609feb4f354aa85f202915e197f37
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599311"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>ワークスペースで Jupyter ノートブックを実行する方法

Azure Machine Learning スタジオのワークスペースで Jupyter ノートブックを直接実行する方法について説明します。 [Jupyter](https://jupyter.org/) または [JupyterLab](https://jupyterlab.readthedocs.io) を起動できますが、ワークスペースから離れずにノートブックを編集して実行することもできます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://aka.ms/AMLFree) を作成してください。
* Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。

## <a name="create-notebooks"></a><a name="create"></a> ノートブックを作成する

Azure Machine Learning ワークスペースで、新しい Jupyter ノートブックを作成して作業を開始します。 新しく作成されたノートブックは、既定のワークスペース ストレージに格納されます。 このノートブックは、ワークスペースにアクセスできるすべてのユーザーと共有できます。 

新しいノートブックを作成するには: 

1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[Notebooks]\(ノートブック\)** を選択します。 
1. **[My files]\(マイ ファイル\)** セクションの **[User files]\(ユーザー ファイル\)** リストの上にある **[新しいファイルの作成]** アイコンを選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="[新しいファイルの作成]":::

1. ファイルに名前を付けます。 
1. Jupyter ノートブック ファイルの場合は、ファイルの種類として **[ノートブック]** を選択します。
1. ファイル ディレクトリを選択します。
1. **［作成］** を選択します

テキスト ファイルを作成することもできます。  ファイルの種類として **[テキスト]** を選択し、拡張子を名前に追加します (たとえば、myfile.py、myfile.txt)。  

[Notebooks]\(ノートブック\) ページの上部にあるツールを使用して、ノートブックを含むフォルダーとファイルをアップロードすることもできます。  ノートブックとほとんどのテキスト ファイルの種類はプレビュー セクションに表示されます。  他のほとんどのファイルの種類ではプレビューを使用できません。

> [!IMPORTANT]
> ノートブックやスクリプトの内容によっては、Azure の組織なしでセッションからデータを読み取ったり、データにアクセスしたりできる可能性があります。  信頼できるソースのファイル以外は読み込まないでください。 詳細については、[ソース コードのベスト プラクティス](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)に関する記事をご覧ください。

### <a name="clone-samples"></a>サンプルを複製する

ワークスペースには **Samples** フォルダーがあり、SDK を探索するために役立ち、独自の機械学習プロジェクトの例として利用できるように設計されたノートブックが格納されています。  これらのノートブックをワークスペース ストレージ コンテナーに作成したフォルダーに複製することができます。  

例については、「[Tutorial:初めての ML 実験を作成する](tutorial-1st-experiment-sdk-setup.md#azure)」を参照してください。

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Git のファイルを使用し、ファイルのバージョン管理を行う

ターミナル ウィンドウを使用してすべての Git 操作にアクセスできます。 すべての Git ファイルとフォルダーは、ワークスペース ファイル システムに格納されます。

> [!NOTE]
> すべての Jupyter 環境で表示されるように、 **~/cloudfiles/code/Users** フォルダー以下の任意の場所にファイルとフォルダーを追加します。

ターミナルにアクセスするには:

1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[Notebooks]\(ノートブック\)** を選択します。
1. 左側の **[User files]\(ユーザー ファイル\)** セクションにある任意のノートブックを選択します。  ノートブックがない場合は、まず[ノートブックを作成](#create)します
1. **[コンピューティング]** 先を選択するか、新しいものを作成し、それが実行されるまで待ちます。
1. **[Open terminal]\(ターミナルを開く\)** アイコンを選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="ターミナルを開きます":::

1. アイコンが表示されない場合は、コンピューティング ターゲットの右側にある **[...]** を選択し、 **[ターミナルを開く]** を選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="[...] の [Open terminal]\(ターミナルを開く\)":::


詳細については、[Git リポジトリをワークスペース ファイル システムに複製する](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)方法に関する記事を参照してください。

### <a name="copy-and-paste-in-terminal"></a>ターミナルでのコピーと貼り付け

> * Windows: コピーの場合は `Ctrl-Insert`、貼り付けの場合は `Ctrl-Shift-v` または `Shift-Insert` を使用します。
> * Mac OS: コピーの場合は `Cmd-c`、貼り付けの場合は `Cmd-v` を使用します。
> * Firefox や IE ではクリップボードのアクセス許可が正しくサポートされない場合があります。

### <a name="share-notebooks-and-other-files"></a>ノートブックとその他のファイルを共有する

ノートブックまたはファイルを共有するには、URL をコピーして貼り付けます。  この URL にアクセスできるのは、ワークスペースの他のユーザーだけです。  詳細については、[ワークスペースへのアクセスの許可](how-to-assign-roles.md)に関する記事を参照してください。

## <a name="edit-a-notebook"></a>ノートブックを編集する

ノートブックを編集するには、ワークスペースの **[User files]\(ユーザー ファイル\)** セクションにあるノートブックを開きます。 編集するセルをクリックします。 

ノートブックは、コンピューティング インスタンスに接続することなく編集できます。  ノートブックのセルを実行する場合は、コンピューティング インスタンスを選択または作成します。  停止したコンピューティング インスタンスを選択した場合は、最初のセルを実行すると自動的に開始されます。

コンピューティング インスタンスの実行中に、任意の Python ノートブックで [Intellisense](https://code.visualstudio.com/docs/editor/intellisense) によるコード補完を使用することもできます。

ノートブックのツールバーから Jupyter または JupyterLab を起動することもできます。  Azure Machine Learning では、Jupyter または JupyterLab からの更新プログラムの提供やバグの修正は行われません。これは、Microsoft サポートの範囲に含まれないオープン ソース製品であるためです。

### <a name="focus-mode"></a>フォーカス モード

フォーカス モードを使用して現在のビューを拡張すると、アクティブなタブに集中できるようになります。 フォーカス モードでは、Notebook ファイル エクスプローラーが非表示になります。

1. ターミナル ウィンドウのツールバーで、 **[フォーカス モード]** を選択してフォーカス モードをオンにします。 ウィンドウの幅によっては、ツールバーの **[...]** メニュー項目の下にあります。
1. フォーカス モードになっているときに標準ビューに戻るには、 **[標準ビュー]** を選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="フォーカス モード/標準ビューの切り替え":::


### <a name="use-intellisense"></a>IntelliSense を使用する

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) は、次のような多くの機能を備えたコード補完支援機能です: メンバーの一覧表示、パラメーター ヒント、クイック ヒント、入力候補。 これらの機能を使うと、数回のキーストロークだけで使用するコードの詳細を確認したり、入力するパラメーターを追跡したり、プロパティやメソッドの呼び出しを追加したりできます。  

コードを入力するときは、Ctrl + Space キーを使用して IntelliSense をトリガーします。

### <a name="clean-your-notebook-preview"></a>ノートブックをクリーンアップする (プレビュー)

> [!IMPORTANT]
> 現在、この収集機能はパブリック プレビュー段階にあります。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ノートブックを作成しているうちに、一般的にはデータの探索やデバッグに使用したセルが溜まっていきます。 *収集* 機能を使用すると、これらの不要なセルが含まれないクリーンなノートブックを作成できます。

1. すべてのノートブック セルを実行します。
1. 新しいノートブックを実行するコードが含まれているセルを選択します。 たとえば、実験を送信するコードや、あるいはモデルを登録するコードなどです。
1. セルのツールバーに表示される **[収集]** アイコンを選択します。
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="スクリーンショット: [収集] アイコンを選択します":::
1. 新しい "収集された" ノートブックの名前を入力します。  

新しいノートブックにはコード セルのみが含まれており、収集するよう選択したセルと同じ結果を得るために必要なすべてのセルが含まれています。

### <a name="save-and-checkpoint-a-notebook"></a>ノートブックを保存およびチェックポイントする

*ipynb* ファイルを作成すると、Azure Machine Learning によってチェックポイント ファイルが作成されます。

ノートブック ツール バーでメニューを選択し、 **[ファイル] &gt; [Save and checkpoint]\(保存とチェックポイント\)** の順に選択して、手動でノートブックを保存します。そうすると、ノートブックに関連付けられたチェックポイント ファイルが追加されます。

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="ノートブック ツール バーの保存ツールのスクリーンショット":::

すべてのノートブックは 30 秒ごとに自動保存されます。 自動保存では、チェックポイント ファイルではなく、最初の *ipynb* ファイルのみが更新されます。
 
ノートブックのメニューで **[チェックポイント]** を選択して、名前付きチェックポイントを作成し、保存されているチェックポイントにノートブックを戻すことができます。

## <a name="delete-a-notebook"></a>ノートブックを削除する

**Samples** ノートブックを削除することは "*できません*"。  これらのノートブックは Studio の一部であり、新しい SDK が発行されるたびに更新されます。  

**ユーザー ファイル** ノートブックは、次のいずれかの方法で削除 "*できます*"。

* Studio で、フォルダーまたはファイルの末尾にある **[...]** を選択します。  必ずサポートされているブラウザー (Microsoft Edge、Chrome、または Firefox) を使用してください。
* 任意のノートブック ツールバーから [ **[Open terminal]\(ターミナルを開く\)** ](#terminal) を選択して、コンピューティング インスタンスのターミナル ウィンドウにアクセスします。
* Jupyter または JupyterLab で付属のツールを使用します。

## <a name="run-a-notebook-or-python-script"></a>ノートブックまたは Python スクリプトを実行する

ノートブックまたは Python スクリプトを実行するには、まず、実行中の[コンピューティング インスタンス](concept-compute-instance.md)に接続します。 コンピューティング インスタンスがない場合は、次の手順に従って作成します。 

1. ノートブックまたはスクリプトのツールバーで **[+]** を選択します。 
2. コンピューティングに名前を付け、 **[仮想マシン サイズ]** を選択します。 
3. **［作成］** を選択します
4. コンピューティング インスタンスがファイルに自動的に接続されます。  これで、コンピューティング インスタンスの左側にあるツールを使用して、ノートブック セルまたは Python スクリプトを実行できるようになります

作成したコンピューティング インスタンスを表示および使用できるのは自分のみです。  **ユーザー ファイル** は VM とは別に格納され、ワークスペース内のすべてのコンピューティング インスタンス間で共有されます。

### <a name="view-logs-and-output"></a>ログと出力を表示する

[ノートブック ウィジェット](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py)を使用して、実行の進行状況とログを表示します。 ウィジェットは非同期であり、トレーニングが終了するまで更新情報が表示されます。 Azure Machine Learning ウィジェットは、Jupyter および JupterLab でもサポートされています。

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="スクリーンショット: Jupyter ノートブック ウィジェット ":::

## <a name="explore-variables-in-the-notebook"></a>ノートブックの変数を調べる

Notebook ツールバーの **変数エクスプローラー** ツールを使用すると、ノートブックに作成されているすべての変数の名前、型、長さ、サンプル値が表示されます。

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="スクリーンショット: 変数エクスプローラー ツール":::

ツールを選択して、変数エクスプローラー ウィンドウを表示します。

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="スクリーンショット: 変数エクスプローラー ウィンドウ":::

## <a name="navigate-with-a-toc"></a>目次を使用して移動する

目次を表示または非表示にするには、ノートブック ツールバーの **[目次]** ツールを使用します。  見出しを使用してマークダウン セルを開始し、それを目次に追加します。 目次のエントリをクリックして、ノートブック内のそのセルまでスクロールします。  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="スクリーンショット: ノートブックの目次":::

## <a name="change-the-notebook-environment"></a>ノートブック環境を変更する

ノートブックのツールバーを使用すると、ノートブックを実行する環境を変更できます。  

このようなアクションを実行しても、ノートブックの状態またはノートブック内の変数の値は変更されません。

|アクション  |結果  |
|---------|---------| --------|
|カーネルを停止する     |  任意の実行中のセルを停止します。 セルを実行すると、カーネルが自動的に再起動されます。 |
|別のワークスペース セクションに移動する     |     実行中のセルは停止されます。 |

これらのアクションを実行すると、ノートブックの状態はリセットされ、ノートブック内のすべての変数がリセットされます。

|アクション  |結果  |
|---------|---------| --------|
| カーネルを変更する | Notebook に新しいカーネルが使用されます |
| コンピューティングを切り替える    |     Notebook には新しいコンピューティングが自動的に使用されます。 |
| コンピューティングをリセットする | セルを実行しようとすると再起動します |
| コンピューティングを停止する     |    セルは実行されません  |
| Jupyter または JupyterLab でノートブックを開く     |    新しいタブでノートブックが開きます。  |

### <a name="add-new-kernels"></a>新しいカーネルを追加する

ノートブックによって、接続されたコンピューティング インスタンスにインストールされているすべての Jupyter カーネルが自動的に検出されます。  コンピューティング インスタンスにカーネルを追加するには:

1. ノートブックのツールバーで [ **[Open terminal]\(ターミナルを開く\)** ](#terminal) を選択します。
1. ターミナル ウィンドウを使用して、新しい環境を作成します。  たとえば、次のコードでは `newenv` が作成されます。
    ```shell
    conda create -y --name newenv
    ```
1. 環境をアクティブにします。  `newenv` を作成した後の例を次に示します。

    ```shell
    conda activate newenv
    ```
1. pip と ipykernel パッケージを新しい環境にインストールし、その conda 環境用のカーネルを作成します

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

> [!NOTE]
> Notebook 内のパッケージ管理については、すべてのパッケージ (現在実行されているカーネルの外部のパッケージを含む) を参照する **!pip** または **!conda** ではなく、 **%pip** または **%conda** マジック関数を使用して、**現在実行中のカーネル** にパッケージを自動的にインストールします

任意の[使用可能な Jupyter カーネル](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)をインストールできます。

### <a name="status-indicators"></a>状態インジケーター

**[コンピューティング]** ドロップダウンの横のインジケーターには、その状態が表示されます。  状態は、ドロップダウン リストにも表示されます。  

|Color |コンピューティング状態 |
|---------|---------| 
| [緑] | コンピューターは実行中 |
| [赤] |コンピューティングの失敗 | 
| Black | コンピューティングの停止 |
|  淡い青 |コンピューティングの作成、開始、再起動、設定 |
|  グレー |コンピューティングの削除、停止 |

**[カーネル]** ドロップダウンの横にあるインジケーターには、その状態が表示されます。

|Color |カーネルの状態 |
|---------|---------|
|  [緑] |カーネル接続済み、アイドル、ビジー|
|  グレー |カーネルが接続されていません |

## <a name="shortcut-keys"></a>ショートカット キー
Jupyter Notebook と同様に、Azure Machine Learning Studio ノートブックにはモーダル ユーザー インターフェイスがあります。 キーボードの動作は、ノートブック セルのモードによって異なります。 Azure Machine Learning Studio ノートブックでは、特定のコード セルに対して、コマンド モードと編集モードという 2 つのモードがサポートされています。

### <a name="command-mode-shortcuts"></a>コマンド モードのショートカット

入力を求めるテキスト カーソルがない場合、セルはコマンド モードになります。 セルがコマンド モードの場合、ノートブックを全体として編集できますが、個々のセルに入力することはできません。 `ESC` キーを押すか、マウスを使用してセルのエディター領域の外側を選択し、コマンド モードに入ります。  アクティブなセルの左側の境界線は青い実線で、 **[実行]** ボタンは青です。

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="コマンド モードのノートブック セル ":::

| ショートカット                      | 説明                          |
| ----------------------------- | ------------------------------------|
| Enter                         | 編集モードを開始する             |        
| Shift + Enter                 | セルを実行し、下のものを選択する         |     
| Ctrl/Command + Enter       | セルの実行                            |
| Alt + Enter                   | セルを実行し、下にコード セルを挿入する    |
| Ctrl/Command + Alt + Enter | セルを実行し、下にマークダウン セルを挿入する|
| Alt + R                       | [すべて実行]      |                       
| Y                             | セルをコードに変換する    |                         
| M                             | セルをマークダウンに変換する  |                       
| ↑/K                          | 上のセルを選択する    |               
| ↓/J                        | 下のセルを選択する    |               
| A                             | 上にコード セルを挿入する  |            
| B                             | 下にコード セルを挿入する   |           
| Ctrl/Command + Shift + A   | 上にマークダウン セルを挿入する    |      
| Ctrl/Command + Shift + B   | 下にマークダウン セルを挿入する   |       
| X                             | 選択したセルを切り取る    |               
| C                             | 選択したセルをコピーする   |               
| Shift + V                     | 選択したセルを上に貼り付ける           |
| V                             | 選択したセルを下に貼り付ける    |       
| D D                           | 選択したセルを削除する|                
| O                             | TextWriter         |              
| Shift + O                     | 出力のスクロールを切り替える   |          
| I I                           | カーネルを中断する |                   
| 0 0                           | カーネルを再開する |                     
| Shift + Space                 | 上にスクロール  |                         
| Space                         | 下にスクロール|
| タブ                           | フォーカスを次のフォーカス可能な項目に移動する (タブ トラップが無効になっている場合)|
| Ctrl/Command + S           | ノートブックを保存する |                      
| 1                             | h1 に変更する|                       
| 2                             | h2 に変更する|                        
| 3                             | h3 に変更する|                        
| 4                             | h4 に変更する |                       
| 5                             | h5 に変更する |                       
| 6                             | h6 に変更する |                       

### <a name="edit-mode-shortcuts"></a>編集モードのショートカット

編集モードは、エディター領域への入力を求めるテキスト カーソルによって示されます。 セルが編集モードの場合、セルに入力することができます。 `Enter` キーを押すか、マウスを使用してセルのエディター領域を選択し、編集モードに入ります。 アクティブなセルの左側の境界線は緑のハッチで、 **[実行]** ボタンは緑です。 また、編集モードではセルにカーソル プロンプトが表示されます。

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="編集モードのノートブック セル":::

次のキーストローク ショートカットを使用すると、編集モードのときに Azure Machine Learning ノートブックのコードをより簡単に移動して実行できます。

| ショートカット                      | 説明|                                     
| ----------------------------- | ----------------------------------------------- |
| エスケープ                        | コマンド モードを開始する|  
| Ctrl/Command + Space       | IntelliSense をアクティブにする |
| Shift + Enter                 | セルを実行し、下のものを選択する |                         
| Ctrl/Command + Enter       | セルの実行  |                                      
| Alt + Enter                   | セルを実行し、下にコード セルを挿入する  |              
| Ctrl/Command + Alt + Enter | セルを実行し、下にマークダウン セルを挿入する  |          
| Alt + R                       | すべてのセルを実行する     |                              
| ［上へ］                            | カーソルを上または前のセルに移動する    |             
| [下へ]                          | カーソルを下または次のセルに移動する |                  
| Ctrl/Command + S           | ノートブックを保存する   |                                
| Ctrl/Command + ↑          | セルの先頭に移動する   |                             
| Ctrl/Command + ↓        | セルの末尾に移動する |                                 
| タブ                           | コード補完またはインデント (タブ トラップが有効になっている場合) |
| Ctrl/Command + M           | タブ トラップを有効または無効にする  |                       
| Ctrl/Command + ]           | インデントする |                                         
| Ctrl/Command + [           | インデントを解除する  |                                        
| Ctrl/Command + A           | すべて選択する|                                      
| Ctrl/Command + Z           | 元に戻す |                                           
| Ctrl/Command + Shift + Z   | やり直す |                                           
| Ctrl/Command + Y           | やり直す |                                           
| Ctrl/Command + Home        | セルの先頭に移動する|                                
| Ctrl/Command + End         | セルの末尾に移動する   |                               
| Ctrl/Command + 左        | 1 単語左に移動する |                               
| Ctrl/Command + →       | 1 単語右に移動する |                              
| Ctrl/Command + Backspace   | 前の単語を削除する |                             
| Ctrl/Command + Delete      | 後の単語を削除する |                              
| Ctrl/Command + /           | cu でコメントを切り替える

## <a name="find-compute-details"></a>コンピューティングの詳細を確認する

コンピューティング インスタンスの詳細については、[Studio](https://ml.azure.com) の **コンピューティング** に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [最初の実験を実行する](tutorial-1st-experiment-sdk-train.md)
* [スナップショットを使用してファイル ストレージをバックアップする](../storage/files/storage-snapshots-files.md)