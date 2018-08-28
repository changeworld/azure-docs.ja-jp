---
title: Ansible を使用して Azure 内の Linux 仮想マシンを管理する
description: Ansible を使用して Azure 内の Linux 仮想マシンを管理する方法について説明します。
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, プレイブック, Bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 66106346b298fae22cce47081916a6c8eec8fd40
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250671"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Ansible を使用して Azure 内の Linux 仮想マシンを管理する
Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 Azure 仮想マシンも、他のリソースと同様、Ansible を使って管理することができます。 この記事では、Ansible プレイブックを使って Linux 仮想マシンを起動したり停止させたりする方法について説明します。 

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

- **Azure Cloud Shell を構成**するか、または **Linux 仮想マシンに Ansible をインストールして構成**します。

  **Azure Cloud Shell を構成する**

  1. **Azure Cloud Shell の構成** - Azure Cloud Shell を初めて使う場合は、Cloud Shell を起動および構成する方法について、「[Azure Cloud Shell の Bash のクイック スタート](/azure/cloud-shell/quickstart)」を参照してください。 

  1. **Linux 仮想マシン** - Linux 仮想マシンがない場合は、[Ansible で仮想マシンを作成](ansible-create-vm.md)できます。

  **- または -**

  **Linux 仮想マシンに Ansible をインストールして構成する**

  1. **Ansible のインストール** - [サポートされている Linux プラットフォーム](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine)に Ansible をインストールします。

  1. **Ansible の構成** - [Azure 資格情報を作成し、Ansible を構成します。](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Ansible を使用して Azure 仮想マシンの割り当てを解除 (停止) する
このセクションでは、Ansible を使用して Azure 仮想マシンの割り当てを解除 (停止) する方法について説明します。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. [Cloud Shell](/azure/cloud-shell/overview) を開きます。

1. 次のように、(プレイブックを保存するための) `azure_vm_stop.yml` という名前のファイルを作成し、vi エディターで開きます。

  ```azurecli-interactive
  vi azure_vm_stop.yml
  ```

1. **I** キーを選択して挿入モードに入ります。

1. 以下のサンプル コードをエディターに貼り付けます。

    ```yaml
    - name: Stop Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
            allocated: no 
    ```

1. **Esc** キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

1. サンプル Ansible プレイブックを実行します。

  ```bash
  ansible-playbook azure_vm_stop.yml
  ```

1. 出力は、次の例のようになり、仮想マシンが正常に割り当て解除 (停止) されたことが示されます。

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>割り当て解除 (停止) された Azure 仮想マシンを Ansible を使用して起動する
このセクションでは、割り当て解除 (停止) された Azure 仮想マシンを Ansible を使用して起動する方法について説明します。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. [Cloud Shell](/azure/cloud-shell/overview) を開きます。

1. 次のように、(プレイブックを保存するための) `azure_vm_start.yml` という名前のファイルを作成し、vi エディターで開きます。

  ```azurecli-interactive
  vi azure_vm_start.yml
  ```

1. **I** キーを選択して挿入モードに入ります。

1. 以下のサンプル コードをエディターに貼り付けます。

    ```yaml
    - name: Start Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Start the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
    ```

1. **Esc** キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

1. サンプル Ansible プレイブックを実行します。

  ```bash
  ansible-playbook azure_vm_start.yml
  ```

1. 出力は、次の例のようになり、仮想マシンが正常に起動されたことが示されます。

    出力は、次の例のようになり、仮想マシンが正常に起動されたことが示されます。

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Ansible を使用した Azure の動的インベントリの管理](../../ansible/ansible-manage-azure-dynamic-inventories.md)