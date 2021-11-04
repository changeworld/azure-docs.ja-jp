---
title: 事前構築済み Docker イメージの Python 拡張機能
titleSuffix: Azure Machine Learning
description: Python パッケージ拡張ソリューションを使用して、事前構築済み Docker イメージを拡張します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 037de82a9123014151cc26013550348459b57ca7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561501"
---
# <a name="python-package-extensibility-for-prebuilt-docker-images-preview"></a>事前構築済みの Docker イメージ用の Python パッケージの機能拡張 (プレビュー)

[モデル推論用に事前構築済みの Docker イメージ](concept-prebuilt-docker-images-inference.md)には、一般的な機械学習フレームワーク向けのパッケージが含まれています。 Python パッケージは、次の 2 とおりの方法で、"__Docker イメージを再構築することなく__" 追加できます。

* [動的インストール](#dynamic): Docker コンテナーのブート時に、[requirements](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) ファイルを使用して Python パッケージを自動的に復元する方法です。

    "__迅速なプロトタイプ作成__" を目指すのであれば、この方法を検討してください。 イメージが起動するときに、`requirements.txt` ファイルを使用してパッケージが復元されます。 この方法ではイメージの起動に伴う処理が増えるため、デプロイが要求を処理できるようになるまでの待ち時間が長くなります。

* [プレインストール Python パッケージ](#preinstalled): プレインストール Python パッケージを含むディレクトリを指定します。 デプロイ中、使用するエントリ スクリプト (`score.py`) のコンテナーにこのディレクトリがマウントされます。

    この方法は、"__運用環境のデプロイ__" に使用します。 パッケージを格納するディレクトリはイメージにマウントされるため、デプロイにパブリック インターネット アクセスがなくても使用できます。 たとえば、セキュリティで保護された Azure Virtual Network にデプロイするケースが考えられます。

> [!IMPORTANT]
> Azure Machine Learning での事前構築済み Docker イメージ用 Python パッケージ拡張性の使用は、現在プレビュー段階です。 プレビュー機能では、サポートやサービス レベル アグリーメントは保証されず、"現状有姿" で提供されます。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 ワークスペースの作成に関するチュートリアルについては、[Azure Machine Learning の利用開始](quickstart-create-resources.md)に関するページを参照してください。
* Azure Machine Learning [環境](how-to-use-environments.md)の使い方を熟知していること。
* Azure Machine Learning で[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を熟知していること。

<a id="dynamic"></a>

## <a name="dynamic-installation"></a>動的インストール

この方法では、イメージの起動時に、[requirements](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) ファイルを使用して Python パッケージを自動的に復元します。

事前構築済みの Docker コンテナー イメージを requirements.txt を使用して拡張するには、これらの手順に従います。

1. `score.py` スクリプトと共に `requirements.txt` ファイルを作成します。
2. 必要なパッケージを **すべて** `requirements.txt` ファイルに追加します。
3. `AZUREML_EXTRA_REQUIREMENTS_TXT` 環境変数を、ご使用の Azure Machine Learning [環境](how-to-use-environments.md)における `requirements.txt` ファイルの場所に設定します。

デプロイ後、スコア スクリプト用にパッケージが自動的に復元されます。

> [!TIP]
> プロトタイプの段階であっても、`requirements.txt` には、各パッケージのバージョンを固定することをお勧めします。
> たとえば、単に `scipy` や `scipy > 1.2.3` とするのではなく、`scipy == 1.2.3` を使用します。
> バージョンを厳密に固定しないと、`scipy` の新しいバージョンがリリースされた場合に、スコアリング スクリプトが支障をきたしたり、デプロイやスケーリング中にエラーが発生したりする可能性があります。

`AZUREML_EXTRA_REQUIRMENTS_TXT` 環境変数を設定する例を次に示します。

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies 

myenv = Environment(name="my_azureml_env")
myenv.docker.enabled = True
myenv.docker.base_image = <MCR-path>
myenv.python.user_managed_dependencies = True

myenv.environment_variables = {
    "AZUREML_EXTRA_REQUIREMENTS_TXT": "requirements.txt"
}
```

次の図は、動的インストール プロセスを視覚的に表したものです。

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/dynamic-install-python-extend.svg" alt-text="動的インストール プロセスの図":::

<a id="preinstalled"></a>

## <a name="pre-installed-python-packages"></a>プレインストール Python パッケージ

この方法では、指定したディレクトリがイメージにマウントされます。 このディレクトリにある Python パッケージは、エントリ スクリプト (`score.py`) から使用できるようになります。

事前構築済みの Docker コンテナー イメージをプレインストール Python パッケージを通じて拡張するには、これらの手順に従います。

> [!IMPORTANT]
> Python 3.7 と互換性のあるパッケージを使用する必要があります。 現在のイメージはすべて Python 3.7 に固定されています。

1. [virtualenv](https://virtualenv.pypa.io/) を使用して仮想環境を作成します。

1. 依存関係をインストールします。 たとえば、`requirements.txt` に依存関係のリストが指定されている場合、そのリストを `pip install -r requirements.txt` で使用してインストールできるほか、`pip install` で個々の依存関係をインストールすることができます。

1. `AZUREML_EXTRA_PYTHON_LIB_PATH` 環境変数を指定する際は、適切なサイト パッケージ ディレクトリを指定してください。サイト パッケージ ディレクトリは、実際の環境名と Python バージョンによって異なります。 次のコードは、Python 3.7 と `myenv` という名前の仮想環境のパスを設定しているところを示しています。


    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies 

    myenv = Environment(name='my_azureml_env')
    myenv.docker.enabled = True
    myenv.docker.base_image = <MCR-path>
    myenv.python.user_managed_dependencies = True

    myenv.environment_variables = {
        "AZUREML_EXTRA_PYTHON_LIB_PATH": "myenv/lib/python3.7/site-packages"
    }
    ```

次の図は、プレインストール パッケージのプロセスを視覚的に表したものです。

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/pre-install-python-extend.svg" alt-text="プレインストール パッケージを使用したプロセスの図":::

### <a name="common-problems"></a>一般的な問題

マウント ソリューションが正しく機能するのは、`myenv` サイト パッケージ ディレクトリにすべての依存関係が格納されている場合だけです。 別の場所にインストールされている依存関係をローカル環境で使用した場合、それらをイメージ内で利用することはできません。

この問題の原因となり得るいくつかの事柄を次に示します。

* `virtualenv` では、分離された環境が既定で作成されます。 仮想環境をアクティブにした後は、"__グローバルな依存関係を使用できません__"。
* `PYTHONPATH` 環境変数がグローバルな依存関係を指している場合、__仮想環境に支障が生じる可能性があります__。 環境をアクティブにした後は、`pip list` と `pip freeze` を実行して、不要な依存関係が環境に存在しないことを確認してください。
* "__Conda 環境と `virtualenv` 環境は、干渉する場合があります__"。 [Conda 環境](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)と `virtualenv` は同時に使用しないでください。

## <a name="limitations"></a>制限事項

### <a name="modelpackage"></a>Model.package()

* [Model.package()](/python/api/azureml-core/azureml.core.model(class)) メソッドを使用すると、Docker イメージまたは Dockerfile ビルド コンテキストの形式でモデル パッケージを作成できます。 事前構築済みの推論 Docker イメージで Model.package() を使用すると、非ルート ユーザーをルート ユーザーに変更する中間イメージ ビルドがトリガーされます。

* Microsoft の Python パッケージ拡張ソリューションを使用することをお勧めします。 他の依存関係 (`apt` パッケージなど) が必要な場合は、[推論イメージを拡張する Dockerfile](how-to-extend-prebuilt-docker-image-inference.md#buildmodel) を独自に作成してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

* requirements.txt を使用した拡張方法では、ファイル名を必ず `requirements.txt` にしなければならないのですか?

        
    ```python
    myenv.environment_variables = {
        "AZUREML_EXTRA_REQUIREMENTS_TXT": "name of your pip requirements file goes here"
    }
    ```

* `requirements.txt` を使用した方法と "*マウントを使用した方法*" の違いを簡単に教えてください。

    まずは、*requirements.txt* を使用した方法を試してください。
    何度か繰り返した後、適切なモデル デプロイに必要なパッケージ (とバージョン) についての確信が得られたら、"*マウントによるソリューション*" に切り替えます。
        
    詳しい比較を次に示します。

    | 比較項目 | requirements.txt (動的インストール) | パッケージ マウント |
    | ----- | ----- | ------ |
    | 解決策  | 指定されたパッケージをコンテナーの起動時にインストールする `requirements.txt` を作成します。 | すべての依存関係を含んだローカル Python 環境を作成します。 そのディレクトリを実行時にコンテナーにマウントします。 |
    | パッケージのインストール           | 追加インストールは不要です (pip が既にインストールされている場合)                                                                                                          | 仮想環境または conda 環境のインストール。                                                                                   |
    | 仮想環境のセットアップ              | 必要な仮想環境について、特別なセットアップはありません。ユーザーは、`requirements.txt` を作成するために必要に応じて pip freeze を使用し、現在のローカル ユーザー環境をプルできます。 | クリーンな仮想環境をセットアップする場合は、現在のユーザーのローカル環境に応じて追加の手順が必要になることがあります。                        |
    | [デバッグ](how-to-inference-server-http.md)                 | 依存関係が明確にリストされているので、サーバーのセットアップとデバッグは簡単です。 | 仮想環境がクリーンではない場合、サーバーのデバッグ時に問題が生じる可能性があります。 たとえば、環境やユーザー コードからエラーが発生するようだとクリアとは言えません。 |
    | スケールアウト中の一貫性 | 外部の PyPi パッケージに依存しており、またその依存関係をユーザーが固定しているため、一貫性がありません。 こうした外部ダウンロードは、不具合の原因になる場合があります。                                 | ユーザー環境にのみ依存しているため、一貫性の問題はありません。                                                                             |

* `requirements.txt` とマウントされた依存関係ディレクトリがコンテナーに見つかりません。なぜでしょうか?

    ローカルで環境変数が正しく設定されていることを確認してください。 次に、指定したパスのスペルが正しいこと、またそのパスが存在することを確認します。
    [inference config](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor) コンストラクターで、ソース ディレクトリが正しく設定されているかどうかを確認します。

* 事前構築済みの推論 Docker イメージに含まれている Python パッケージの依存関係はオーバーライドできますか?

    はい。 推論イメージに既にインストールされているバージョン以外の Python パッケージを使用したい場合、拡張ソリューションでは、ユーザーによって指定されたバージョンが優先されます。 2 つのバージョン間に競合がないことを確認してください。

## <a name="best-practices"></a>ベスト プラクティス

* [登録済みモデルの読み込み](how-to-deploy-advanced-entry-script.md#load-registered-models)に関するドキュメントを参照してください。モデル ディレクトリを登録する際は、そのディレクトリにスコアリング スクリプトやマウントされた依存関係ディレクトリ、`requirements.txt` を含めないでください。


* 登録済みのモデルまたはローカル モデルを読み込む方法について詳しくは、[デプロイする場所と方法](how-to-deploy-and-where.md?tabs=azcli#define-a-dummy-entry-script)に関するページを参照してください。

## <a name="bug-fixes"></a>バグの修正

### <a name="2021-07-26"></a>2021 年 07 月 26 日

* `AZUREML_EXTRA_REQUIREMENTS_TXT` と `AZUREML_EXTRA_PYTHON_LIB_PATH` は、スコア スクリプトのディレクトリに対して常に相対的になるようになりました。
たとえば、requirements.txt とスコア スクリプトの両方が **my_folder** 内にある場合は、`AZUREML_EXTRA_REQUIREMENTS_TXT` を requirements.txt に設定する必要があります。 `AZUREML_EXTRA_REQUIREMENTS_TXT` は **my_folder/requirements.txt** に設定されなくなります。

## <a name="next-steps"></a>次の手順

モデルのデプロイについて詳しくは、[モデルのデプロイ方法](how-to-deploy-and-where.md)に関するページを参照してください。

事前構築済み Docker イメージのデプロイをトラブルシューティングする方法については、[事前構築済み Docker イメージのデプロイをトラブルシューティングする方法](how-to-troubleshoot-prebuilt-docker-image-inference.md)に関するページを参照してください。
