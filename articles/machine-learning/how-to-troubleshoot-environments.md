---
title: 環境イメージのトラブルシューティング
titleSuffix: Azure Machine Learning
description: 環境イメージのビルドとパッケージのインストールに関する問題のトラブルシューティングを行う方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733236"
---
# <a name="troubleshoot-environment-image-builds"></a>環境イメージのビルドのトラブルシューティング
Docker 環境イメージのビルドとパッケージのインストールに関する問題のトラブルシューティングを行う方法について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。
* ローカルでデバッグするには、ローカル システム上に機能する Docker のインストールが必要です。

## <a name="docker-image-build-failures"></a>Docker イメージのビルド エラー
 
イメージのビルド エラーの大部分については、イメージ ビルド ログで根本的な原因を見つけることができます。
イメージ ビルド ログは Azure Machine Learning ポータル (20\_image\_build\_log.txt) から、または ACR タスク実行ログから見つけることができます。
 
ほとんどの場合、エラーをローカルで再現する方が簡単です。 エラーの種類を確認し、次の `setuptools` のいずれかを試してください。

- conda 依存関係をローカルにインストールする `conda install suspicious-dependency==X.Y.Z`
- pip 依存関係をローカルにインストールする `pip install suspicious-dependency==X.Y.Z`
- 環境全体を具体化する `conda create -f conda-specification.yml`

> [!IMPORTANT]
> ローカル コンピューティングのプラットフォームとインタープリターをリモートのものと一致していることを確認します。 

### <a name="timeout"></a>タイムアウト 
 
タイムアウトの問題は、さまざまなネットワークの問題によって発生することがあります。
- インターネット帯域幅が少ない
- サーバーの問題
- 依存関係が大きく、指定された conda または pip のタイムアウト設定ではダウンロードできない
 
次のようなメッセージは、この問題を示しています。
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

考えられる解決策:
 
- 使用可能な場合は、ミラー、BLOB ストレージ、その他の Python フィードなど、依存関係の別のソースを試してみてください。
- conda または pip を更新します。 カスタム Docker ファイルを使用している場合は、タイムアウト設定を更新します。
- 一部の pip バージョンには既知の問題があります。 pip の特定のバージョンを環境の依存関係に追加することを検討してください。

### <a name="package-not-found"></a>パッケージが見つからない

これは、イメージのビルド エラーの最も一般的な例です。

- conda パッケージが見つからなかった
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- 指定した pip パッケージまたはバージョンが見つからなかった
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- 入れ子になった pip 依存関係が無効
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

指定したソースにパッケージが存在することを確認します。 pip の依存関係を確認するには、[pip search](https://pip.pypa.io/en/stable/reference/pip_search/) を使用します。

`pip search azureml-core`

conda の依存関係については、[conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html) を使用します。

`conda search conda-forge::numpy`

その他のオプションについては、以下を参照してください。
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>インストーラーに関する注意事項

指定したプラットフォームと Python インタープリターのバージョン用に必要とされるディストリビューションが存在することを確認します。

pip の依存関係については、`https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` に移動して、必要なバージョンが使用可能かどうかを確認します。 たとえば、https://pypi.org/project/azureml-core/1.11.0/#files のように指定します。

conda の依存関係については、チャネル リポジトリ上のパッケージをご確認ください。
Anaconda, Inc. によって管理されているチャネルについては、[こちら](https://repo.anaconda.com/pkgs/)をご確認ください。

### <a name="pip-package-update"></a>pip パッケージの更新

pip パッケージのインストール時または更新時に、新しい要件を満たすために、既にインストールされているパッケージをリゾルバーで更新することが必要になる場合があります。
pip バージョンや、依存関係がインストールされた方法に関連したさまざまな理由により、アンインストールが失敗することがあります。
最も一般的なシナリオは、conda によってインストールされた依存関係を pip でアンインストールできなかったというものです。
このシナリオの場合、`conda remove mypackage` を使用して依存関係をアンインストールすることを検討してください。

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>インストーラーの問題

特定のインストーラーのバージョンはパッケージ リゾルバーに問題があり、それがビルドの失敗につながるおそれがあります。

カスタムの基本イメージまたは Dockerfile を使用する場合は、conda バージョン 4.5.4 以降を使用することをお勧めします。

pip の依存関係をインストールするには pip パッケージが必要です。環境にバージョンが指定されていない場合は、最新バージョンが使用されます。
最新バージョンのツールが原因で発生するおそれがある一時的な問題や破壊的変更を回避するには、pip の既知のバージョンを使用することをお勧めします。

次のいずれかのメッセージが表示された場合は、環境内で pip バージョンを固定することを検討してください。

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

さらに、依存関係に解決できない競合がある場合は、pip のインストールが無限ループに陥るおそれがあります。 ローカルで作業している場合は、pip のバージョンを 20.3 未満にダウングレードします。 YAML ファイルから作成された conda 環境では、この問題が発生するのは、conda-forge が最高優先度のチャネルである場合のみです。 この問題を軽減するには、conda 依存関係として、conda 仕様ファイル内に pip < 20.3 (!=20.3 または =20.2.4 で他のバージョンに固定) を明示的に指定します。

## <a name="service-side-failures"></a>サービス側のエラー

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>MCR からイメージをプルできない、またはコンテナー レジストリのアドレスを解決できない
考えられる問題:
- コンテナー レジストリのパス名が正しく解決されていないおそれがあります。 イメージ名に二重スラッシュが使用されていることと、Linux と Windows のホストでのスラッシュの方向が正しいことをご確認ください。
- VNet の背後にある ACR で、[サポートされていないリージョン](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)内のプライベート エンドポイントを使用している場合は、ポータルからサービス エンドポイント (パブリック アクセス) を使用して VNet の背後にある ACR を構成してから、再試行してください。
- VNet の背後に ACR を配置した後、[ARM テンプレート](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry)が確実に実行されているようにします。 これにより、ワークスペースと ACR インスタンスが通信できるようになります。

### <a name="401-error-from-workspace-acr"></a>ワークスペース ACR からの 401 エラー
[ws.sync_keys()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--) を使用してストレージ キーを再同期します

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>環境の "Waiting for other Conda operations to finish…" (他の Conda 操作の完了を待機しています...) エラーがスローされ続ける
イメージのビルドが進行しているときは、conda は SDK クライアントによってロックされます。 プロセスがクラッシュした場合、またはユーザーによって誤って取り消された場合、conda はロックされた状態のままになります。 これを解決するには、ロック ファイルを手動で削除します。 

### <a name="custom-docker-image-not-in-registry"></a>カスタム Docker イメージがレジストリにない
[正しいタグ](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment)が使用されていて、`user_managed_dependencies = True` であることを確認します。 `Environment.python.user_managed_dependencies = True` を使用すると、Conda が無効になり、ユーザーがインストールしたパッケージが使用されます。

### <a name="common-vnet-issues"></a>VNet に関する一般的な問題

1. ストレージ アカウント、コンピューティング クラスター、および Azure Container Registry のすべてが、仮想ネットワークの同じサブネット内に存在することを確認します。
2. ACR が VNet の背後にある場合、それは、イメージをビルドするために直接使用することはできません。 イメージをビルドするには、コンピューティング クラスターを使用する必要があります。
3. ストレージを VNet の背後に配置する必要があるのは、次のような場合です。
    - 推論またはプライベート ホイールを使用している
    - サービス エラー 403 (承認されていません) が表示される
    - ACR または MCR からイメージの詳細を取得できない

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>ネットワークの保護されたストレージにアクセスしようとしたときにイメージのビルドが失敗する
- ACR タスクは VNet の背後では動作しません。 ユーザーは、VNet の背後にある ACR を使用している場合、コンピューティング クラスターを使用してイメージをビルドする必要があります。
- 依存関係をプルできるようにするには、ストレージを VNet の背後に配置する必要があります。 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>ストレージのネットワーク セキュリティが有効になっているときは実験を実行できない
既定の Docker イメージを使用し、ユーザーによって管理される依存関係を有効にする場合は、MicrosoftContainerRegistry および AzureFrontDoor.FirstParty [サービス タグ](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network)を使用して、MCR とその依存関係を許可リストに載せる必要があります。

 詳細については、[VNET の有効化](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry)に関するページを参照してください。

### <a name="creating-an-icm"></a>ICM の作成

メタストアの ICM の作成または割り当てを行う場合は、問題をより深く理解できるように、CSS サポート チケットを含めてください。

## <a name="next-steps"></a>次のステップ

- [機械学習モデルをトレーニングして花を分類する](how-to-train-scikit-learn.md)
- [カスタム Docker イメージを使用して機械学習モデルをトレーニングする](how-to-train-with-custom-image.md)