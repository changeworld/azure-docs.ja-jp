---
title: Azure Cloud Shell での Bash を使用した Ansible の実行
description: Azure Cloud Shell で Bash を使用してさまざまな Ansible タスクを実行する方法について説明します
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, プレイブック, Bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 9fe65f4cf10119002bcb7a3855d112d850e20f1a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150381"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Azure Cloud Shell での Bash を使用した Ansible の実行

このチュートリアルでは、Azure Cloud Shell で Bash からさまざまな Ansible タスクを実行する方法について説明します。 これらのタスクには、仮想マシンに接続するタスクと、Azure リソース グループを作成および削除するための Ansible プレイブックを作成するタスクが含まれます。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

- **Azure 資格情報** - [Azure 資格情報の作成と Ansible の構成](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Azure Cloud Shell の構成** - Azure Cloud Shell を初めて使う場合は、Cloud Shell を起動および構成する方法について、「[Azure Cloud Shell の Bash のクイックスタート](https://docs.microsoft.com/azure/cloud-shell/quickstart)」を参照してください。 以下から Cloud Shell 専用の Web サイトを起動します。

[![Cloud Shell を起動する](https://shell.azure.com/images/launchcloudshell.png "Cloud Shell を起動する")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Ansible を使用した VM への接続
Ansible には、Azure Resource Manager に対して API 要求を行って Azure リソースの動的インベントリを生成する、[azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) という名前の Python スクリプトが用意されています。 `azure_rm.py` スクリプトを使用して Azure 仮想マシンに接続する手順を次に示します。

1. Cloud Shell で Bash を開きます。 Cloud Shell ウィンドウの左側にシェルの種類が表示されます。

1. 使用できる仮想マシンがない場合は、次のコマンドを Cloud Shell に入力して、テスト用の仮想マシンを作成します。

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. GNU `wget` コマンドを使用して、`azure_rm.py` スクリプトを取得します。

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. `chmod` コマンドを使用して、`azure_rm.py` スクリプトへのアクセス許可を変更します。 次のコマンドでは、`+x` パラメーターを使用して、指定されたファイル (`azure_rm.py`) の実行を許可しています。

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. [ansible コマンド](https://docs.ansible.com/ansible/2.4/ansible.html)を使用して、仮想マシンに接続します。 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  接続されると、次の出力のような結果が表示されます。

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. このセクションでリソース グループと仮想マシンを作成した場合

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Cloud Shell でのプレイブックの実行
[ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) コマンドを使用すると、Ansible プレイブックを実行してターゲット ホスト上でタスクを実行できます。 このセクションでは、Cloud Shell を使用して、2 つのプレイブックを作成して実行します。1 つはリソース グループを作成するためのプレイブック、もう 1 つはリソース グループを削除するためのプレイブックです。 

1. 次のように、`rg.yml` という名前のファイルを作成します。

  ```azurecli-interactive
  vi rg.yml
  ```

1. 次のコンテンツを、(現在 VI エディターのインスタンスがホストされている) Cloud Shell ウィンドウにコピーします。

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        location: eastus
  ```

1. ファイルを保存し、VI エディターを終了します。そのためには、「`:wq`」と入力し、Enter キーを押します。

1. `ansible-playbook` コマンドを使用して `rg.yml` プレイブックを実行します。

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. 次の出力のような結果が表示されます。

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. デプロイを検証します。

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. リソース グループを作成した後は、このリソース グループを削除するための、2 つ目の Ansible プレイブックを作成します。

  ```azurecli-interactive
  vi rg2.yml
  ```

1. 次のコンテンツを、(現在 VI エディターのインスタンスがホストされている) Cloud Shell ウィンドウにコピーします。

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        state: absent
  ```

1. ファイルを保存し、VI エディターを終了します。そのためには、「`:wq`」と入力し、Enter キーを押します。

1. `ansible-playbook` コマンドを使用して `rg2.yml` プレイブックを実行します。

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. 次の出力のような結果が表示されます。

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Ansible を使用して Azure で基本的な仮想マシンを作成する](/azure/virtual-machines/linux/ansible-create-vm)
