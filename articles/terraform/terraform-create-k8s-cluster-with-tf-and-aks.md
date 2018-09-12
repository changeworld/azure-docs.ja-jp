---
title: Azure Kubernetes Service (AKS) および Terraform を使用して Kubernetes クラスターを作成する
description: Azure Kubernetes Service と Terraform を使用して Kubernetes クラスターを作成する方法を示すチュートリアル
services: terraform
ms.service: terraform
keywords: terraform, devops, 仮想マシン, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/06/2018
ms.openlocfilehash: cd1219fda7821fdc99e334de58826317113415d4
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053643"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Azure Kubernetes Service および Terraform を使用して Kubernetes クラスターを作成する
[Azure Kubernetes Service (AKS)](/azure/aks/) を使用すると、ホストされている Kubernetes 環境を管理できます。これによって、コンテナー オーケストレーションの知識がなくてもコンテナー化されたアプリケーションを迅速かつ簡単にデプロイおよび管理できるようになります。 また、アプリケーションをオフラインにすることなく、要求に応じてリソースをプロビジョニング、アップグレード、スケーリングすることにより、実行中の操作およびメンテナンスの負担もなくなります。

このチュートリアルでは、[Terraform](http://terraform.io) と AKS を使用して [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) クラスターを作成する際に次のタスクを実行する方法を学びます。

> [!div class="checklist"]
> * HCL (HashiCorp 言語) を使用した Kubernetes クラスターの定義
> * AKS と Terraform を使用した Kubernetes クラスターの作成
> * kubectl ツールを使用した Kubernetes クラスターの可用性のテスト

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**: Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

- **Terraform の構成**: [Terraform および Azure へのアクセスの構成](/azure/virtual-machines/linux/terraform-install-configure)に関する記事の手順に従ってください。

- **Azure サービス プリンシパル**: 「[Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal)」の「**サービス プリンシパルを作成する**」セクションの指示に従ってください。 appId、displayName、password、および tenant の値を書き留めます。

## <a name="create-the-directory-structure"></a>ディレクトリ構造を作成する
最初の手順では、演習のために、Terraform 構成ファイルを保持するディレクトリを作成します。

1. [Azure ポータル](http://portal.azure.com)にアクセスします。

1. [Azure Cloud Shell](/azure/cloud-shell/overview) を開きます。 前に環境を選択しなかった場合、環境として **Bash** を選択します。

    ![Cloud Shell のプロンプト](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. ディレクトリを `clouddrive` ディレクトリに変更します。

    ```bash
    cd clouddrive
    ```

1. `terraform-aks-k8s` という名前のディレクトリを作成します。

    ```bash
    mkdir terraform-aks-k8s
    ```

1. 新しいディレクトリに移動します。

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Azure プロバイダーを宣言する
Azure プロバイダーを宣言する Terraform 構成ファイルを作成します。

1. Cloud Shell で、`main.tf` という名前のファイルを作成します。

    ```bash
    vi main.tf
    ```

1. I キーを選択し、挿入モードに入ります。

1. 以下のコードをエディターに貼り付けます。

    ```JSON
    provider "azurerm" {
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. **Esc** キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Kubernetes クラスターを定義する
Kubernetes クラスターのリソースを宣言する Terraform 構成ファイルを作成します。

1. Cloud Shell で、`k8s.tf` という名前のファイルを作成します。

    ```bash
    vi k8s.tf
    ```

1. I キーを選択し、挿入モードに入ります。

1. 以下のコードをエディターに貼り付けます。

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
            key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_DS2_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    上記のコードでは、クラスターの name、location、resource_group_name が設定されます。 さらに、クラスターにアクセスするために使用される完全修飾ドメイン名 (FQDN) のフォームの一部である dns_prefix 値も設定されます。

    **linux_profile** レコードを使用すると、SSH を使用してワーカー ノードにサインインできる設定を構成できます。

    AKS では、ワーカー ノードのみについて課金されます。 **agent_pool_profile** レコードは、これらのワーカー ノードの詳細を構成します。 **agent_pool_profile レコード**には、作成するワーカー ノードの数とワーカー ノードの種類が含まれます。 将来、クラスターをスケールアップまたはスケールダウンする必要がある場合は、このレコードの **count** 値を変更します。

1. **Esc** キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>変数を宣言する

1. Cloud Shell で、`variables.tf` という名前のファイルを作成します。

    ```bash
    vi variables.tf
    ```

1. I キーを選択し、挿入モードに入ります。

1. 以下のコードをエディターに貼り付けます。

    ```JSON
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }
    ```

1. **Esc** キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Terraform 出力ファイルを作成する
[Terraform 出力](https://www.terraform.io/docs/configuration/outputs.html)によって、Terraform がプランを適用するときユーザーに対して強調表示される値を定義できます。これは、`terraform output` コマンドを使用してクエリできます。 このセクションでは、[kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) を使用したクラスターへのアクセスを許可する出力ファイルを作成します。

1. Cloud Shell で、`output.tf` という名前のファイルを作成します。

    ```bash
    vi output.tf
    ```

1. I キーを選択し、挿入モードに入ります。

1. 以下のコードをエディターに貼り付けます。

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. **Esc** キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Terraform 状態を保存する Azure Storage をセットアップする
Terraform は `terraform.tfstate` ファイルを介して状態をローカルで追跡します。 このパターンは 1 名の環境に適しています。 ただし、より実用的な複数名の環境では、[Azure Storage](/azure/storage/) を利用してサーバー上で状態を追跡する必要があります。 このセクションでは、必要なストレージ アカウント情報 (アカウント名とアカウント キー) を取得して、Terraform 状態の情報が格納されるストレージ コンテナーを作成します。

1. Azure Portal の左側のメニューで **[すべてのサービス]** を選択します。

1. **[ストレージ アカウント]** を選択します。

1. **[ストレージ アカウント]** タブで、Terraform が状態を格納するストレージ アカウントの名前を選択します。 たとえば、最初に Cloud Shell を開いたときに作成したストレージ アカウントを使用できます。  通常、Cloud Shell によって作成されたストレージ アカウント名は、`cs` の後に数字と文字のランダムな文字列が続きます。 **選択したストレージ アカウントの名前を覚えておいてください。後で必要になります。**

1. [ストレージ アカウント] タブで **[アクセス キー]** を選択します。

    ![ストレージ アカウント メニュー](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. **key1** の **[キー]** 値をメモします。 (キーの右側にあるアイコンを選択すると、値がクリップボードにコピーされます。)

    ![ストレージ アカウントのアクセス キー](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Cloud Shell で、Azure ストレージ アカウントにコンテナーを作成します (&lt;YourAzureStorageAccountName> および &lt;YourAzureStorageAccountAccessKey> プレースホルダーをご使用の Azure ストレージ アカウントの適切な値で置き換えます)。

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes クラスターを作成する
このセクションでは、`terraform init` コマンドを使用して、前のセクションで作成した構成ファイルに定義されているリソースを作成する方法を学びます。

1. Cloud Shell で、Terraform を初期化します (&lt;YourAzureStorageAccountName> および &lt;YourAzureStorageAccountAccessKey> プレースホルダーをご使用の Azure ストレージ アカウントの適切な値で置き換えます)。

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` コマンドによって、バックエンドおよびプロバイダー プラグインの初期化が成功したことが表示されます。

    !["terraform init" の結果例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. `terraform plan` コマンドを実行して、インフラストラクチャ要素を定義する Terraform プランを作成します。 このコマンドでは、**var.client_id** と **var.client_secret** の 2 つの値が要求されます。 **var.client_id** 変数には、ご使用のサービス プリンシパルに関連付けられた **appId** 値を入力します。 **var.client_secret** 変数には、ご使用のサービス プリンシパルに関連付けられた **password** 値を入力します。

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` コマンドによって、`terraform apply` コマンドを実行したときに作成されるリソースが表示されます。

    !["terraform plan" の結果例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. `terraform apply` コマンドを実行して、プランを適用し、Kubernetes クラスターを作成します。 Kubernetes クラスターを作成するプロセスに数分間かかり、Cloud Shell セッションがタイムアウトになる場合があります。Cloud Shell セッションがタイムアウトした場合は、「[Cloud Shell タイムアウトから復旧する](#recover-from-a-dloud-shell-timeout)」セクションの手順に従ってチュートリアルを完了できます。

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` コマンドによって、構成ファイルに定義されたリソースの作成結果が表示されます。

    !["terraform apply" の結果例](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Azure Portal で、左側のメニューの **[すべてのサービス]** を選択すると、新しい Kubernetese クラスターに対して作成されたリソースが表示されます。

    ![Cloud Shell のプロンプト](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Cloud Shell タイムアウトから復旧する
Cloud Shell セッションがタイムアウトした場合は、次の手順に従って復旧できます。

1. Cloud Shell セッションを開始します。

1. Terraform 構成ファイルを含むディレクトリに移動します。

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. 次のコマンドを実行します。

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Kubernetes クラスターをテストする
Kubernetes ツールを使用して、新しく作成したクラスターを確認できます。

1. Terraform 状態から Kubernetes 構成を取得し、kubectl が読み取れるファイルに格納します。

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. kubectl が正しい構成を選択できるように環境変数を設定します。

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. クラスターの正常性を確認します。

    ```bash
    kubectl get nodes
    ```

    ワーカー ノードの詳細を確認してください。次の図のように、すべてのステータスが **Ready** になっている必要があります。

    ![kubectl ツールを使用すると Kubernetes クラスターの正常性を確認できる](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="next-steps"></a>次の手順
この記事では、Terraform と AKS を使用して Kubernetes クラスターを作成する方法を学習しました。 Azure 上の Terraform の詳細については、次のリソースもご覧ください。 

 [Microsoft.com の Terraform ハブ](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure プロバイダーのドキュメント](http://aka.ms/terraform)  
 [Terraform Azure プロバイダーのソース](http://aka.ms/tfgit)  
 [Terraform Azure モジュール](http://aka.ms/tfmodules)