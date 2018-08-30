---
title: Azure Terraform VS Code 拡張機能 | Microsoft Docs
description: この記事では、Visual Studio Code で Terraform 拡張機能をインストールして使用する方法を説明します。
keywords: terraform, devops, 仮想マシン, azure
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190997"
---
# <a name="azure-terraform-vs-code-extension"></a>Azure Terraform VS Code 拡張機能

Microsoft Azure Terraform Visual Studio Code (VS Code) 拡張機能は、Azure で Terraform を作成、テスト、および使用しながら、開発者の生産性を高めるように設計されています。 この拡張機能により、VS Code での Terraform コマンドのサポート、グラフを使ったリソースの視覚化、CloudShell との統合が実現します。

## <a name="what-you-do"></a>作業内容

- オープン ソースの HashiCorp Terraform 実行可能ファイルをコンピューターにインストールします。
- ご自身の VS Code のローカル インストールで Azure 向け Terraform VS Code 拡張機能をインストールします。

## <a name="what-you-learn"></a>学習内容

このチュートリアルで学習する内容は次のとおりです。

- Terraform によって Azure サービスのプロビジョニングを自動化および簡略化する方法。
- Azure サービス用の Microsoft Terraform VS Code 拡張機能をインストールして使用する方法。
- VS Code を使用して、Terraform プランを記述、計画、および実行する方法。

## <a name="what-you-need"></a>必要なもの

- Windows 10、Linux、または macOS 10.10 以上を実行しているコンピューター。
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US)。
- 有効な Azure サブスクリプション [30 日間の無料試用版 Microsoft Azure アカウント](https://azure.microsoft.com/free/)をアクティブにします。
- ローカル コンピューターへの [Terraform](https://www.terraform.io/) オープン ソース ツールのインストール。
  
## <a name="prepare-your-dev-environment"></a>開発環境の準備

### <a name="install-git"></a>Git のインストール

この記事の演習を完了するには、[Git をインストール](https://git-scm.com/)する必要があります。

### <a name="install-hashicorp-terraform"></a>HashiCorp Terraform のインストール

HashiCorp の「[Install Terraform (Terraform のインストール)](https://www.terraform.io/intro/getting-started/install.html)」Web ページの手順に従います。このページでは、以下について説明しています。

- Terraform のダウンロード
- Terraform のインストール
- Terraform が正しくインストールされていることの確認

>[!Tip]
>ご自身の PATH システム変数の設定に関する手順に従ってください。

### <a name="install-nodejs"></a>Node.js のインストール

Cloud Shell で Terraform を使用するには、[Node.js をインストール](https://nodejs.org/)する必要があります (Node.js 6.0 以上)。

>[!NOTE]
>Node.js がインストールされているかどうかを確認するには、ターミナル ウィンドウを開いて、「`node -v`」と入力します

### <a name="install-graphviz"></a>GraphViz のインストール

Terraform 視覚化機能を使用するには、[GraphViz をインストール](http://graphviz.org/)する必要があります。

>[!NOTE]
>GraphViz がインストールされているかどうかを確認するには、ターミナル ウィンドウを開いて、「`dot -V`」と入力します

### <a name="install-the-azure-terraform-vs-code-extension"></a>Azure Terraform VS Code 拡張機能のインストール:

1. VS Code を起動します。
2. **拡張機能**アイコンをクリックします。

    ![拡張機能ボタン](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. **Marketplace の拡張機能の検索**テキスト ボックスを使用して、Azure Terraform 拡張機能を検索します。

    ![Marketplace での VS Code 拡張機能の検索](media/terraform-vscode-extension/tf-search-extensions.png)

4. **[インストール]** をクリックします。

    >[!NOTE]
    >Azure Terraform 拡張機能の **[インストール]** をクリックすると、VS Code によって Azure アカウントの拡張機能が自動的にインストールされます。 Azure アカウントは Azure Terraform 拡張機能の依存関係ファイルであり、Azure サブスクリプション認証および Azure 関連のコード拡張機能の実行に使用されます。

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Visual Studio Code に Terraform 拡張機能がインストールされていることの確認

1. 拡張機能アイコンをクリックします。
2. 検索テキスト ボックスに「`@installed`」と入力します。

    ![インストール済み拡張機能](media/terraform-vscode-extension/tf-installed-extensions.png)

Azure Terraform 拡張機能が、インストール済み拡張機能の一覧に表示されます。

![インストール済み Terraform 拡張機能](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

お使いの Cloud Shell 環境でサポートされているすべての Terraform コマンドを、VS Code 内から実行できるようになりました。

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>演習 1: 基本的な Terraform コマンドのチュートリアル

この演習では、新しい Azure リソース グループをプロビジョニングする基本的な Terraform 構成ファイルを作成して実行します。

### <a name="prepare-a-test-plan-file"></a>テスト計画ファイルの準備

1. VS Code で、メニュー バーから **[ファイル] > [新しいファイル]** を選択します。
2. [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) に移動し、**使用例**コード ブロックのコードをコピーします。
3. コピーしたコードを、VS Code で作成した新しいファイルに貼り付けます。

    ![使用例コードの貼り付け](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >リソース グループの **name** 値は変更できますが、この値はご自身の Azure サブスクリプションに対して一意である必要があります。

4. メニュー バーから **[ファイル] > [名前を付けて保存]** を選択します。
5. **[名前を付けて保存]** ダイアログで、任意の場所に移動し、**[新しいフォルダー]** をクリックします  (新しいフォルダーの名前を、*[新しいフォルダー]* 以外のわかりやすい名前に変更してください)。

    >[!NOTE]
    >この例では、フォルダーに TERRAFORM-TEST-PLAN という名前を付けます。

6. 新しいフォルダーが強調表示 (選択) されていることを確認し、**[開く]** をクリックします。
7. **[名前を付けて保存]** ダイアログで、ファイルの既定の名前を *main.tf* に変更します。

    ![main.tf として保存](media/terraform-vscode-extension/tf-save-as-main.png)

8. **[Save]** をクリックします。
- メニュー バーで、**[ファイル] > [フォルダーを開く]** を選択します。 作成した新しいフォルダーに移動し、選択します。

### <a name="run-terraform-init-command"></a>Terraform *init* コマンドの実行

1. Visual Studio Code を起動します。
2. VS Code のメニュー バーから、**[ファイル] > [フォルダーを開く]** を選択し、*main.tf* ファイルを見つけて選択します。

    ![main.tf ファイル](media/terraform-vscode-extension/tf-main-tf.png)

3. メニュー バーから、**[表示] > [コマンド パレット...] > [Azure Terraform: Init]** を選択します。
4. しばらくして、*[Do you want to open Cloud Shell?]\(Cloud Shell を開きますか\)* と尋ねるメッセージが表示されたら、 Click **OK**.

    ![Cloud Shell を開きますか、と尋ねるメッセージ](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. 新しいフォルダーから初めて Cloud Shell を起動すると、Web アプリケーションを設定するよう求められます。 **[開く]** をクリックします。

    ![初めての Cloud Shell の起動](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. [Welcome to Azure Cloud Shell]\(Azure Cloud Shell へようこそ\) ページが開きます。 Bash または PowerShell を選択します。

    ![Azure Cloud Shell へようこそ](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >この例では、Bash (Linux) が選択されています。

7. Azure ストレージ アカウントをまだ設定していない場合は、次の画面が表示されます。 **[ストレージの作成]** をクリックします。

    ![ストレージがマウントされていません](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell によって、前に選択したシェルが起動され、先ほど作成されたクラウド ドライブに関する情報が表示されます。

    ![クラウド ドライブが作成されました](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. ここで Cloud Shell を終了できます
10. メニュー バーから、**[表示]** > **[コマンド パレット]** > **[Azure Terraform: init]** を選択します。

    ![Terraform が正常に初期化されました](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>プランの視覚化

先ほどこのチュートリアルの中で GraphViz をインストールしました。 Terraform では、構成または実行プランを視覚化するために GraphViz を使用できます。 この機能は、Azure Terraform VS Code 拡張機能の *visualize* コマンドによって実装されます。

- **メニュー バー**から、**[表示] > [コマンド パレット] > [Azure Terraform: Visualize]** を選択します。

    ![プランの視覚化](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Terraform *plan* コマンドの実行

Terraform *plan* コマンドは、一連の変更に対する実行プランが意図したとおりに動作するかどうかの確認に使用されます。

>[!NOTE]
>Terraform *plan* によって、実際の Azure リソースに変更が行われることはありません。 ご自身のプランに含まれる変更を実際に行うには、Terraform *apply* コマンドを使用します。

- メニュー バーから、**[表示]** > **[コマンド パレット]** > **[Azure Terraform: plan]** を選択します。

    ![Terraform プラン](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Terraform *apply* コマンドの実行

Terraform *plan* の結果に満足したら、*apply* コマンドを実行できます。

1. メニュー バーから、**[表示]** > **[コマンド パレット]** > **[Azure Terraform: apply]** を選択します。

    ![terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

2. 「*yes*」と入力します。

    ![Terraform apply、yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>目的の Terraform プランが実行されていることの確認

目的の新しい Azure リソース グループが正常に作成されたかどうかを確認するには:

1. Azure Portal を開きます。
2. 左側のナビゲーション ウィンドウで、**[リソース グループ]** を選択します。

    ![新しいリソースの確認](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

新しいリソース グループが **[名前]** 列に表示されています。

>[!NOTE]
>この Azure portal ウィンドウは開いておいてください。次の手順で使用します。

### <a name="run-terraform-destroy-command"></a>Terraform *destroy* コマンドの実行

1. メニュー バーから、**[表示]** > **[コマンド パレット]** > **[Azure Terraform: destroy]** を選択します。

    ![Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. 「*yes*」と入力します。

    ![Terraform destroy、yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>目的のリソース グループが破棄されていることの確認

新しいリソース グループが Terraform によって正常に破棄されていることを確認するには:

1. Azure portal の *[リソース グループ]* ページで、**[更新]** をクリックします。
2. 目的のリソース グループは表示されません。

    ![リソース グループが破棄されていることの確認](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>演習 2: Terraform *compute* モジュール

この演習では、Terraform *compute* モジュールを VS Code 環境に読み込む方法について説明します。

### <a name="clone-the-terraform-azurerm-compute-module"></a>terraform-azurerm-compute モジュールの複製

1. [こちらのリンク](https://github.com/Azure/terraform-azurerm-compute)を使用して、GitHub 上の Terraform Azure Rm Compute モジュールにアクセスします。
2. **[複製またはダウンロード]** をクリックします。

    ![複製またはダウンロード](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >この例では、使用するフォルダーに *terraform azurerm-compute* という名前が付けられています。

### <a name="open-the-folder-in-vs-code"></a>VS Code でフォルダーを開く

1. Visual Studio Code を起動します。
2. **メニュー バー**から、**[ファイル] > [フォルダーを開く]** を選択し、前の手順で作成したフォルダーに移動して、選択します。

    ![terraform-azurerm-compute フォルダー](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Terraform を初期化する

VS Code 内からの Terraform コマンドの使用を開始するには、2 つの Azure プロバイダー用のプラグイン (random および azurerm) をダウンロードする必要があります。

1. VS Code IDE のターミナル ウィンドウで、「`terraform init`」と入力します

    ![terraform init コマンド](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. 「`az login`」と入力し、画面の指示に従います。

### <a name="module-test-lint"></a>モジュール テスト: *lint*

1. **メニュー バー**から、**[表示] > [コマンド パレット...] > [Azure Terraform: Execute Test]** を選択します。
2. テストの種類のオプション一覧から **[lint]** を選択します。

    ![テストの種類の選択](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. *[Do you want to open Cloud Shell?]\(Cloud Shell を開きますか\)* と尋ねるメッセージが表示されたら、**[OK]** をクリックして、画面の指示に従います。

    ![CloudShell を開くかどうかを尋ねるメッセージ](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>**lint** または **end to end** のテストを実行すると、Azure では、コンテナー サービスを使用して、実際のテストを実行するテスト マシンがプロビジョニングされます。 このため、通常、テスト結果が返されるまでに数分かかる場合があります。

しばらくすると、次の例のようなリストがターミナル ウィンドウに表示されます。

![lint テストの結果](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>モジュール テスト: *end-to-end*

1. **メニュー バー**から、**[表示] > [コマンド パレット] > [Azure Terraform: Execute Test]** を選択します。
2. テストの種類のオプション一覧から **[end to end]** を選択します。

    ![テストの種類の選択](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. *[Do you want to open Cloud Shell?]\(Cloud Shell を開きますか\)* と尋ねるメッセージが表示されたら、**[OK]** をクリックして、画面の指示に従います。

    ![CloudShell を開くかどうかを尋ねるメッセージ](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>**lint** または **end to end** のテストを実行すると、Azure では、コンテナー サービスを使用して、実際のテストを実行するテスト マシンがプロビジョニングされます。 このため、通常、テスト結果が返されるまでに数分かかる場合があります。

しばらくすると、次の例のようなリストがターミナル ウィンドウに表示されます。

![end-to-end テストの結果](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>次の手順

Terraform を使用して Visual Studio Code 内から Azure サービスのプロビジョニングを簡素化する方法をいくつか見てきました。 次は、これらのリソースのいくつか確認します。
- [Terraform モジュール レジストリ](https://registry.terraform.io/)により、Azure およびサポートされているその他のプロバイダーで使用できる Terraform モジュールの一覧が表示されます。

これらのモジュールごとに、次の情報が提供されます。

- モジュールの一般的な機能とその特性の説明
- 使用例
- テスト構成。ローカルの開発用コンピューターで各モジュールをビルド、実行、およびテストする方法が示されています
- Dockerfile。モジュール開発環境をローカルで構築および実行できるようにします。
