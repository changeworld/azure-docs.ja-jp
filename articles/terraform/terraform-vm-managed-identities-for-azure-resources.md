---
title: チュートリアル - Terraform を使用して Azure Marketplace イメージからのマネージド ID で Linux VM を作成する
description: Azure Marketplace イメージを使用してマネージド ID とリモート状態管理によって Terraform Linux VM を作成する
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 233012d6caf1280914a6d2439ae856d69570fff7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838043"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>チュートリアル:Terraform を使用して Azure Marketplace イメージからのマネージド ID で Linux VM を作成する

この記事では、[Terraform Marketplace イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview)を使用して、[Terraform](https://www.terraform.io/intro/index.html) の最新バージョンがインストールされ、[Azure リソースのマネージド ID](/azure/active-directory/managed-service-identity/overview) を使用して構成された Ubuntu Linux VM (16.04 LTS) を作成する方法について説明します。 このイメージでは、Terraform を使用した[リモート状態](https://www.terraform.io/docs/state/remote.html)管理を可能にするリモート バックエンドも構成します。 

Terraform Marketplace イメージを使用すると、Azure で Terraform を簡単に使い始めることができます。Terraform を手動でインストールして構成する必要はありません。 

この Terraform VM イメージではソフトウェアの料金は発生しません。 プロビジョニングされた VM のサイズに基づいて、Azure ハードウェアの使用料金のみを支払います。 

コンピューティング料金の詳細については、[Linux VM の料金ページ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)をご覧ください。

## <a name="prerequisites"></a>前提条件
Terraform Linux VM を作成するには、Azure サブスクリプションが必要です。 Azure サブスクリプションがない場合は、「 [無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free/)」をご覧ください。  

## <a name="create-your-terraform-vm"></a>Terraform VM を作成する 

Terraform Linux VM のインスタンスを作成する手順を以下に示します。 

1. Azure Portal で、[[リソースの作成]](https://ms.portal.azure.com/#create/hub) 一覧に移動します。

1. **[Search the Marketplace]\(Marketplace の検索\)** 検索バーで、**Terraform** を探します。 

1. **[作成]** を選択します。 

1. 以降のセクションでは、Terraform Linux VM を作成するウィザードの各ステップでの入力について説明します。 次のセクションでは、これらの各手順を構成するために必要な入力を一覧表示します。

## <a name="details-on-the-create-terraform-tab"></a>[Create Terraform]\(Terraform の作成\) タブの詳細

**[Create Terraform]\(Terraform の作成\)** タブで、次の詳細項目を入力します。

1. **基本**
    
   * **Name**:Terraform VM の名前。
   * **User Name**:最初のアカウントのサインイン ID。
   * **パスワード**: 最初のアカウントのパスワード。 (パスワードの代わりに SSH 公開キーを使用できます)。
   * **サブスクリプション**:マシンが作成され課金対象となるサブスクリプション。 そのサブスクリプションに対するリソース作成権限が必要です。
   * **リソース グループ**: 新規または既存のリソース グループ。
   * **場所**: 最適なデータ センター。 通常は、データの大半が存在するデータ センターか、ネットワーク アクセスを最速にするために物理的に最も近いデータ センターを選びます。

2. **追加設定**

   * **[サイズ]** :VM のサイズ。 
   * **VM ディスクの種類**: SSD または HDD。

3. **[Summary Terraform]\(Terraform の概要\)**

   * 入力したすべての情報が正しいことを確認します。 

4. **[購入]**

   * プロビジョニング プロセスを開始するには、 **[購入]** を選択します。 取引条件へのリンクが用意されています。 サイズに関するステップで選択したサーバー サイズのコンピューティングを超える追加の課金が、VM で発生することはありません。

Terraform VM イメージでは、次の手順が実行されます。

* Ubuntu 16.04 LTS イメージに基づいて、システムによって割り当てられた ID を使用する VM を作成する。
* VM に Azure リソースのマネージド ID の拡張機能をインストールして、Azure リソースに対して OAuth トークンを発行できるようにする。
* RBAC のアクセス許可を管理対象 ID に割り当てて、リソース グループの所有者権限を付与する。
* Terraform テンプレート フォルダー (tfTemplate) を作成する。
* Azureバックエンドで Terraform のリモート状態を事前構成する。

## <a name="access-and-configure-a-linux-terraform-vm"></a>Terraform Linux VM にアクセスして構成する

VM を作成したら、次の手順を実行します。

1. SSH を使用して VM にサインインします。 前のセクションで作成したアカウントの資格情報を使用します。 Windows では、[Putty](https://www.putty.org/) などの SSH クライアント ツールをダウンロードできます。

1. VM 上の Azure リソースの管理対象 ID に、サブスクリプション全体に対する共同作成者のアクセス許可を付与します。 

    共同作成者のアクセス許可により、VM 上の Azure リソースのマネージド ID は、Terraform を使用して VM リソース グループの外部にリソースを作成できます。 この操作は、次のスクリプトを実行して行います。 
    
    ```bash
    . ~/tfEnv.sh
    ```

    このスクリプトでは、[Azure CLI の対話形式のログイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) メカニズムを使用して、Azure で認証します。 このプロセスでは、サブスクリプション全体に対する[マネージド ID 共同作成者のアクセス許可](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)が割り当てられます。 

1. VM は、Terraform のリモート状態バックエンドを備えています。 Terraform のデプロイでこれを有効にするには、`remoteState.tf` ファイルを Terraform スクリプトのルートにコピーする必要があります。

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    リモート状態管理の詳細については、[Terraform のリモート状態](https://www.terraform.io/docs/state/remote.html)に関するページをご覧ください。 ストレージ アクセス キーは、このファイルで公開されています。 Terraform 構成ファイルをソース管理にコミットする前にこれを除外してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure での Terraform の使用について詳細を参照](/azure/terraform)