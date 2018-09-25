---
title: Azure Machine Learning 実験サービスの構成 | Microsoft Docs
description: この記事では、Azure Machine Learning 実験サービスの概略と構成手順について説明します。
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 3c5084e548bbb72fa38aae8b60aa46fb4d462dca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990350"
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Azure Machine Learning 実験サービスの構成

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

## <a name="overview"></a>概要
Azure Machine Learning 実験サービスは、データ サイエンティストが Azure Machine Learning の実行機能と実行管理機能を使用して実験できるようにします。 これにより、すばやく反復する俊敏な実験のためのフレームワークが提供されます。 Azure Machine Learning Workbench では、ご使用のマシンでのローカルな実行から始めることができ、GPU を備えたリモートの Data Science VM や Spark が実行されている HDInsight クラスター などの他の環境に簡単にスケールアップおよびスケールアウトできます。

実験サービスは、再現可能な、整合性のある、独立した方法で実験できるように構築されています。 コンピューティング ターゲット、実行環境、実行構成を管理するのに役立ちます。 Azure Machine Learning Workbench の実行機能と実行管理機能を使用することで、さまざまな環境の間を簡単に移動できます。 

Python または PySpark スクリプトを Workbench プロジェクトでローカルに、またはクラウドで大規模に実行できます。 

次の環境でスクリプトを実行できます。 

* Workbench によってインストールされたローカル コンピューター上の Python (3.5.2) 環境
* ローカル コンピューター上の Docker コンテナー内の Conda Python 環境
* リモート Linux マシン上で所有および管理している Python 環境
* リモート Linux マシン上の Docker コンテナー内の Conda Python 環境。 例: [Azure 上の Ubuntu ベースの DSVM] (https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Azure 上の [HDInsight for Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/)

>[!IMPORTANT]
>Azure Machine Learning 実験サービスでは、現在、Python と Spark のランタイム バージョンとして、それぞれ Python 3.5.2 と Spark 2.1.11 をサポートしています。 


### <a name="key-concepts-in-experimentation-service"></a>実験サービスの重要な概念
Azure Machine Learning の実験における次の概念を理解しておく必要があります。 後続のセクションで、これらの概念の利用方法について詳しく説明します。 

#### <a name="compute-target"></a>コンピューティング ターゲット
"_コンピューティング ターゲット_" は、プログラムを実行する場所 (ユーザーのデスクトップ、VM 上のリモート Docker、クラスターなど) を指定します。 コンピューティング ターゲットは、ユーザーがアドレス指定してアクセスできる場所である必要があります。 Workbench で、コンピューティング ターゲットを作成し、Workbench アプリケーションと CLI を使用してそれらを管理できます。 

CLI で_az ml computetarget attach_ コマンドを実行すると、実行で使用可能なコンピューティング ターゲットを作成できます。

サポートされているコンピューティング ターゲットを次に示します。
* Workbench によってインストールされたコンピューター上のローカル Python (3.5.2) 環境。
* コンピューター上のローカル Docker
* リモート Linux-Ubuntu VM 上のユーザーが管理する Python 環境。 [Azure 上の Ubuntu ベースの DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) などです。
* Linux-Ubuntu VM 上のリモート Docker。 [Azure 上の Ubuntu ベースの DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) などです。
* Azure 上の [HDInsight for Spark クラスター](https://azure.microsoft.com/services/hdinsight/apache-spark/)

実験サービスでは、現在、Python と Spark のランタイム バージョンとして、それぞれ Python 3.5.2 と Spark 2.1.11 をサポートしています。 

>[!IMPORTANT]
> Docker が実行されている Windows VM は、リモートのコンピューティング ターゲットとしてサポート**されていません**。

#### <a name="execution-environment"></a>実行環境
"_実行環境_" は、Workbench でプログラムを実行するために必要なランタイム構成と依存関係を定義します。

Workbench の既定のランタイム上で実行する場合は、お好みのツールとパッケージ マネージャーを使用してローカルの実行環境を管理します。 

ローカル Docker とリモート Docker の実行と、HDInsight ベースの実行を管理するために、Conda が使用されます。 これらのコンピューティング ターゲットでは、実行環境の構成は、**Conda_dependencies.yml** ファイルと **Spark_dependencies.yml** ファイルによって管理されます。 これらのファイルは、プロジェクト内の **aml_config** フォルダーにあります。

**実行環境でサポートされているランタイムは、次のとおりです。**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>実行構成
コンピューティング ターゲットと実行環境だけでなく、Azure Machine Learning は、*実行構成*の定義と変更を行うためのフレームワークも提供します。 さまざまな実験を行うなかで、繰り返し行う実験の一部で異なる構成が必要になる場合があります。 異なるパラメーター範囲やデータ ソースを使用したり、Spark パラメーターを調整したりできます。 実験サービスは、実行構成を管理するためのフレームワークを提供します。

_az ml computetarget attach_ コマンドの実行により、ご使用のプロジェクト内の **aml_config** フォルダーに ".compute" と ".runconfig" という 2 つのファイルが作成されます。これらのファイルは、_<your_computetarget_name>.compute_ と _<your_computetarget_name>.runconfig_ という命名規則に従っています。 コンピューティング ターゲットの作成時には、利便性のために .runconfig ファイルが自動作成されます。 CLI で _az ml runconfigurations_ コマンドを使用して、他の実行構成を作成し、管理できます。 ファイル システム上で作成し、編集することもできます。

Workbench の実行構成には、環境変数を指定することもできます。 ご使用の .runconfig ファイルに次のセクションを追加すると、環境変数を指定してそれをコードで使用できます。 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

これらの環境変数には、コードでアクセスできます。 たとえば、この Phyton コード スニペットでは、"EXAMPLE_ENV_VAR1" という環境変数が出力されます
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**次の図は、最初の実験実行のおおまかなフローを示しています。**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>実験の実行シナリオ
このセクションでは、実行シナリオを掘り下げ、Azure Machine Learning で実験がどのように行われるかについて説明します。具体的には、ローカルでの実験、リモート VM での実験、HDInsight クラスターでの実験の実行について説明します。 このセクションでは、コンピューティング ターゲットの作成から実験の実行までを段階的に説明します。

>[!NOTE]
>この記事の残りの部分では、CLI (コマンド ライン インターフェイス) コマンドを使用して概念と機能を示します。 ここで説明する機能は、Workbench から使用することもできます。

## <a name="launching-the-cli"></a>CLI の起動
CLI を簡単に起動するには、Workbench でプロジェクトを開き、**[ファイル]、[コマンド プロンプトを開く]** の順に選択します。

![](media/experimentation-service-configuration/opening-cli.png)

このコマンドを実行すると、ターミナル ウィンドウが起動されます。ここで、現在のプロジェクト フォルダー内のスクリプトを実行するコマンドを入力できます。 このターミナル ウィンドウは、Workbench によってインストールされる Python 3.5.2 環境を使用して構成されます。

>[!NOTE]
> コマンド ウィンドウから _az ml_ コマンドを実行する場合、ユーザーが Azure に対して認証されている必要があります。 CLI では、デスクトップ アプリから独立した認証キャッシュを使用しているため、Workbench にログインできても、CLI 環境で認証されているとは限りません。 認証するには次の手順を使用します。 認証トークンは一定期間ローカルにキャッシュされるため、これらの手順を繰り返す必要があるのは、トークンの有効期限が切れた場合のみです。 トークンの有効期限が切れたか、認証エラーが発生した場合は、次のコマンドを実行します。

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>プロジェクト フォルダー内で _az ml_ コマンドを実行する場合、そのプロジェクトが_現在の_ Azure サブスクリプションの Azure Machine Learning 実験アカウントに属していることを確認してください。 属していないと、実行エラーが発生する場合があります。


## <a name="running-scripts-and-experiments"></a>スクリプトと実験の実行
Workbench では、_az ml experiment submit_ コマンドを使用して、さまざまなコンピューティング ターゲットに対して Python スクリプトと PySpark スクリプトを実行できます。 このコマンドでは、実行構成定義が必要となります。 

ユーザーがコンピューティング ターゲットを作成すると、Workbench によって対応する runconfig ファイルが作成されますが、_az ml runconfiguration  create_ コマンドを使用して追加の実行構成を作成できます。 実行構成ファイルを手動で編集することもできます。

実行構成は、Workbench の実験実行エクスペリエンスの一部として表示されます。 

>[!NOTE]
>実行構成ファイルについて詳しくは、[実験実行構成リファレンス](experimentation-service-configuration-reference.md)に関するセクションをご覧ください。

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Workbench によってインストールされたランタイムでのスクリプトのローカル実行
Workbench では、Workbench によってインストールされた Python 3.5.2 ランタイムに対してスクリプトを直接実行できます。 この既定のランタイムは、Workbench のセットアップ時にインストールされ、Azure Machine Learning のライブラリと依存関係が含まれています。 ローカル実行の実行結果とアーティファクトは、クラウド上の実行履歴サービスに引き続き保存されます。

Docker ベースの実行とは異なり、この構成は Conda によって管理_されません_。 ローカルの Workbench Python 環境のパッケージの依存関係は、手動でプロビジョニングする必要があります。

次のコマンドで、Workbench によってインストールされた Python 環境でスクリプトをローカルに実行できます。 

```
$az ml experiment submit -c local myscript.py
```

Workbench CLI ウィンドウで次のコマンドを入力することで、既定の Python 環境へのパスを確認できます。
```
$ conda env list
```

>[!NOTE]
>ローカルの Spark 環境に対して直接 PySpark をローカルに実行することは、現在サポート**されていません**。 Workbench では、PySpark スクリプトのローカル Docker での実行がサポートされています。 Azure Machine Learning ベースの Docker イメージには、Spark 2.1.11 がプレインストールされています。 

_**Python スクリプトのローカル実行の概要:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>ローカル Docker でのスクリプトの実行
実験サービスを使用して、プロジェクトをローカル コンピューター上の Docker コンテナーで実行することもできます。 Workbench には基本の Docker イメージが用意されています。このイメージには Azure Machine Learning ライブラリと Spark 2.1.11 ランタイムが付属しており、ローカルで Spark を実行に実行できるようになっています。 ローカル コンピューターで Docker が既に実行されている必要があります。

ローカル Docker 上で Python スクリプトや PySpark スクリプトを実行するには、CLI で次のコマンドを実行します。

```
$az ml experiment submit -c docker myscript.py
```
or
```
az ml experiment submit --run-configuration docker myscript.py
```

ローカル Docker 上の実行環境は、Azure Machine Learning ベースの Docker イメージを使用して準備されます。 Workbench では、初回実行時にこのイメージがダウンロードされ、conda_dependencies.yml ファイルに指定されたパッケージでそのイメージがオーバーレイされます。 この処理により、初回の実行は速度が遅くなりますが、後続の実行はキャッシュされたレイヤーが Workbench で再使用されるため、はるかに速くなります。 

>[!IMPORTANT]
>初回実行向けに Docker イメージを準備するには、最初に _az ml experiment prepare -c docker_ コマンドを実行する必要があります。 ご使用の docker.runconfig ファイルで、**PrepareEnvironment** パラメーターを true に設定することもできます。 この操作により、実行の一部として環境が自動的に準備されます。  

>[!NOTE]
>PySpark スクリプトを Spark 上で実行している場合は、conda_dependencies.yml に加えて、spark_dependencies.yml も使用されます。

Docker イメージ上でスクリプトを実行すると、次のような利点があります。

1. スクリプトを他の実行環境で正確に実行できます。 Docker コンテナー上での実行により、移植性に影響する恐れのあるローカル参照を発見したり、回避したりできます。 

2. Apache Spark などの複雑なインストールと構成のランタイムやフレームワークを、自分でインストールすることなくすばやくテストできます。


_**Python スクリプトのローカル Docker 実行の概要:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>リモート Docker でのスクリプトの実行
一部の例では、ユーザーのローカル コンピューター上で使用可能なリソースが、必要なモデルをトレーニングするのに十分でないことがあります。 このような場合に実験サービスを使用すると、リモート Docker の実行を利用して、より高性能な VM 上で Python スクリプトや PySpark スクリプトを簡単に実行できます。 

リモート VM は次の要件を満たしている必要があります。
* リモート VM で Linux-Ubuntu が実行されている必要があります。また、SSH を使用してリモート VM アクセスできる必要があります。 
* リモート VM で Docker が実行されている必要があります。

>[!IMPORTANT]
> Docker が実行されている Windows VM は、リモート コンピューティング ターゲットとしてサポート**されていません**。


次のコマンドを使用して、リモート Docker ベースの実行用のコンピューティング ターゲット定義と実行構成の両方を作成できます。

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

コンピューティング ターゲットを構成したら、次のコマンドを使用してスクリプトを実行できます。
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>実行環境は conda_dependencies.yml 内の仕様を使用して構成されていることを覚えておいてください。 .runconfig ファイル内で PySpark フレームワークが指定されている場合には、spark_dependencies.yml も使用されます。 

リモート VM の Docker 構築プロセスはローカル Docker 実行のプロセスと全く同じであるため、同様の実行エクスペリエンスとなることが予想されます。

>[!TIP]
>初回実行向けに Docker イメージを構築して待ち時間が発生しないようにするには、次のコマンドを使用して、スクリプトを実行する前にコンピューティング ターゲットを準備します。 az ml experiment prepare -c remotedocker

_**Python スクリプトのリモート VM 実行の概要:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>リモート VM 上でユーザー管理環境をターゲットとするスクリプトを実行する
実験サービスは、リモート Ubuntu 仮想マシン内にあるユーザーの Python 環境でのスクリプトの実行もサポートしています。 これにより、実行のために自らの環境を管理しながら、Azure Machine Learning 機能を引き続き使用できます。 

自らの環境でスクリプトを実行するには次の手順に従います。
* リモート Ubuntu VM または DSVM に依存関係をインストールして、自らの Python 環境を準備します。
* 次のコマンドを使用して Azure Machine Learning の要件をインストールします。

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>場合によっては、このコマンドを sudo モードで実行する必要があります。これは権限によって異なります。 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* 次のコマンドを使用して、リモート VM 実行上のユーザー管理実行のために、コンピューティング ターゲット定義と実行構成の両方を作成できます。
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>.compute 構成ファイルの "userManagedEnvironment" パラメーターが true に設定されます。

* Python ランタイム実行可能ファイルの場所を .compute ファイルに設定します。 python 実行可能ファイルの完全なパスを指定する必要があります。 
```
pythonLocation: python3
```

コンピューティング ターゲットを構成したら、次のコマンドを使用してスクリプトを実行できます。
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> DSVM で実行する場合は、次のコマンドを使用する必要があります。

DSVM のグローバル python 環境で直接実行する場合には、次のコマンドを実行します。
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>HDInsight クラスター上でのスクリプトの実行
HDInsight は、Apache Spark をサポートする、ビッグ データ分析のための一般的なプラットフォームです。 Workbench では、HDInsight Spark クラスターを使用してビッグ データに関する実験を行うことができます。 

>[!NOTE]
>HDInsight クラスターは、プライマリ ストレージとして Azure BLOB を使用する必要があります。 Azure Data Lake Storage はまだサポートされていません。

コンピューティング ターゲットを作成して HDInsight Spark クラスター向けに構成するには、次のコマンドを使用します。

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>IP アドレスではなく FQDN を使用する場合、HDI Spark クラスターの名前は _foo_ となり、SSH エンドポイントの場所は _foo-ssh.azurehdinsight.net_ というドライバー ノード上になります。 _--address_ パラメーターに FQDN を使用する場合、サーバー名に **-ssh** という接尾辞を付けてください。


コンピューティング コンテキストを用意したら、次のコマンドを実行して PySpark スクリプトを実行できます。

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench では、Conda を使用して HDInsight クラスター上の実行環境を準備して管理します。 構成は、_conda_dependencies.yml_ および _spark_dependencies.yml_ の構成ファイルで管理されます。 

このモードで実験を行うには、HDInsight クラスターへの SSH アクセス権を保有している必要があります。 

>[!NOTE]
>サポートされている構成は、Linux (Python/PySpark 3.5.2 と Spark 2.1.11 を備えた Ubuntu) が実行されている HDInsight Spark クラスターです。

_**PySpark スクリプトの HDInsight ベースの実行の概要**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>GPU 上でのスクリプトの実行
GPU 上でスクリプトを実行するには、「[Azure Machine Learning で GPU を使用する方法](how-to-use-gpu.md)」で説明されているガイダンスに従います。

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>SSH キー ベースの認証を使用してコンピューティング ターゲットを作成および使用する
Azure Machine Learning Workbench では、ユーザー名/パスワード ベースのスキームに加えて SSH キー ベースの認証を使用して、コンピューティング ターゲットを作成および使用できます。 remotedocker またはクラスターをコンピューティング ターゲットとして使用する場合は、この機能を使用することができます。 このスキームを使用すると、Workbench は公開キーと秘密キーのペアを作成し、公開キーをレポートします。 ユーザー名の ~/.ssh/authorized_keys ファイルに公開キーを追加します。 Azure Machine Learning Workbench は、このコンピューティング ターゲットでのアクセスと実行に ssh キー ベースの認証を使用します。 コンピューティング ターゲット用の秘密キーはワークスペースのキー ストアに保存されるため、ワークスペースの他のユーザーは、コンピューティング ターゲットを作成するために指定したユーザー名を指定して、コンピューティング ターゲットを同じ方法で使用できます。  

この機能を使用するには次の手順に従います。 

- 以下のコマンドのいずれかを使用してコンピューティング ターゲットを作成します。

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
or
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Workbench によって生成された公開キーを、接続されているコンピューティング ターゲット上の ~/.ssh/authorized_keys ファイルに追加します。 

>[!IMPORTANT]
>コンピューティング ターゲットの作成に使用したのと同じユーザー名を使用してコンピューティング ターゲットにログオンする必要があります。 

- これで、SSH キー ベースの認証を使用してコンピューティング ターゲットを準備および使用できるようになりました。

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>次の手順
* [Azure Machine Learning を作成およびインストールする](quickstart-installation.md)
* [モデル管理](model-management-overview.md)
