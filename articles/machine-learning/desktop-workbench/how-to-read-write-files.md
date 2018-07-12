---
title: 大きなデータ ファイルの読み取りと書き込み | Microsoft Docs
description: Azure Machine Learning の実験で大きなファイルの読み取りと書き込みを実行します。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: 5a772f8792c02139e45977e207b5be4bebc63a9c
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906328"
---
# <a name="persisting-changes-and-working-with-large-files"></a>変更の保持と大きなファイルの操作
Azure Machine Learning 実験サービスでは、さまざまな実行ターゲットを構成できます。 ターゲットは、ローカル (ローカル コンピューターやローカル コンピューター上の Docker コンテナーなど) の場合もあれば、 リモート (リモート コンピューター上の Docker コンテナーや HDInsight クラスターなど) の場合もあります。 詳細については、[Azure Machine Learning 実験実行サービスの概要](experimentation-service-configuration.md)に関する記事をご覧ください。 

ターゲットで実行するには、まずプロジェクト フォルダーをそのコンピューティング ターゲットにコピーする必要があります。 このため、ローカルの一時フォルダーを使用するローカル実行でもこれを行う必要があります。 

## <a name="execution-isolation-portability-and-reproducibility"></a>実行の分離性、移植性、再現性
この設計の目的は、実行の分離性、再現性、移植性を確保することです。 同一または別のコンピューティング ターゲットで、同じスクリプトを 2 回実行すると、同じ結果が得られます。 最初の実行中に行われた変更が、2 回目の実行での変更に影響を与えてはなりません。 この設計により、コンピューティング ターゲットを、完了後に実行されるジョブとのアフィニティがないステートレスな計算リソースとして扱うことができます。

## <a name="challenges"></a>課題
この設計には、移植性と再現性というメリットがある一方で、いくつかの固有の課題もあります。

### <a name="persisting-state-changes"></a>状態の変更の保持
スクリプトによって計算コンテキストの状態が変更された場合、それらの変更は次回の実行では保持されず、クライアント コンピューターに自動的に反映されるわけではありません。 

具体的には、スクリプトによってサブフォルダーの作成やファイルの書き込みが行われた場合、そのフォルダーやファイルは実行後のプロジェクト ディレクトリには存在しません。 ファイルは、コンピューティング ターゲット環境の一時フォルダーに保存されます。 それらをデバッグに使用することはできますが、常に存在するとは限らないことに注意してください。

### <a name="working-with-large-files-in-the-project-folder"></a>プロジェクト フォルダー内の大きなファイルの操作

プロジェクト フォルダーに大きなファイルが含まれている場合、実行の開始時にフォルダーがターゲット コンピューティング環境にコピーされるときに待ち時間が発生します。 実行がローカルで行われる場合でも、回避すべき不要なディスク トラフィックが発生します。 そのため、現在、プロジェクトの最大サイズは 25 MB に制限されています。

## <a name="option-1-use-the-outputs-folder"></a>方法 1: *outputs* フォルダーを使用する
この方法は、次のすべての条件に該当する場合に推奨されます。
* スクリプトでファイルが生成される。
* 実験を実行するたびにファイルが変更されると予想される。
* これらのファイルの履歴を保持 したい。 

一般的なユース ケースは次のとおりです。
* Training a model
* データセットを作成する
* モデル トレーニングの実行の一環として、グラフをイメージ ファイルとしてプロットする 

また、各実行の出力を比較し、前回の実行で生成された出力ファイル (モデルなど) を選択して、それを後続のタスク (スコア付けなど) で使用することもできます。

ルート ディレクトリを基準とした *outputs* という名前のフォルダーにファイルを書き込むことができます。 このフォルダーは、実験サービスによって特別に処理されます。 モデル ファイル、データ ファイル、プロットされたイメージ ファイルなど、実行中にスクリプトによってこのフォルダーに作成されたもの (総称して "_アーティファクト_" と呼びます) はすべて、実行が完了すると実験アカウントに関連付けられた Azure Blob Storage アカウントにコピーされます。 これらのファイルは実行履歴レコードに含まれます。

*outputs* フォルダーにモデルを保存するコードの簡単な例を次に示します。
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
アーティファクトをダウンロードするには、Azure Machine Learning Workbench で特定の実行の詳細ページの **[出力ファイル]** セクションを参照します。 実行を選択し、**[ダウンロード]** をクリックします。 また、コマンド ライン インターフェイス (CLI) ウィンドウで `az ml asset download` コマンドを入力することもできます。

詳細な例については、"_あやめの分類_" サンプル プロジェクトの `iris_sklearn.py` Python スクリプトを参照してください。

## <a name="option-2-use-the-shared-folder"></a>方法 2: 共有フォルダーを使用する
各実行のファイルの履歴を保持する必要がない場合は、複数の独立した実行でアクセスできる共有フォルダーの使用が、次のシナリオに最適な方法となります。 
- スクリプトが、トレーニング データまたはテスト データとして、ローカル ファイル (.csv ファイルなど) またはテキスト/イメージ ファイルのディレクトリからデータを読み込む必要がある。 
- スクリプトが生データを処理し、テキスト/イメージ ファイルから、後続のトレーニング実行で使用される中間結果 (特徴を生成したトレーニング データなど) を書き出す。 
- スクリプトがモデルを吐き出し、後続のスコア付けスクリプトがそのモデルを取得して評価に使用する必要がある。 

共有フォルダーは、選択したコンピューティング ターゲットにローカルに存在することに注意してください。 そのため、この方法が機能するのは、この共有フォルダーを参照するすべてのスクリプト実行が同じコンピューティング ターゲットで実行され、コンピューティング ターゲットが実行間でリサイクルされない場合に限られます。

共有フォルダー機能を利用することで、`AZUREML_NATIVE_SHARE_DIRECTORY` 環境変数によって識別される特殊なフォルダーに対する読み書きを実行できます。 

### <a name="example"></a>例
この共有フォルダーを使用してテキスト ファイルに対する読み書きを実行する Python コードの例を次に示します。
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "w") as f1:
    f1.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "r") as f2:
    text = f2.read()
```

詳細な例については、"_あやめの分類_" サンプル プロジェクトの *iris_sklearn_shared_folder.py* ファイルを参照してください。

この機能を使用するには、*aml_config* フォルダー内のターゲット実行コンテキストを表すいくつかの単純な構成を *.compute* ファイルに設定しておく必要があります。 このフォルダーの実際のパスは、選択したコンピューティング ターゲットと構成した値によって異なる場合があります。

### <a name="configure-local-compute-context"></a>ローカルの計算コンテキストの構成

ローカルの計算コンテキストでこの機能を有効にするには、"_ローカル_" 環境を表す *.compute* ファイル (通常は *local.compute* という名前) に次の行を追加するだけです。
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

path ~/.azureml/share は、既定のベース フォルダーのパスです。 これは、スクリプト実行でアクセスできる任意のローカル絶対パスに変更できます。 実験アカウント名、ワークスペース名、プロジェクト名がベース ディレクトリ名に自動的に追加され、共有ディレクトリの完全なパスになります。 たとえば、前述の既定値を使用した場合、ファイルの書き込み (および取得) は次のパスに対して実行されます。

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Docker 計算コンテキスト (ローカルまたはリモート) の構成
Docker 計算コンテキストでこの機能を有効にするには、ローカルまたはリモートの Docker *.compute* ファイルに次の 2 行を追加する必要があります。

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>`AZUREML_NATIVE_SHARE_DIRECTORY` 環境変数を使用して共有フォルダーにアクセスするときは、**sharedVolumes** プロパティを *true* に設定する必要があります。 そうしないと実行は失敗します。

Docker コンテナーで実行されるコードは、常にこの共有フォルダーを */azureml-share/* と見なします。 Docker コンテナーから見たこのフォルダー パスは構成できません。 コードでこのフォルダー名を使用しないでください。 代わりに、常に環境変数名 `AZUREML_NATIVE_SHARE_DIRECTORY` を使用してこのフォルダーを参照します。 これは、Docker ホスト コンピューターまたは計算コンテキストのローカル フォルダーにマップされます。 このローカル フォルダーのベース ディレクトリは、*.compute* ファイル内の `nativeSharedDirectory` 設定の構成可能な値です。 既定値を使用する場合、ホスト コンピューター上の共有フォルダーのローカル パスは次のようになります。
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>ローカル計算コンテキストとローカル Docker 計算コンテキストのどちらであるかに関係なく、ローカル ディスク上の共有フォルダーのパスは同じです。 これは、ローカル実行とローカル Docker 実行の間でファイルを共有できることを意味します。

これらのフォルダーに入力データを直接配置し、そのマシン上でのローカル実行または Docker 実行で取得させることができます。 また、ローカル実行または Docker 実行からこのフォルダーにファイルを書き込んで、ファイルがそのフォルダー内で実行ライフサイクルを超えて保持されるようにすることもできます。

詳細については、[Azure Machine Learning Workbench 実行構成ファイル](experimentation-service-configuration-reference.md)に関する記事をご覧ください。

>[!NOTE]
>`AZUREML_NATIVE_SHARE_DIRECTORY` 環境変数は、HDInsight 計算コンテキストではサポートされていません。 ただし、Azure Blob Storage の絶対パスを明示的に使用して、接続された Blob Storage に対する読み書きを実行することで、同じ結果を簡単に得ることができます。

## <a name="option-3-use-external-durable-storage"></a>方法 3: 外部の永続ストレージを使用する

外部の永続ストレージを使用して、実行時の状態を保持できます。 この方法は次のシナリオで役立ちます。
- ターゲット コンピューティング環境からアクセスできる永続ストレージに入力データが既に格納されている。
- これらのファイルを実行履歴レコードに含める必要はない。
- さまざまなコンピューティング環境での複数の実行でこれらのファイルを共有する必要がある。
- これらのファイルが計算コンテキスト自体で存続できる必要がある。

このような方法の 1 つとして、Python または PySpark コードから Azure Blob Storage を使用します。 簡単な例を次に示します。

```python
from azure.storage.blob import BlockBlobService
from azure.storage.blob.models import PublicAccess
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
blob_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

任意の Azure Blob Storage を HDI Spark ランタイムに接続する簡単な例を次に示します。
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>まとめ
Azure Machine Learning ではプロジェクト フォルダー全体をターゲット計算コンテキストにコピーしてスクリプトを実行するため、大きな入力ファイル、出力ファイル、中間ファイルには特に注意が必要です。 大きなファイルのトランザクションには、特殊な outputs フォルダー、`AZUREML_NATIVE_SHARE_DIRECTORY` 環境変数を使用してアクセスできる共有フォルダー、または外部の永続ストレージを使用できます。 

## <a name="next-steps"></a>次の手順
- [Azure Machine Learning Workbench 実行構成ファイル](experimentation-service-configuration-reference.md)に関する記事を確認する。
- [あやめの分類](tutorial-classifying-iris-part-1.md)チュートリアル プロジェクトで、outputs フォルダーを使用してトレーニング済みのモデルを保持する方法を確認する。
