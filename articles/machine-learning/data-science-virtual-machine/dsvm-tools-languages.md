---
title: サポートされている言語
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine にプレインストールされているサポート対象のプログラム言語および関連ツール。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: fcb2a4d9860687fb18409666f81839002329d54e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070900"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンでサポートされている言語 

Data Science Virtual Machine (DSVM) には、人工知能 (AI) アプリケーションを構築するための事前構築済み言語および開発ツールがいくつか付属します。 重要なものをいくつか紹介します。

## <a name="python"></a>Python

| カテゴリ | 値 |
|--|--|
| サポートされている言語バージョン | Python 3.8 |
| サポートされている DSVM エディション | Windows Server 2019、Ubuntu 18.04 |
| DSVM での構成/インストール方法 | 複数の環境 `conda` があり、それぞれ Python パッケージが事前にインストールされています。 ご自身のマシン内で使用できるすべての環境を一覧表示するには、`conda env list` を実行します。 |

### <a name="how-to-use-and-run-it"></a>使用と実行方法

* コマンド プロンプトでの実行:

  コマンド プロンプトを開き、実行する Python のバージョンに応じて次のいずれかの方法を使用します。

    ```
    conda activate <conda_environment_name>
    python --version
    ```
    
* IDE で使用する:

  DSVM イメージには、VS.Code、PyCharm など、複数の IDE がインストールされています。 それらを使用して、ご自身の Python スクリプトを編集、実行、デバッグできます。

* Jupyter Lab で使用する:

  Jupyter Lab で Launcher タブを開き、新しいドキュメントの種類とカーネルを選択します。 ドキュメントを特定のフォルダーに配置する場合は、最初に左側のファイル ブラウザーでそのフォルダーに移動します。

* Python パッケージをインストールする:

  新しいパッケージをインストールするには、最初に適切な環境をアクティブにする必要があります。 新しいパッケージは、その環境内にインストールされ、その環境でのみ使用できます。

  環境をアクティブにするには、`conda activate <environment_name>` を実行します。 環境がアクティブになると、`conda` や `pip` などのパッケージ マネージャーを使用して、パッケージをインストールまたは更新できます。

  また、Jupyter を使用している場合は、セルで `!pip install --upgrade <package_name>` を実行して、パッケージを直接インストールすることもできます。

## <a name="r"></a>R

| カテゴリ | 値 |
|--|--|
| サポートされている言語バージョン | CRAN R 4.0.5 |
| サポートされている DSVM エディション | Linux、Windows |

### <a name="how-to-use-and-run-it"></a>使用と実行方法

* コマンド プロンプトでの実行:

  コマンド プロンプトを開き、「`R`」と入力します。

* IDE で使用する:

  IDE 内で R スクリプトを編集するには、RStudio を使用できます。これは、既定で DSVM イメージ上にインストールされています。

* Jupyter Lab で使用する

  Jupyter Lab で Launcher タブを開き、新しいドキュメントの種類とカーネルを選択します。 ドキュメントを特定のフォルダーに配置する場合は、最初に左側のファイル ブラウザーでそのフォルダーに移動します。

* R パッケージをインストールする:

  新しい R パッケージをインストールするには、`install.packages()` 関数を使用するか、RStudio を使用します。

## <a name="julia"></a>Julia

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされている言語バージョン | 1.0.5 |
| サポートされている DSVM エディション      | Linux、Windows     |


### <a name="how-to-use-and-run-it"></a>使用と実行方法    

* コマンド プロンプトで実行する

  コマンド プロンプトを開き、`julia` を実行します。

* Jupyter で使用する:

  Jupyter で Launcher タブを開き、新しいドキュメントの種類とカーネルを選択します。 ドキュメントを特定のフォルダーに配置する場合は、最初に左側のファイル ブラウザーでそのフォルダーに移動します。

* Julia パッケージをインストールする:

  その後、`Pkg.add()` などの Julia パッケージ マネージャー コマンドを使用して、パッケージをインストールまたは更新できます。


## <a name="other-languages"></a>その他の言語

**C#** : Windows で使用可能であり、Visual Studio Community エディションから、または `Developer Command Prompt for Visual Studio` でアクセスできます (`csc` コマンドを実行できる場合)。

**Java**: OpenJDK は DSVM の Linux エディションと Windows エディションの両方で使用可能であり、パスに設定されています。 Java を使用するには、Windows のコマンド プロンプトまたは Linux の bash シェルで `javac` または `java` コマンドを入力します。

**Node.js**:Node.js は DSVM の Linux エディションと Windows エディションの両方で使用可能であり、パスに設定されています。 Node.js にアクセスするには、Windows のコマンド プロンプトまたは Linux の bash シェルで `node` または `npm` コマンドを入力します。 Windows では、Node.js アプリケーション開発用のグラフィカル IDE を提供するために、Node.js ツール用の Visual Studio 拡張機能がインストールされます。

**F#** : Windows で使用可能であり、Visual Studio Community エディションから、または `Developer Command Prompt for Visual Studio` でアクセスできます (`fsc` コマンドを実行できる場合)。
