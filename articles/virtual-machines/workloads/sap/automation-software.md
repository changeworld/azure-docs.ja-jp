---
title: 自動化フレームワーク用の SAP ソフトウェアをダウンロードする
description: Azure で SAP デプロイ自動化フレームワークを使用するには、Ansible プレイブックを使用して Azure 環境に SAP ソフトウェアをダウンロードします。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: d93f1d9daa433a6abcd6ad54426060795708c9ef
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725152"
---
# <a name="download-sap-software"></a>SAP ソフトウェアをダウンロードする

[Azure で SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)を使用するには、SAP ソフトウェアのコピーが必要です。 ストレージ アカウントに SAP メディアを配置できるように、[Azure 環境を準備します](#configure-key-vault)。 その後、[Ansible プレイブックを使用して、SAP ソフトウェアをダウンロードします](#download-sap-software)。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。
- ソフトウェア ダウンロード権限を持つ SAP ユーザーアカウント (SAP ユーザーまたは S ユーザー アカウント)。

## <a name="configure-key-vault"></a>キー コンテナーを構成する

まず、配置機能のキー コンテナーのシークレットを構成します。 この構成例では、リソース グループは `DEMO-EUS2-DEP00-INFRASTRUCTURE` または `DEMO-SCUS-DEP00-INFRASTRUCTURE` です。

1. 使用するアカウントで [Azure CLI にサインインします](/cli/azure/authenticate-azure-cli)。

    ```azurecli-interactive
    az login
    ```

1. SAP ユーザー アカウントのユーザー名を持つシークレットを追加します。 `<keyvault-name>` を配置機能のキー コンテナーの名前に置き換えます。 また、`<sap-username>` も SAP ユーザー名に置き換えます。

    ```azurecli-interactive
     az keyvault secret set --name "S-Username" --vault-name "<keyvault-name>" --value "<sap-username>";
    ```

2. SAP ユーザー アカウントのパスワードを持つシークレットを追加します。 `<keyvault-name>` を配置機能のキー コンテナーの名前に置き換えます。 また、`<sap-password>` も SAP パスワードに置き換えます。

```azurecli-interactive
az keyvault secret set --name "S-Password" --vault-name "<keyvault-name>" --value "<sap-password>";
```

3. ストレージ アカウント `sapbits` のためのこの手順では、その他に 2 つのシークレットが必要ですが、自動化フレームワークによって自動的に設定されます。 ただし、これらが配置機能のキー コンテナーに存在するかどうかを確認することをお勧めします。

    ```azurecli-interactive
    sapbits-access-key
    sapbits-location-base-path
    ```

## <a name="download-sap-software"></a>SAP ソフトウェアをダウンロードする

次に、ダウンロード処理用の [SAP パラメーター ファイルを構成します](#configure-parameters-file)。 その後、[Ansible プレイブックを使用して、SAP ソフトウェアをダウンロードします](#download-sap-software)。 

### <a name="configure-parameters-file"></a>パラメーター ファイルを構成する

SAP パラメーター ファイルを構成します。

1. SAP デプロイ ワークスペースのディレクトリに移動します。

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/
    ```

1. `BOMS` という新しいディレクトリを作成します。

    ```bash
    mkdir -p ~/Azure_SAP_Automated_Deployment/WORKSPACES/BOMS; cd $_
    ```

1. SAP パラメーターの YAML ファイルを作成します。

    ```bash
    cat <<EOF > sap-parameters.yaml
    ---
    bom_base_name:               S41909SPS03_v0006ms
    sapbits_location_base_path:  https://<storage_account_FQDN>/sapbits
    kv_name: Name of your Management/Control Plane keyvault
    secret_prefix:
    ...
    EOF
    ```

1. エディターで `sap-parameters.yaml` を開きます。

    ```bash
    vi sap-parameters.yaml
    ``` 

1. 次のパラメーターを更新します。

    1. `bom_base_name` の値を `S41909SPS03_v0006ms` に変更します。

    2. `sapbits_location_base_path` の値をストレージ アカウント `sapbits` へのパスに変更します。

    3. `kv_name` の値を配置機能のキー コンテナーの名前に変更します。
   
   4. (必要に応じて) 環境内のプレフィックスに一致するように `secret_prefix` の値を変更します (例: DEV-WEEU-SAP)
   
### <a name="execute-ansible-playbooks"></a>Ansible プレイブックを実行する

次に、Ansible プレイブックを実行します。 プレイブックを実行する方法の 1 つとして、検証コントロール テスト メニューを使用する方法があります。

1. 検証コントロール テスト メニュー スクリプトを実行します。

    ```bash
    ~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/validator_test_menu.sh
    ```

1. 実行するプレイブックを選択します。 次に例を示します。
    
    ```output
    1) BoM Downloader
    2) Quit
    Please select playbook: 
    ```


もう 1 つの方法は、`ansible-playbook` コマンドを使用して、Ansible プレイブックを実行することです。 

```bash
ansible-playbook                                                                                   \
  --user        azureadm                                                                           \
  --extra-vars="@sap-parameters.yaml"                                                              \
  ~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/playbook_bom_downloader.yaml
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [自動化フレームワークのデプロイの概要](automation-get-started.md)
