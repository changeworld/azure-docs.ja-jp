---
title: Ansible を使用して Azure 内に仮想マシン スケール セットを作成する
description: Ansible を使用して、Azure 内で仮想マシン スケール セットを作成し、構成する方法について説明します
ms.service: ansible
keywords: ansible, azure, devops, bash, プレイブック, 仮想マシン, 仮想マシン スケール セット, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 5f915f7b1b425a3bd6e5d62eb70bb3f633b7eda8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011710"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Ansible を使用して Azure 内に仮想マシン スケール セットを作成する
Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 Ansible を使用すると、他の Azure リソースを管理するのと同様に、Azure 内の仮想マシン スケール セット (VMSS) を管理できます。 この記事では、Ansible を使用して、仮想マシン スケール セットを作成し、スケール アウトする方法について説明します。 

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- **Ansible の構成** - [Azure 資格情報を作成し、Ansible を構成します。](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible および Azure Python SDK モジュール** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)

> [!Note]
> このチュートリアルでは、以下のサンプルのプレイブックを実行する際に Ansible 2.6 が必要です。 

## <a name="create-a-vmss"></a>VMSS を作成する
このセクションでは、次のリソースを定義するサンプルの Ansible プレイブックを示します。
- **リソース グループ**。すべてのリソースがデプロイされるリソース グループです。
- **仮想ネットワーク**。10.0.0.0/16 アドレス空間内の仮想ネットワークです。
- **サブネット**。仮想ネットワーク内のサブネットです。
- **パブリック IP アドレス**。このアドレスを使用して、インターネットのリソースにアクセスできます。
- **ネットワーク セキュリティ グループ**。仮想 マシン スケール セットに出入りするネットワーク トラフィックのフローを制御します。
- **ロード バランサー**。ロード バランサー ルールを使用して、定義された一連の VM にトラフィックを分散します。
- **仮想マシン スケール セット**。作成されたすべてのリソースを使用します。

*admin_password* の値には、独自のパスワードを入力してください。

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Ansible を使用して仮想マシン スケール セットを作成するには、上記のプレイブックを `vmss-create.yml` という名前で保存するか、[サンプルの Ansible プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml)します。

Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

  ```bash
  ansible-playbook vmss-create.yml
  ```

プレイブックを実行すると、次の例のような出力が表示され、仮想マシン スケール セットが正常に作成されたことが示されます。

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>VMSS をスケールアウトする
作成された仮想マシン スケール セットには、2 つのインスタンスがあります。 Azure Portal 内で仮想マシン スケール セットに移動すると、**[Standard_DS1_v2 (2 instances)]\(Standard_DS1_v2 (2 インスタンス)\)** が表示されます。 Cloud Shell 内で次のコマンドを実行して、[Azure Cloud Shell](https://shell.azure.com/) を使用することもできます。

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

出力は次のようになります。

  ```bash
  {
    "capacity": 2,
  }
  ```

ここで、インスタンス数を 2 から 3 に増やしましょう。 次の Ansible プレイブック コードは、仮想マシン スケールに関する情報を取得し、その容量を 2 から 3 に変更します。 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

作成した仮想マシン スケール セットをスケールアウトするには、上記のプレイブックを `vmss-scale-out.yml` という名前で保存するか、[サンプルの Ansible プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)します。 

次のコマンドでプレイブックを実行します。

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Ansible プレイブックを実行して得られた出力を見ると、仮想マシン スケール セットが正常にスケールアウトされたことがわかります。

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Azure Portal 内で構成した仮想マシン スケール セットに移動すると、**[Standard_DS1_v2 (3 instances)]\(Standard_DS1_v2 (3 インスタンス)\)** が表示されます。 次のコマンドを実行して、[Azure Cloud Shell](https://shell.azure.com/) で変更を検証することもできます。

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Cloud Shell 内でコマンドを実行した結果を見ると、現在 3 つのインスタンスが存在していることがわかります。 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [VMSS 用の Ansible サンプル プレイブック](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)