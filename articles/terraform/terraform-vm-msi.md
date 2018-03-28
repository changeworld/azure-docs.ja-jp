---
title: Azure Marketplace イメージを使用して管理対象サービス ID を使用する Terraform Linux 仮想マシンを作成する
description: Azure にリソースを簡単にデプロイするために、Marketplace イメージを使用して、管理対象サービス ID とリモート状態管理を使用する Terraform Linux 仮想マシンを作成します。
keywords: terraform, devops, MSI, 仮想マシン, リモート状態, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Azure Marketplace イメージを使用して管理対象サービス ID を使用する Terraform Linux 仮想マシンを作成する

この記事では、[Terraform Marketplace イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)を使用して、[Terraform](https://www.terraform.io/intro/index.html) の最新バージョンがインストールされ、[管理対象サービス ID (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) を使用して構成された `Ubuntu Linux VM (16.04 LTS)` を作成する方法について説明します。 このイメージでは、Terraform を使用した[リモート状態](https://www.terraform.io/docs/state/remote.html)管理を可能にするリモート バックエンドも構成します。 Terraform Marketplace イメージを使用すると、Azure で Terraform を数分で簡単に使い始めることができます。Terraform をインストールして認証を手動で構成する必要はありません。 

この Terraform VM イメージではソフトウェアの料金は発生しません。 プロビジョニングされた仮想マシンのサイズに基づいて評価された、Azure ハードウェアの利用料金のみが課金されます。 コンピューティング料金の詳細については、「[Linux Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)」をご覧ください。

## <a name="prerequisites"></a>前提条件
Terraform Linux 仮想マシンを作成するには、Azure サブスクリプションが必要です。 Azure サブスクリプションがない場合は、「 [無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free/)」をご覧ください。  

## <a name="create-your-terraform-virtual-machine"></a>Terraform 仮想マシンの作成 

Terraform Linux 仮想マシンのインスタンスを作成する手順を以下に示します。 

1. Azure Portal の [[リソースの作成]](https://ms.portal.azure.com/#create/hub) に移動します。
2. `Search the Marketplace` 検索バーで `Terraform` を検索します。 `Terraform` テンプレートを選択します。 Terraform の詳細タブの右下にある **[作成]** をクリックします。
![代替テキスト](media\terraformmsi.png)
3. 以下のセクションでは、Terraform Linux 仮想マシンを作成するウィザードの各ステップ (**右側に列挙**) での入力について説明します。  以下は、これらの各ステップを構成するために必要な入力項目です。

## <a name="details-in-create-terraform-tab"></a>[Create Terraform]\(Terraform の作成\) タブの詳細

[Create Terraform]\(Terraform の作成\) タブで入力する必要がある詳細情報を以下に示します。

a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **基本**
    
* **名前**: Terraform 仮想マシンの名前。
* **ユーザー名**: 最初のアカウントのサインイン ID。
* **パスワード**: 最初のアカウントのパスワード (パスワードの代わりに SSH 公開キーを使用できます)。
* **サブスクリプション**: 複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。 そのサブスクリプションに対するリソース作成権限が必要です。
* **リソース グループ**: 新しいリソース グループを作成することも、既存のグループを使用することもできます。
* **場所**: 最適なデータ センターを選択します。 通常は、データの大半が存在するデータ センターか、ネットワーク アクセスを最速にするために物理的に最も近いデータ センターを選びます。

b. **[追加設定]**

* サイズ: 仮想マシンのサイズ。
* VM ディスクの種類: SSD または HDD を選択します。

c. **[Summary Terraform]\(Terraform の概要\)**

* 入力したすべての情報が正しいことを確認します。 

d. **[購入]**

* プロビジョニングを開始するには、[購入] をクリックします。 取引条件へのリンクが用意されています。 [サイズ] ステップで選択したサーバー サイズのコンピューティングを超える追加の課金が VM で発生することはありません。

Terraform VM イメージでは、次の手順が実行されます。

* Ubuntu 16.04 LTS イメージに基づいて、システムによって割り当てられた ID を使用する VM を作成する
* VM に MSI 拡張機能をインストールして、Azure リソースに対して OAuth トークンを発行できるようにする
* RBAC のアクセス許可を管理対象 ID に割り当てて、リソース グループの所有者権限を付与する
* Terraform テンプレート フォルダー (tfTemplate) を作成する
* Azureバックエンドで Terraform のリモート状態を事前構成する

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Terraform Linux 仮想マシンにアクセスして構成する方法

VM を作成したら、SSH を使用してサインインできます。 テキスト シェル インターフェイスで、手順 3. の [基本] セクションで作成したアカウントの資格情報を使用します。 Windows では、[Putty](http://www.putty.org/) などの SSH クライアント ツールをダウンロードできます。

`SSH` を使用して仮想マシンに接続したら、仮想マシンの管理対象サービス ID に、サブスクリプション全体に対する共同作成者のアクセス許可を付与する必要があります。 共同作成者のアクセス許可により、VM の MSI は、Terraform を使用して VM リソース グループの外部にリソースを作成できます。 このアクションは、スクリプトを 1 回実行することで簡単に実現できます。 これを実行するコマンドを次に示します。

`. ~/tfEnv.sh`

上記のスクリプトでは、Azure での認証に [AZ CLI v 2.0 の対話型ログイン](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) メカニズムを使用し、仮想マシンの管理対象サービス ID に、サブスクリプション全体に対する共同作成者のアクセス許可を割り当てます。 

 VM には、Terraform のリモート状態バックエンドが作成されています。Terraform デプロイメントでこれを有効にするには、remoteState.tf ファイルを tfTemplate ディレクトリから Terraform スクリプトのルートにコピーする必要があります。  

 `cp  ~/tfTemplate/remoteState.tf .`

 リモート状態管理の詳細については、[こちら](https://www.terraform.io/docs/state/remote.html)をご覧ください。 このファイルで公開されているストレージ アクセス キーを、ソース管理に慎重にチェックインする必要があります。  

## <a name="next-steps"></a>次の手順
この記事では、Azure で Terraform Linux 仮想マシンをセットアップする方法について説明しました。 Azure 上の Terraform の詳細については、次のリソースもご覧ください。 

 [Microsoft.com の Terraform ハブ](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure プロバイダーのドキュメント](http://aka.ms/terraform)  
 [Terraform Azure プロバイダーのソース](http://aka.ms/tfgit)  
 [Terraform Azure モジュール](http://aka.ms/tfmodules)
 

















