---
title: Visual Studio Code 拡張機能 (プレビュー) を設定する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Visual Studio Code 拡張機能をセットアップする方法について説明します。
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: bb4035d1757a68cce1bcb57abedb6c805c47a3a2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558385"
---
# <a name="set-up-the-visual-studio-code-azure-machine-learning-extension-preview"></a>Visual Studio Code Azure Machine Learning 拡張機能をセットアップする (プレビュー)

機械学習ワークフローに使用する Azure Machine Learning Visual Studio Code 拡張機能をセットアップする方法について説明します。

> [!div class="mx-imgBorder"]
> ![VS Code 拡張機能](./media/how-to-setup-vs-code/vs-code-extension.PNG)

VS Code 用の Azure Machine Learning 拡張機能には、次の作業を行うためのユーザー インターフェイスが備わっています。

- Azure Machine Learning リソース (実験、仮想マシン、モデル、デプロイなど) を管理する
- ローカルからリモート コンピューティング インスタンスを使用して開発を行う
- 機械学習モデルをトレーニングする
- 機械学習の実験をローカルでデバッグする
- 仕様ファイルの作成に関するスキーマベースの言語サポート、オートコンプリート、診断
- 一般的なタスクのスニペット

## <a name="prerequisites"></a>前提条件

- Azure のサブスクリプション。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) にサインアップしてお試しください。
- Visual Studio Code。 インストールしていない場合は、[インストール](https://code.visualstudio.com/docs/setup/setup-overview)します。
- [Python](https://www.python.org/downloads/)
- (省略可) 拡張機能を使用してリソースを作成するには、CLI (v2) をインストールする必要があります。 設定手順については、[CLI (v2) (プレビュー) のインストール、設定、および使用](how-to-configure-cli.md)に関する記事を参照してください。

## <a name="install-the-extension"></a>拡張機能をインストールする

1. Visual Studio Code を開きます。
1. **アクティビティ バー** の **拡張機能** アイコンを選択して、拡張機能ビューを開きます。
1. 拡張機能ビューで、「Azure Machine Learning」を検索します。
1. **[インストール]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning VS Code 拡張機能をインストールする](./media/how-to-setup-vs-code/install-aml-vscode-extension.PNG)

> [!NOTE]
> または、[インストーラーを直接ダウンロード](https://aka.ms/vscodetoolsforai)して、Visual Studio Marketplace 経由で Azure Machine Learning 拡張機能をインストールすることもできます。

このチュートリアルの残りの手順は、拡張機能の最新バージョンを使用してテストされています。

> [!NOTE]
> Azure Machine Learning VS Code 拡張機能では、既定で CLI (v2) を使用しています。 1\.0 CLI に切り替えるには、Visual Studio Code の `azureML.CLI Compatibility Mode` 設定を `1.0` に設定します。 Visual Studio の設定の変更について詳しくは、[ユーザーとワークスペースの設定に関するドキュメント](https://code.visualstudio.com/docs/getstarted/settings)を参照してください。

## <a name="sign-in-to-your-azure-account"></a>Azure アカウントにサインインする

Azure でリソースをプロビジョニングし、ワークロードを実行するには、Azure アカウントの資格情報でサインインする必要があります。 アカウント管理を支援するために、Azure Machine Learning では Azure アカウント拡張機能が自動的にインストールされます。 Azure アカウント拡張機能の詳細については、[こちらのサイト](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)を参照してしてください。

Azure アカウントにサインインするには、Visual Studio Code のステータス バーにある **[Azure: サインイン]** ボタンを選択してサインイン プロセスを開始します。

または、次のようにコマンド パレットを使用します。

1. メニュー バーから **[表示]、[コマンド パレット]** の順に選択して、コマンド パレットを開きます。
1. コマンド パレットに「> Azure: Sign In」コマンドを入力して、サインイン プロセスを開始します。

## <a name="choose-your-default-workspace"></a>既定のワークスペースを選択する

既定の Azure Machine Learning ワークスペースを選択すると、CLI (v2) YAML 仕様ファイルの作成時に次の機能が有効になります。

- スキーマの検証
- オート コンプリート
- 診断

ワークスペースをお持ちでない場合は、作成してください。 詳細については、[VS Code 拡張機能を使用して Azure Machine Learning リソースを管理する方法](how-to-manage-resources-vscode.md)に関するページを参照してください。

既定のワークスペースを選択するには、Visual Studio Code のステータス バーにある **[Set Azure ML Workspace]\(Azure ML ワークスペースの設定\)** ボタンを選択し、画面の指示に従ってワークスペースを設定します。

または、コマンド パレットの `> Azure ML: Set Default Workspace` コマンドを使用し、画面の指示に従ってワークスペースを設定します。

## <a name="next-steps"></a>次の手順

- [Azure Machine Learning リソースの管理](how-to-manage-resources-vscode.md)
- [ローカルからリモート コンピューティング インスタンスで開発を行う](how-to-set-up-vs-code-remote.md)
- [リモート Jupyter サーバーとしてコンピューティング インスタンスを使用する](how-to-set-up-vs-code-remote.md)
- [Visual Studio Code 拡張機能を使用して画像分類モデルをトレーニングする](tutorial-train-deploy-image-classification-model-vscode.md)
- [機械学習の実験をローカルで実行およびデバッグする](how-to-debug-visual-studio-code.md)