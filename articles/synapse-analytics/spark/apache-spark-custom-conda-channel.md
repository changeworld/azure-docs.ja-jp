---
title: パッケージ管理のためのカスタム Conda チャネルを作成する
description: パッケージ管理のためのカスタム Conda チャネルを作成する方法について説明します
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 937bfbf1ab1f874c6582b005ee0f7376ecf46235
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751778"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>パッケージ管理のためのカスタム Conda チャネルを作成する 
Python パッケージのインストール時に、Conda パッケージ マネージャーでは、パッケージを探すためにチャネルが使用されます。 場合によっては、さまざまな理由でカスタム Conda チャネルを作成する必要があります。 たとえば、次のようなことが考えられます。

- ご利用のワークスペースはデータ流出から保護されていて、アウトバウンド接続はブロックされている。  
- パブリック リポジトリにアップロードしたくないパッケージがある。
- ワークスペース内で、ユーザー用に代替リポジトリを設定したい。

この記事では、ご利用の Azure Data Lake Storage アカウント内でカスタム Conda チャネルを作成するのに役立つステップバイステップ ガイドを示します。

## <a name="set-up-your-local-machine"></a>ご利用のローカル コンピューターを設定する

1. ローカル コンピューターに Conda をインストールします。同じランタイムで使用される Conda バージョンを特定するには、[Azure Synapse Spark ランタイム](./apache-spark-version-support.md)に関するページを参照してください。
   
2. カスタム チャネルを作成するには、conda-build をインストールします。
```
conda install conda-build
```
3. サービスを提供するプラットフォームにすべてのパッケージを整理します。 この例では、Anaconda アーカイブをローカル コンピューターにインストールします。

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3  
```

4. Azure Synapse ランタイムで使用できる作成済みのものと似た環境を作成するには、[こちらのテンプレート](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml)をダウンロードできます。 テンプレートと実際の Azure Synapse 環境の間には若干の違いがある場合があります。 ダウンロードした後は、次のコマンドを実行できます。
```
apt-get -yq install gcc g++
conda env update --prune -f base_environment.yml
```

## <a name="mount-the-storage-account-onto-your-machine"></a>ストレージ アカウントをコンピューターにマウントする
次に、Azure Data Lake Storage Gen2 アカウントをローカル コンピューターにマウントします。 このプロセスは WASB アカウントを使用して実行することもできます。ただし、紹介するのは、ADLSg2 アカウントの例です。 
 
ローカル コンピューターにストレージ アカウントをマウントする方法の詳細については、[こちらのページ](https://github.com/Azure/azure-storage-fuse#blobfuse )を参照してください。 

1. blobfuse は、Microsoft 製品用の Linux ソフトウェア リポジトリからインストールできます。

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<storage-account-name>
export AZURE_STORAGE_SAS_TOKEN="<SAS>" 
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net
```

2. 自分のマウントポイント (```mkdir /path/to/mount```) を作成し、blobfuse を使用して BLOB コンテナーをマウントします。 この例では、**mycontainer** 変数の値として **privatechannel** を使用してみましょう。
   
```
sudo mkdir /home/trusted-service-user/privatechannel 
sudo mkdir -p /mnt/blobfusetmp 
blobfuse /home/trusted-service-user/privatechannel --container-name=privatechannel --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo chown trusted-service-user /mnt/blobfusetmp 
```
## <a name="create-the-channel"></a>チャネルを作成する
次の一連の手順では、カスタム Conda チャネルを作成します。

1. ご利用のローカル コンピューター上で、自分のカスタム チャネル用のすべてのパッケージを整理するためのディレクトリを作成します。 https://repo.anaconda.com/pkgs/main/linux-64/ からのすべての ```tar.bz2``` パッケージをサブディレクトリに整理します。 依存する tar.bz2 パッケージもすべて必ず含めてください。
   
```

cd ~/privatechannel/ 
mkdir -P channel/linux64 

<Add all .tar.bz2 from https://repo.anaconda.com/pkgs/main/linux-64/> 
// Note: Add all dependent .tar.bz2 as well 

cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 
conda index channel/noarch 
conda index channel/linux-64 
conda index channel
```

2. これで、```privatechannel/channel``` ディレクトリが作成されたストレージ アカウントを確認できます。

>[!Note]
> Conda では、コンテナーに関連付けられている SAS トークンは使用されません。 そのため、コンテナー "privatechannel" をパブリック アクセスとしてマークする必要があります。


カスタム チャネルを作成するため詳細については、[Conda ユーザーガイド](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html)も参照してください。 

## <a name="storage-account-permissions"></a>ストレージ アカウントのアクセス許可
次に、ストレージ アカウントに対するアクセス許可を検証する必要があります。 これらのアクセス許可を設定するには、カスタム チャネルを作成するパスに移動します。 次に、読み取り、一覧表示、実行の各アクセス許可を持つ ```privatechannel``` 用の SAS トークンを作成します。 

チャネル名は、このプロセスで生成され、blob SAS URL になります。  

## <a name="create-a-sample-conda-environment-configuration-file"></a>サンプルの Conda 環境構成ファイルを作成する
最後に、サンプルの Conda ```environment.yml``` ファイルを作成してインストール プロセスを確認します。 データ流出防止が有効なワークスペース内の場合は、環境ファイルで ``nodefaults`` チャネルを指定する必要があります。

Conda 構成ファイルの例を次に示します。
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
サンプルの Conda ファイルを作成したら、Conda 仮想環境を作成できます。 次のコマンドを実行することにより、ローカル環境でこれを検証できます。

```
conda env create –file sample.yml  
source activate env 
conda list 
```
カスタム チャネルの確認が完了したので、[Python プール管理](./apache-spark-manage-python-packages.md)プロセスを使用することで、Apache Spark プール上でライブラリを更新できます。

## <a name="next-steps"></a>次のステップ
- 既定のライブラリを確認します: [Apache Spark バージョンのサポート](apache-spark-version-support.md)
- Python パッケージを管理する: [Python パッケージの管理](./apache-spark-manage-python-packages.md)

