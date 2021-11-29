---
title: 自動化フレームワークの手動デプロイの概要
description: サンプルの構成とサンプル パラメーター ファイルを使用して、SAP デプロイ自動化フレームワークを Azure に手動でデプロイします。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: cd8250d690a07fe8999274779ce00d3ac8fa04dc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725175"
---
# <a name="get-started-with-manual-deployment"></a>手動デプロイの概要

[自動デプロイ](automation-get-started.md)に加えて、[Azure での SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)を手動でデプロイすることもできます。 このサンプルの構成とサンプル パラメーター ファイルを使用して開始します。

> [!TIP]
> このガイドでは、**手動** によるデプロイを実行する方法についてのみ説明します。 すぐに開始する場合は、代わりに [**自動** デプロイ ガイド](automation-get-started.md)に関する記事を参照してください。

これらの手順では、自動化フレームワークの[既定の名前付け規則](automation-naming.md)を参照して使用します。 コード全体での名前付けにも、値の例が使用されます。 たとえば、配置機能名は `DEMO-EUS2-DEP00-INFRASTRUCTURE` です。 この例では、環境はデモ (`DEMO`)、リージョンは **米国東部 2** (`EUS2`)、配置機能仮想ネットワークは `DEP00` です。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。
- サービス プリンシパルを作成する権限を持つ Azure アカウント。 
- Azure 環境に [SAP ソフトウェアがダウンロードされている](automation-software.md)。

## <a name="deployer-setup"></a>配置機能のセットアップ

開始する前に、[正しい Azure サブスクリプションを使用していることを確認してください](#check-azure-subscription)。 次に、配置機能をセットアップします。

1. [Terraform をダウンロードしてインストールします](#download-terraform)。 
1. 配置機能で[自動化フレームワーク リポジトリを複製して構成します](#set-up-repository)。 
1. [Terraform を初期化します](#initialize-terraform)。
1. デプロイの残りの部分で使用する [SSH キーを取得します](#get-ssh-keys)。
### <a name="check-azure-subscription"></a>Azure サブスクリプションを確認する

適切な Azure サブスクリプションを使用していることを確認します。

1. [Azure portal](https://portal.azure.com/)にサインインします。

1. [Azure Cloud Shell を開きます](https://shell.azure.com/)。

1. 使用するサブスクリプションがアクティブであることを確認します。

    ```azurecli-interactive
    az account list --output=table | grep -i true
    ```

1. 必要に応じて、[アクティブなサブスクリプションを使用するサブスクリプションに変更します](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)。 

### <a name="download-terraform"></a>Terraform をダウンロードする

Terraform を環境にダウンロードします。

1. 新しいディレクトリ `bin` を作成してそこに移動します。

    ```azurecli-interactive
    mkdir -p ~/bin; cd $_
    ```

1. 適切な Terraform バイナリを取得します。 次に例を示します。

    ```azurecli-interactive
    wget  https://releases.hashicorp.com/terraform/0.14.7/terraform_0.14.7_linux_amd64.zip
    ```

1. Terraform バイナリを解凍します。 次に例を示します。

    ```azurecli-interactive
    unzip terraform_0.14.7_linux_amd64.zip
    ```

1. Terraform のダウンロードを確認します。

    ```azurecli-interactive
    hash terraform
    ```

1. SAP 自動デプロイ用のディレクトリを作成します。

    ```azurecli-interactive
    mkdir -p ~/Azure_SAP_Automated_Deployment; cd $_
    ```

## <a name="set-up-repository"></a>リポジトリをセットアップする

[自動化フレームワーク リポジトリ](https://github.com/Azure/sap-hana)を複製して構成します。

1. GitHub からリポジトリを複製します。

    ```azurecli-interactive
    git clone https://github.com/Azure/sap-hana.git
    ```

1. `sap-hana` フォルダーに移動します

    ```azurecli-interactive
    cd  ~/Azure_SAP_Automated_Deployment/sap-hana
    ```

1. 必要に応じて、main ブランチとは別のブランチをチェックアウトします。 既定では、リポジトリの main ブランチが使用されます。
    
    1. `<branch>` を使用するブランチ名またはコミット ハッシュに置き換えます。

        ```azurecli
        git checkout <branch>
        ```

    1. ブランチが想定されているリビジョンであることを確認します。

        ```azurecli-interactive
        git rev-parse HEAD
        ```

### <a name="initialize-terraform"></a>Terraform を初期化する

1. 作業ディレクトリを作成します。 ディレクトリ名は、[既定の名前付け規則](automation-naming.md)に従っている必要があります。 次に例を示します。

    ```azurecli-interactive
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/DEPLOYER/DEMO-EUS2-DEP00-INFRASTRUCTURE; cd $_
    ```

1. JSON パラメーター ファイルを作成します。

    ```azurecli-interactive
    cat <<EOF > DEMO-EUS2-DEP00-INFRASTRUCTURE.json
    {
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnets": {
          "management": {
            "name"                            : "DEP00",
            "address_space"                   : "10.0.0.0/25",
            "subnet_mgmt": {
              "prefix"                        : "10.0.0.64/28"
            },
            "subnet_fw": {
              "prefix"                        : "10.0.0.0/26"
            }
          }
        }
      },
      "options": {
        "enable_deployer_public_ip"           : true
      },
      "firewall_deployment"                   : true
    }
    EOF
    ```

1. Terraform を初期化します。

    ```azurecli-interactive
    terraform init  ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. 既定の名前付け規則に従って、Terraform 実行プランを作成します。

    ```azurecli-interactive
    terraform plan                                                                    \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                    \
                    ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. Terraform 実行プランを適用して、リソースをデプロイします。

    ```azurecli-interactive
    terraform apply --auto-approve                                                    \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                    \
                    ../../../sap-hana/deploy/terraform/bootstrap/sap_deployer/
    ```

1. 出力をメモします。

### <a name="get-ssh-keys"></a>SSH キーを取得する

1. [Terraform のデプロイ](#initialize-terraform)からの出力を使用して、次のフィールドの値を確認します。

    1. パブリック IP アドレス: `deployer_public_ip_address`。

    1. キー コンテナーのユーザー名: `deployer_kv_user_name`。

    1. 秘密キー コンテナーの名前: `deployer_kv_prvt_name`。

    1. 公開キーの名前: `deployer_public_key_secret_name`。

    1. 秘密キーの名前: `deployer_private_key_secret_name`。

1. 後処理スクリプトを実行します。

    ```azurecli-interactive
    ./post_deployment.sh
    ```

1. 秘密 SSH キーを抽出します。
    
    ```azurecli-interactive
    az keyvault secret show               \
      --vault-name DEMOEUS2DEP00userE27   \
      --name DEMO-EUS2-DEP00-sshkey     | \
      jq -r .value > sshkey
    
    ```

1. 公開 SSH キーを抽出します。
    
    ```azurecli-interactive
    az keyvault secret show               \
      --vault-name DEMOEUS2DEP00userF6A   \
      --name DEMO-EUS2-DEP00-sshkey-pub | \
      jq -r .value > sshkey.pub
    
    ```

1. 秘密キーと公開キーのペアをダウンロードします。 Cloud Shell のメニューで、 **[ファイルのアップロード/ダウンロード]** &gt; **[ダウンロード]** を選択します。

## <a name="service-principal-configuration"></a>サービス プリンシパルの構成

配置機能では、サービス プリンシパルを使用して、サブスクリプションにリソースをデプロイします。 

1. Azure CLI にサインインします。

    ```azurecli-interactive
    az login
    ```

1. サービス プリンシパルを作成する。 `<subscription-id>` は、実際の Azure サブスクリプション ID に置き換えてください。 また、`<sp-name>` をサービス プリンシパルの名前に置き換えます。

    ```azurecli-interactive
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --name="<sp-name>"
    ```

1. サービス プリンシパルに関する情報が含まれている出力を確認します。 次のフィールドの値をメモします。

    1. アプリケーション ID: `appId`。

    1. パスワード: `password`。

    1. テナント ID: `tenant`。

1. サービス プリンシパルに対するロール割り当てを作成します。 `<appId>` を前の手順でメモしたアプリケーション ID に置き換えてください。

    ```azurecli-interactive
    az role assignment create --assignee <appId> --role "User Access Administrator"
    ```

1. 次のように、サービス プリンシパルのキーをキー コンテナーに追加します。 プレースホルダーの値を前の手順でメモした情報に置き換えてください。 `<environment>` を環境の名前 (`DEMO` など) に置き換えます。 

    ```azurecli-interactive
    az keyvault secret set --name "<environment>-subscription-id" --vault-name "<deployer_kv_user_name>" --value "<subscription-id>";
    az keyvault secret set --name "<environment>-tenant-id"       --vault-name "<deployer_kv_user_name>" --value "<tenant>";
    az keyvault secret set --name "<environment>-client-id"       --vault-name "<deployer_kv_user_name>" --value "<appId>";
    az keyvault secret set --name "<environment>-client-secret"   --vault-name "<deployer_kv_user_name>" --value "<password>";
    ```

## <a name="library-configuration"></a>ライブラリの構成

1. SSH クライアントと[配置機能のセットアップ](#get-ssh-keys)中に取得した SSH キーを使用して、配置機能にサインインします。 SSH クライアントとして PuTTY を使用する場合は、使用する前に、SSH キーを `.ppk` 形式に変換します。

1. 自動化フレームワーク リポジトリを複製した場所に移動します。

    ```bash
    cd  ~/Azure_SAP_Automated_Deployment/sap-hana
    ```

1. 必要に応じて、main ブランチとは別のブランチをチェックアウトします。 既定では、リポジトリの main ブランチが使用されます。
    
    1. `<branch>` を使用するブランチ名またはコミット ハッシュに置き換えます。

        ```azurecli
        git checkout <branch>
        ```

    1. ブランチが想定されているリビジョンであることを確認します。

        ```azurecli-interactive
        git rev-parse HEAD
        ```

1. 作業ディレクトリを作成します。

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/DEMO-EUS2-SAP_LIBRARY; cd $_
    ```

1. JSON 構成ファイルを作成します。

    ```bash
    cat <<EOF > DEMO-EUS2-SAP_LIBRARY.json
    {
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2"
      },
      "deployer": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnet"                                : "DEP00"
      }
    }
    EOF
    ```

1. Terraform を初期化します。

    ```bash
    terraform init  ../../../sap-hana/deploy/terraform/bootstrap/sap_library/
    ```

1. 既定の名前付け規則に従って、Terraform 実行プランを作成します。

    ```bash
    terraform plan                                                                  \
                --var-file=DEMO-EUS2-SAP_LIBRARY.json                           \
                ../../../sap-hana/deploy/terraform/bootstrap/sap_library

    ```

1. Terraform 実行プランを適用して、リソースをデプロイします。

    ```bash
    terraform apply --auto-approve                                                  \
                --var-file=DEMO-EUS2-SAP_LIBRARY.json                           \
                ../../../sap-hana/deploy/terraform/bootstrap/sap_library/

    ```

## <a name="reinitialize-deployment"></a>デプロイを再初期化する

[デプロイ機能](#reinitialize-deployer)と [SAP ライブラリ](#reinitialize-sap-library)の両方を再初期化します。

### <a name="reinitialize-deployer"></a>配置機能を再初期化する

1. SSH クライアントで配置機能にサインインしたままにします。 または、もう一度サインインします。 

1. 作成した作業ディレクトリに移動します。

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LOCAL/DEMO-EUS2-DEP00-INFRASTRUCTURE
    ```

1. `backend` という名前の別のパラメーター ファイルを作成します。 ここでも、既定の名前付け規則に従います。 `resource_group_name` には、Terraform 状態ファイル (`.tfstate`) を持つストレージ アカウントが配置されているリソース グループの名前を使用します。 `storage_account_name` では、`<tfstate_storage_account_name>` を `.tfstate` ファイルの SAP ライブラリ デプロイのストレージ アカウントの名前に置き換えます。 `key` では、配置機能のリソース グループ名と拡張子 `.terraform.tfstate` を組み合わせます。 次に例を示します。

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-DEP00-INFRASTRUCTURE.terraform.tfstate"
    EOF
    ```

1. Terraform を再度初期化します。

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```

1. 「**既存の状態を新しいバックエンドにコピーしますか**」というメッセージが表示されたら、`yes` と入力します。

1. ローカルの状態ファイルを削除します。

    ```bash
    rm terraform.tfstate*
    ```

1. Terraform 実行プランを作成します。 ここでも、既定の名前付け規則に従います。 次に例を示します。

    ```bash
    terraform plan                                                                  \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```
    
1. Terraform 実行プランを適用します。 次に例を示します。

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-DEP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_deployer/
    ```

### <a name="reinitialize-sap-library"></a>SAP ライブラリを再初期化する

1. SSH クライアントで配置機能にサインインしたままにします。 または、もう一度サインインします。 

1. 作成した作業ディレクトリに移動します。

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LIBRARY/DEMO-EUS2-SAP_LIBRARY
    ```

1. `backend` という名前の別のパラメーター ファイルを作成します。 ここでも、既定の名前付け規則に従います。 `resource_group_name` には、Terraform 状態ファイル (`.tfstate`) を持つストレージ アカウントが配置されているリソース グループの名前を使用します。 `storage_account_name` では、`<tfstate_storage_account_name>` を `.tfstate` ファイルの SAP ライブラリ デプロイのストレージ アカウントの名前に置き換えます。 `key` では、配置機能のリソース グループ名と拡張子 `.terraform.tfstate` を組み合わせます。 次に例を示します。

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP_LIBRARY.terraform.tfstate"
    EOF
    ```

1. パラメーター ファイル `backend` の左波かっこ (`{`) の直後に、新しいキーと値のペアを追加します。 `tfstate_resource_id` には、Terraform 状態ファイルのストレージ アカウントのリソース ID を使用します。 `deployer_tfstate_key` には、配置機能状態ファイルのキー名を使用します。 次に例を示します。
    
    ```bash
    {
        "tfstate_resource_id"                   : "<identifier>",
        "deployer_tfstate_key"                  : "<key>",
        "infrastructure": {
            ...
    }
    ```

1. Terraform を再度初期化します。

    ```bash
    terraform init  --backend-config backend                                          \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

1. 「**既存の状態を新しいバックエンドにコピーしますか**」というメッセージが表示されたら、`yes` と入力します。

1. ローカルの状態ファイルを削除します。

    ```bash
    rm terraform.tfstate*
    ```

1. Terraform 実行プランを作成します。 ここでも、既定の名前付け規則に従います。 次に例を示します。

    ```bash
    terraform plan                                                                    \
                    --var-file=DEMO-EUS2-SAP_LIBRARY.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

1. Terraform 実行プランを適用します。 次に例を示します。

    ```bash
    terraform apply --auto-approve                                                    \
                    --var-file=DEMO-EUS2-SAP_LIBRARY.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_library/
    ```

## <a name="deploy-workload-virtual-network"></a>ワークロード仮想ネットワークをデプロイする

次に、SAP ワークロード仮想ネットワークをデプロイします。

1. SSH クライアントで配置機能にサインインしたままにします。 または、もう一度サインインします。 

1. 作業ディレクトリを作成します。 既定の名前付け規則に従います。

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEMO-EUS2-SAP00-INFRASTRUCTURE; cd $_
    ```

1. `backend` というパラメーター ファイルを作成します。 `resource_group_name` には、Terraform 状態ファイル (`.tfstate`) を持つストレージ アカウントが配置されているリソース グループの名前を使用します。 `storage_account_name` では、`<tfstate_storage_account_name>` を `.tfstate` ファイルの SAP ライブラリ デプロイのストレージ アカウントの名前に置き換えます。 `key` では、配置機能のリソース グループ名と拡張子 `.terraform.tfstate` を組み合わせます。 次に例を示します。

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP00-INFRASTRUCTURE.terraform.tfstate"
    EOF
    ```

1. Terraform を再度初期化します。

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

1. Terraform 実行プランを作成します。 ここでも、既定の名前付け規則に従います。 次に例を示します。

    ```bash
    terraform plan                                                                  \
                --var-file=DEMO-EUS2-SAP00-INFRASTRUCTURE.json                  \
                ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

1. Terraform 実行プランを適用します。 次に例を示します。

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-SAP00-INFRASTRUCTURE.json                  \
                    ../../../sap-hana/deploy/terraform/run/sap_landscape/
    ```

## <a name="sap-deployment-unit"></a>SAP の展開単位

次に、SAP 展開単位を設定します。

1. SSH クライアントで配置機能にサインインしたままにします。 または、もう一度サインインします。

1. 作業ディレクトリを作成します。 既定の名前付け規則に従います。

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEMO-EUS2-SAP00-X00; cd $_
    ```

1. `backend` という名前の別のパラメーター ファイルを作成します。 `resource_group_name` には、Terraform 状態ファイル (`.tfstate`) を持つストレージ アカウントが配置されているリソース グループの名前を使用します。 `storage_account_name` では、`<tfstate_storage_account_name>` を `.tfstate` ファイルの SAP ライブラリ デプロイのストレージ アカウントの名前に置き換えます。 `key` では、配置機能のリソース グループ名と拡張子 `.terraform.tfstate` を組み合わせます。 次に例を示します。

    ```bash
    cat <<EOF > backend
    resource_group_name   = "DEMO-EUS2-SAP_LIBRARY"
    storage_account_name  = "<tfstate_storage_account_name>"
    container_name        = "tfstate"
    key                   = "DEMO-EUS2-SAP00-X00.terraform.tfstate"
    EOF
    ```

1. 次のように入力パラメーターを含む JSON パラメーター ファイルを作成します。 サンプルの値は必ず実際の値に置き換えてください。

    ```bash
    cat <<EOF > DEMO-EUS2-SAP00-X00.json
    {
      "tfstate_resource_id"                   : "<resource-id>",
      "deployer_tfstate_key"                  : "DEMO-EUS2-DEP00-INFRASTRUCTURE.terraform.tfstate",
      "landscape_tfstate_key"                 : "DEMO-EUS2-SAP00-INFRASTRUCTURE.terraform.tfstate",
      "infrastructure": {
        "environment"                         : "DEMO",
        "region"                              : "eastus2",
        "vnets": {
          "sap": {
            "name"                            : "SAP00",
            "subnet_db": {
              "prefix"                        : "0.0.0.0/28"
            },
            "subnet_web": {
              "prefix"                        : "0.0.0.0/28"
            },
            "subnet_app": {
              "prefix"                        : "0.0.0.0/27"
            },
            "subnet_admin": {
              "prefix"                        : "0.0.0.0/27"
            }
          }
        }
      },
      "databases": [
        {
          "platform"                          : "HANA",
          "high_availability"                 : false,
          "size"                              : "S4Demo",
          "os": {
            "publisher"                       : "SUSE",
            "offer"                           : "sles-sap-12-sp5",
            "sku"                             : "gen2",
            "version"                         : "latest"
          }
        }
      ],
      "application": {
        "enable_deployment"                   : true,
        "sid"                                 : "X00",
        "scs_instance_number"                 : "00",
        "ers_instance_number"                 : "10",
        "scs_high_availability"               : false,
        "application_server_count"            : 3,
        "webdispatcher_count"                 : 1,
        "authentication": {
          "type"                              : "key",
          "username"                          : "azureadm"
        }
      }
    }
    EOF
    ```

1. Terraform を再度初期化します。

    ```bash
    terraform init  --backend-config backend                                        \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
  
    ```

1. Terraform 実行プランを作成します。 ここでも、既定の名前付け規則に従います。 次に例を示します。

    ```bash
    terraform plan                                                                  \
                    --var-file=DEMO-EUS2-SAP00-X00.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
    ```

1. Terraform 実行プランを適用します。 次に例を示します。

    ```bash
    terraform apply --auto-approve                                                  \
                    --var-file=DEMO-EUS2-SAP00-X00.json                             \
                    ../../../sap-hana/deploy/terraform/run/sap_system/
    ```

## <a name="ansible-configuration"></a>Ansible の構成

Ansible プレイブックを実行してセットアップを構成します。 これらのプレイブックは、`/sap-hana/deploy/ansible` 内の自動化フレームワーク リポジトリにあります。

| ファイル名          | 説明                        |
| ----------------- | ---------------------------------- |
| `playbook_01_os_base_config.yaml` | 基本オペレーティング システム (OS) の構成 |
| `playbook_02_os_sap_specific_config.yaml ` | SAP 固有の OS 構成 |
| `playbook_03_bom_processing.yaml` | SAP 部品表 (BOM) 処理ソフトウェアのダウンロード |
| `playbook_04a_sap_scs_install.yaml   ` | SAP セントラル サービス (SCS) のインストール |
| `playbook_05a_hana_db_install.yaml ` | SAP HANA データベースのインストール |
| `playbook_06a_sap_dbload.yaml` | データベース ローダー |
| `playbook_06b_sap_pas_install.yaml` | SAP プライマリ アプリケーション サーバー (PAS) のインストール |
| `playbook_06c_sap_app_install.yaml` | SAP アプリケーション サーバー のインストール |
| `playbook_06d_sap_web_install.yaml` | SAP Web Dispatcher のインストール |
| `playbook_06_00_00_pacemaker.yaml` | Pacemaker クラスターの構成 |
| `playbook_06_00_01_pacemaker_scs.yaml` | SCS 用の Pacemaker の構成 | 
| `playbook_06_00_03_pacemaker_hana.yaml` | SAP HANA データベース用の Pacemaker の構成 |

1 つのプレイブックまたは複数のプレイブックを実行するには、次のように `ansible-playbook` コマンドを使用します。 すべてのプレースホルダーの値を実際の情報に変更してください。

- `<your-sapbits-path>` の値を SAP ライブラリのストレージ アカウント `sapbits` へのパスに変更します。
- `<azure-admin>` を Azure 管理者のユーザー名に変更します。
- `<ssh-key`> を使用する秘密 SSH キーに変更します。
- 設定の必要に応じて、`--extra-vars` の他の値を変更します。

問題が発生した場合は、Azure 環境に [SAP ソフトウェアをダウンロード](automation-software.md)済みであることを確認してください。

```azurecli-interactive
export           ANSIBLE_HOST_KEY_CHECKING=False
# export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=Yes
# export           ANSIBLE_KEEP_REMOTE_FILES=1


ansible-playbook                                                                                                      \
  --inventory   new-hosts.yaml                                                                                        \
  --user        <azure-admin>                                                                                              \
  --private-key <ssh-key>                                                                                                \
  --extra-vars="{                                                                                                     \
                  \"bom_base_name\":                \"HANA_2_00_053_v001\",                                           \
                  \"download_templates\":           \"false\",                                                        \
                  \"sapbits_location_base_path\":   \"<your-sapbits-path>",        \
                  \"target_media_location\":        \"/usr/sap/install\",                                             \
                  \"sap_sid\":                      \"X00\",                                                          \
                  \"hdb_sid\":                      \"HDB\"                                                           \
                }"                                                                                                    \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_00_transition_start_for_sap_install_refactor.yaml     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_01_os_base_config.yaml                       \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_02_os_sap_specific_config.yaml               \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_03_bom_processing.yaml                       \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_04a_sap_scs_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_05a_hana_db_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06a_sap_dbload.yaml                          \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06b_sap_pas_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06c_sap_app_install.yaml                     \
~/Azure_SAP_Automated_Deployment/centiq-sap-hana/deploy/ansible/playbook_06d_sap_web_install.yaml

```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [自動デプロイを使用してすばやく開始する](automation-get-started.md)
