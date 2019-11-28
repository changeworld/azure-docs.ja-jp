---
title: チュートリアル - Azure で Yeoman を使用して Terraform ベース テンプレートを作成する
description: Azure で Yeoman を使用して Terraform ベース テンプレートを作成する方法について説明します。
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba81d0ee797fd879fdadc3a6b25ca8f310383f61
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159170"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>チュートリアル:Azure で Yeoman を使用して Terraform ベース テンプレートを作成する

このチュートリアルでは、[Terraform](/azure/terraform/) と [Yeoman](https://yeoman.io/) を組み合わせて使用する方法について説明します。 Terraform は、Azure でインフラストラクチャを作成するためのツールです。 Yeoman を使用すると、Terraform モジュールを簡単に作成できます。

この記事では、次のタスクを行う方法について説明します。
> [!div class="checklist"]
> * Yeoman モジュール ジェネレーターを使用して、ベースの Terraform テンプレートを作成します。
> * 2 つの異なる方法を使用して、Terraform テンプレートをテストします。
> * Docker ファイルを使用して、Terraform モジュールを実行します。
> * Azure Cloud Shell で Terraform モジュールをネイティブに実行します。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- **Visual Studio Code**:お使いのプラットフォーム向けの [Visual Studio Code をダウンロードします](https://code.visualstudio.com/download)。
- **Terraform**:Yeoman で作成したモジュールを実行するために、[Terraform をインストールします](/azure/virtual-machines/linux/terraform-install-configure )。
- **Docker**:Yeoman ジェネレーターによって作成されたモジュールを実行するために、[Docker をインストールします](https://www.docker.com/get-started)。
- **Go プログラミング言語**: Yeoman で生成されるテスト ケースは Go 言語を使用したコードであるため、[Go をインストールします](https://golang.org/)。

>[!NOTE]
>このチュートリアルの手順のほとんどには、コマンド ライン インターフェイスが含まれます。 説明する手順は、すべてのオペレーティング システムおよびコマンド ライン ツールに適用されます。 この例では、ローカル環境には PowerShell が選択され、Cloud Shell 環境には Git Bash が選択されています。

## <a name="prepare-your-environment"></a>環境を準備する

### <a name="install-nodejs"></a>Node.js のインストール

Cloud Shell で Terraform を使用するには、[Node.js をインストール](https://nodejs.org/en/download/)する必要があります (Node.js 6.0 以上)。

>[!NOTE]
>Node.js がインストールされているかどうかを確認するには、ターミナル ウィンドウを開いて、`node --version` と入力します。

### <a name="install-yeoman"></a>Yeoman のインストール

次のコマンドを実行します。

```bash
npm install -g yo
```

![Yeoman のインストール](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Terraform モジュール用に Yeoman をインストールする

次のコマンドを実行します。

```bash
npm install -g generator-az-terra-module
```

![generator-az-terra-module のインストール](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Yeoman がインストールされていることを確認するには、次のコマンドを実行します。

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Yeoman で生成されるモジュール用のディレクトリを作成する

Yeoman テンプレートは、現在のディレクトリ内にファイルを生成します。 このため、ディレクトリを作成する必要があります。

この空のディレクトリは $GOPATH/src 以下に配置する必要があります。 このパスの詳細については、「[GOPATHの設定](https://github.com/golang/go/wiki/SettingGOPATH)」の記事を参照してください。

1. 新しいディレクトリを作成する親ディレクトリに移動します。

1. プレースホルダーを置き換えて、次のコマンドを実行します。 この例では、`GeneratorDocSample` というディレクトリ名が使用されています。

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. 新しいディレクトリに移動します。

    ```bash
    cd <new-directory-name>
    ```

    ![新しいディレクトリに移動します](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>ベース モジュール テンプレートを作成します

1. 次のコマンドを実行します。

    ```bash
    yo az-terra-module
    ```

1. 画面上の説明に従って次の情報を指定します:

    - **Terraform モジュール プロジェクト名** - この例では、`doc-sample-module` という値が使用されています。

        ![プロジェクト名](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Docker イメージ ファイルを含みますか?** - 「`y`」と入力します。 `n` を選択した場合、生成されたモジュールのコードはネイティブ モードでのみ実行をサポートします。

        ![Docker イメージ ファイルを含みますか?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. ディレクトリの内容を一覧表示して、作成されている結果のファイルを表示します。

    ```bash
    ls
    ```

    ![作成したファイルをリストアップします](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>生成されたモジュールのコードをレビューします

1. Visual Studio Code を起動します

1. メニュー バーから、 **[ファイル] > [フォルダーを開く]** を選択し、作成したフォルダーを選択します。

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

次のファイルは、Yeoman モジュール ジェネレーターによって作成されました。 これらのファイルとその使用方法の詳細については、 [Terraform モジュールの Terratest](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster) に関する記事を参照してください。

- `main.tf` - `random-shuffle` というモジュールを定義します。 入力は `string_list` です。 出力は順列の数です。
- `variables.tf` - このモジュールによって使用される入力と出力の変数を定義します。
- `outputs.tf` - このモジュールの出力内容を定義します。 ここでは、組み込みの Terraform モジュールである `random_shuffle` によって返される値です。
- `Rakefile` - ビルド ステップを定義します。 その手順は次のとおりです。
    - `build` - main.tf ファイルの書式設定を検証します。
    - `unit` - 生成されたモジュールのスケルトンには、単体テストのコードは含まれません。 単体テストのシナリオを指定する場合は、ここでそのコードを追加します。
    - `e2e` - モジュールのエンド ツー エンド テストを実行します。
- `test`
    - テスト ケースは Go で述されています。
    - テストのすべてのコードは、エンド ツー エンド テストです。
    - エンド ツー エンド テストでは、`fixture` で定義されているすべての項目のプロビジョニングを試行します。 `template_output.go` ファイルの結果は、事前定義された想定値と比較されます。
    - `Gopkg.lock` と `Gopkg.toml`: 依存関係を定義します。 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Docker ファイルを使用して新しい Terraform モジュールをテストする

このセクションでは、Docker ファイルを使用した Terraform モジュールのテスト方法について説明します。

>[!NOTE]
>この例は、Azure ではなく、ローカルでモジュールを実行します。

### <a name="confirm-docker-is-installed-and-running"></a>Docker がインストールされ実行されていることを確認します

コマンド プロンプトに `docker version` と入力します。

![Docker のバージョン](media/terraform-vscode-module-generator/ymg-docker-version.png)

結果として生じする出力が Docker がインストールされていることを確認します。

Docker が実際に実行されていることを確認するには、`docker info`を入力します。

![Docker 情報](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Docker コンテナーを設定します

1. コマンド プロンプトに 入力します

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`

    メッセージ**正常にビルドされました**が表示されます。

    ![正常にビルドされたことを示すメッセージ](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. コマンド プロンプトで「`docker image ls`」と入力すると、作成されたモジュール `terra-mod-example` が一覧表示されます。

    ![新しいモジュールを含む一覧](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. 「 `docker run -it terra-mod-example /bin/sh` 」を入力します。 `docker run` コマンドを実行すると、Docker 環境に入ります。 その時点で、`ls` コマンドを使用してファイルを検出できます。

    ![Docker のファイル一覧](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>モジュールをビルドする

1. 次のコマンドを実行します。

    ```bash
    bundle install
    ```

1. 次のコマンドを実行します。

    ```bash
    rake build
    ```

    ![Rake ビルド](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>エンド ツー エンド テストを実行する

1. 次のコマンドを実行します。

    ```bash
    rake e2e
    ```

1. しばらくすると、**合格**メッセージが表示されます。

    ![合格](media/terraform-vscode-module-generator/ymg-pass.png)

1. 「`exit`」と入力してテストを完了し、Docker 環境を終了します。

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Cloud Shell から Yeoman ジェネレーターを使用してモジュールの作成とテストを行う

このセクションでは、Cloud Shell から Yeoman ジェネレーターを使用してモジュールの作成とテストを行います。 Docker ファイルに代わり Cloud Shell を使用することで、このプロセスが大幅に簡素化されます。 Cloud Shell を使用すると、次の製品がすべてプレインストールされます。

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Cloud Shell セッションを開始する

1. [Azure portal](https://portal.azure.com/)、[shell.azure.com](https://shell.azure.com)、[Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/) のいずれかを使用して Azure Cloud Shell セッションを開始します。

1. **[Welcome to Azure Cloud Shell]\(Azure Cloud Shell へようこそ\)** ページが開きます。 **[Bash (Linux)]** を選択してください。

    ![Azure Cloud Shell へようこそ](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Azure ストレージ アカウントをまだ設定していない場合は、次の画面が表示されます。 **[Create storage]\(ストレージの作成\)** を選択します。

    ![ストレージがマウントされていません](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell によって、前に選択したシェルが起動され、先ほど作成されたクラウド ドライブに関する情報が表示されます。

    ![クラウド ドライブが作成されました](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Terraform モジュールを保持するディレクトリの準備

1. この時点で、環境変数には既に、Cloud Shell によって自動的に GOPATH が構成されています。 このパスを確認するには、「`go env`」と入力します。

1. $GOPATH ディレクトリがまだない場合は、作成します。「 `mkdir ~/go` 」を入力します。

1. $GOPATH ディレクトリ内にディレクトリを作成します。 このディレクトリは、この例で作成されるさまざまなプロジェクトディレクトリを保持するために使用されます。 

    ```bash
    mkdir ~/go/src
    ```

1. プレースホルダーを置き換えて、Terraform モジュールを保持するディレクトリを作成します。 この例では、`my-module-name` というディレクトリ名が使用されています。

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. モジュール ディレクトリに移動します。 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Terraform モジュールを作成してテストする

1. 次のコマンドを実行し、指示に従います。 Docker ファイルを作成するかどうかを確認されたら、「`N`」と入力します。

    ```bash
    yo az-terra-module
    ```

1. 次のコマンドを実行して、依存関係をインストールします。

    ```bash
    bundle install
    ```

1. 次のコマンドを実行して、モジュールをビルドします。

    ```bash
    rake build
    ```

    ![Rake ビルド](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. 次のコマンドを実行して、テストを実行します。

    ```bash
    rake e2e
    ```

    ![テスト合格の結果](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Terraform Visual Studio Code 拡張機能のインストールと使用](/azure/terraform/terraform-vscode-extension)。