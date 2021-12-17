---
title: Azure Lab Services を使用して Linux 上の R と RStudio でラボを設定する
description: Linux で RStudio を使用して R を教えるようにラボを設定する方法の説明
author: emaher
ms.topic: how-to
ms.date: 08/25/2021
ms.author: enewman
ms.openlocfilehash: cc05b682a91deab2d67e9d5ddbe5d4dc85740f6a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176645"
---
# <a name="set-up-a-lab-to-teach-r-on-linux"></a>Linux で R を教えるためのラボを設定

[R](https://www.r-project.org/about.html) は、統計的なコンピューティングとグラフィックス用のオープン ソースの言語と環境です。  これは、遺伝学の統計分析を始め、自然言語処理、財務データの分析において使用されています。  R には、[対話型のコマンドライン](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line) エクスペリエンスが用意されています。  [RStudio](https://www.rstudio.com/products/rstudio/) は、R 言語で使用できる対話型開発環境 (IDE) です。  無料版には、コード編集ツール、統合されたデバッグ エクスペリエンス、パッケージ開発ツールが用意されています。

この記事では、RStudio と R の、統計的コンピューティングを使用する必要があるクラスの構成要素としての側面のみを取り上げます。  [ディープ ラーニング](class-type-deep-learning-natural-language-processing.md)や、[Python と Jupyter Notebooks](class-type-jupyter-notebook.md) を扱う種類のクラスでは、RStudio の設定方法が異なります。  各記事では、マーケットプレース イメージの [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) を使用する方法について説明しています。それには、プレインストールされた RStudio など、多くの[データ サイエンス関連ツール](../machine-learning/data-science-virtual-machine/tools-included.md)が含まれています。  

## <a name="lab-account-configuration"></a>ラボ アカウントの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントの設定方法](./tutorial-setup-lab-account.md)に関するチュートリアルを参照してください。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

次の表の説明に従って、ラボ アカウントの設定を有効にします。 Azure Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Azure Marketplace イメージを指定する](./specify-marketplace-images.md)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| -------------------- | ----- |
| マーケットプレースの画像 | Ubuntu Server 18.04 LTS |
| [ピア仮想ネットワークの有効化](how-to-connect-peer-virtual-network.md) | 次の場合に有効にします。<ul><li>クラスに共有の Microsoft R Server が必要。</li><li>クラスに、学生用 VM ではなく外部に格納するための大きなデータ ファイルが必要。</li></ul> |

> [!IMPORTANT]
> ピア仮想ネットワークを有効にすることを選ぶ場合は、ラボを作成する前にそれを行う必要があります。

## <a name="lab-configuration"></a>ラボの構成

新しいラボを作成し、必要な設定を適用する手順については、「[チュートリアル: クラスルーム ラボを設定する](tutorial-setup-classroom-lab.md)」を参照してください。  ラボを作成するときには、以下の設定を適用します。

| ラボの設定 | 値と説明 |
| ------------ | ------------------ |
| 仮想マシンのサイズ | Small GPU (Compute)|
| VM イメージ | Ubuntu Server 18.04 LTS |
| リモート デスクトップ接続を有効にする | RDP の使用を選択した場合には、この設定を有効にしてください。  [ラボ マシンに接続するための X2Go](how-to-use-remote-desktop-linux-student.md) を選択した場合、この設定は必要ありません。  最初に SSH を使用して Linux VM に接続し、RDP/X2Go と GUI パッケージをインストールする必要があります。  詳細は、「[Linux VM 用のグラフィカル リモート デスクトップを有効にする方法](how-to-enable-remote-desktop-linux.md)」を参照してください。 |

## <a name="external-resource-configuration"></a>外部リソース構成

クラスによっては、大きなデータ ファイルなどのファイルを外部に保存する必要があります。  オプションとセットアップ手順は、「[Azure Lab Services での外部ファイル ストレージの使用](how-to-attach-external-storage.md)」を参照してください。

学生に共有 Microsoft R Server を用意する場合は、ラボを作成する前にサーバーをセットアップしておく必要があります。  共有サーバーをセットアップする方法は、「[Azure Lab Services で共有リソースを使用してラボを作成する方法](how-to-create-a-lab-with-shared-resource.md)」を参照してください。  RStudio Server を作成する手順については、「[Debian および Ubuntu 用 RStudio Server のダウンロード](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/)」と「[オープンソース版 Rstudio Server へのアクセス](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started)」を参照してください。

## <a name="template-configuration"></a>テンプレートの構成

テンプレート マシンが作成されたら、マシンを起動し、そこに接続して [R のインストール](https://docs.rstudio.com/resources/install-r/)を行い、[RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) および、オプションで [X2Go Server](https://wiki.x2go.org/doku.php/doc:installation:x2goserver) をインストールします。  

まず、apt を更新し、コンピューター上の既存のパッケージをアップグレードしてみましょう。

```bash
sudo apt update 
sudo apt upgrade
```

### <a name="install-x2go-server"></a>X2Go サーバーのインストール

X2Goを使用する場合は、[サーバーのインストール](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop#install-x2go-server)を行います。  まず、サーバー コンポーネントをインストールするために、[ssh](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) で接続する必要があります。  この処理が完了したら、[X2Go クライアントを使用して接続](how-to-use-remote-desktop-linux-student.md)した後、セットアップの残りの部分を完了できます。

X2Go の既定のインストールには、RStudio との互換性がありません。  この問題を回避するには、x2goagent オプション ファイルを更新します。

1. `/etc/x2go/x2goagent.options` ファイルを編集します。  sudo でファイルを編集することを忘れないでください。
    1. 次の状態の行をコメント解除します: `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
    1. 次の状態の行をコメント化します: `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
2. 新しいオプションが使用されるように、X2Go サーバーを再起動します。

    ```bash
    sudo systemctl restart x2goserver
    ```

または、[X2Go の Glx Xlib の回避策](https://wiki.x2go.org/doku.php/wiki:development:glx-xlib-workaround)手順に従って、必要なライブラリを作成することもできます。

### <a name="install-r"></a>R をインストールする

R を VM にインストールするにはいくつかの方法があります。  R は、包括的な R アーカイブ ネットワーク (CRAN) リポジトリからインストールします。  ここでは、R の最新バージョンが提供されます。このリポジトリがマシンに追加されたら、R と他の多くの関連パッケージをインストールできます。

CRAN リポジトリを追加する必要があります。 コマンドは、[R 用 Ubuntu パッケージ概説に公開されている手順](https://cran.rstudio.com/bin/linux/ubuntu/)から変更されています。

```bash
#download helper packages
sudo apt install --no-install-recommends software-properties-common dirmngr
# download and add the signing key (by Michael Rutter) for these repos
sudo wget -q "https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc" -O /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
#add repository
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/"
```

それでは以下のコマンドを実行して、R をインストールします。

```bash
sudo apt install r-base
```

### <a name="install-rstudio"></a>RStudio をインストールする

R をローカルにインストールしたので、RStudio IDE をインストールできます。  RStudio Desktop の無料バージョンをインストールします。  使用可能なすべてのバージョンについては、「[RStudio のダウンロード](https://www.rstudio.com/products/rstudio/download/)」を参照してください。

1. RStudio 用の[コード署名キーをインポート](https://www.rstudio.com/code-signing/)します。

    ```bash
    sudo gpg --keyserver keyserver.ubuntu.com  --recv-keys 3F32EE77E331692F
    ```

2. Ubuntu 用の [R StudioのDebian Linux Package file (.deb)](https://www.rstudio.com/products/rstudio/download/#download) をダウンロードします。  ファイルは、`rstudio-{version}-amd64.deb` の形式になります。  次に例を示します。

    ```bash
    export rstudiover="1.4.1717"
    wget --quiet -O rstudio.deb https://download1.rstudio.org/desktop/bionic/amd64/rstudio-$rstudiover-amd64.deb
    ```

3. gdebi を使用して RStudio をインストールします。   apt にローカル ファイルをインストールしていることを示すために、必ずファイル パスを使用してください。

    ```bash
    sudo apt install gdebi-core 
    echo "y" | gdebi rstudio.deb –quiet
    ```

### <a name="cran-packages"></a>CRAN パッケージ

これで、必要な [CRAN パッケージ](https://cloud.r-project.org/web/packages/available_packages_by_name.html)をインストールできます。  まず、[現在のR 4.0 またはそれ以降の「c2d4u」リポジトリ](https://cran.rstudio.com/bin/linux/ubuntu/#get-5000-cran-packages)を追加します。

```bash
sudo add-apt-repository ppa:c2d4u.team/c2d4u4.0+
```

[便利な R パッケージのクイック リスト](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages)のアーティクルにあるように、`install.packages(“package name”)` コマンドを R のインタラクティブ セッションで使用します。  または、RStudio で [ツール]-> [パッケージのインストール] メニュー項目を使用します。

パッケージを見つける際に情報が必要な場合は、[タスク別のパッケージの一覧](https://cran.r-project.org/web/views/)や[アルファベット順のパッケージの一覧](https://cloud.r-project.org/web/packages/available_packages_by_name.html)を参照してください。

## <a name="cost"></a>コスト

このクラスのコスト見積もりの例について説明します。  クラスの学生数は 25 名とします。 各学生には、20 時間の授業時間が予定されています。  予定された授業時間の他に、宿題や課題を行うための時間として、追加の 10 時間が各学生に割り当てられます。  ここで選択した仮想マシンのサイズは、**Small GPU (Compute)** で、139 ラボ ユニットです。

25 人の学生 &times; (予定の 20 時間 + 割り当てられた 10 時間)  &times; 139 ラボ ユニット &times; 0.01 米ドル/時間 = 1042.5 米ドル

> [!IMPORTANT]
> このコスト見積もりは、例を示すためだけのものです。  現在の価格の詳細については、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」を参照してください。

## <a name="next-steps"></a>次のステップ

テンプレート イメージをラボに発行できるようになります。 手順の詳細については、「[テンプレート VM を発行する](how-to-create-manage-template.md#publish-the-template-vm)」セクションを参照してください。

ラボを設定するときは、次の記事を参照してください。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)