---
title: Ansible を使用して、Azure 内に Azure Kubernetes Service クラスターを作成して構成する
description: Ansible を使用して、Azure 内に Azure Kubernetes Service クラスターを作成し、管理する方法について説明します。
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, プレイブック, aks, コンテナー, Kubernetes
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 6d7c5f961256e0ae1831bd76353cadd761f4b8ac
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011990"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Ansible を使用して、Azure 内に Azure Kubernetes Service クラスターを作成して構成する
Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 Ansible では、Azure Kubernetes Service (AKS) の管理が可能です。 この記事では、Ansible を使用して、Azure Kubernetes Service クラスターを作成し、構成する方法について説明します。

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- **Ansible の構成** - [Azure 資格情報を作成し、Ansible を構成します。](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible および Azure Python SDK モジュール** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Azure サービス プリンシパル** - [サービス プリンシパルを作成](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal)するときは、**appId**、**displayName**、**password**、および **tenant** の値に注意してください。

> [!Note]
> このチュートリアルでは、以下のサンプルのプレイブックを実行する際に Ansible 2.6 が必要です。 

## <a name="create-a-managed-aks-cluster"></a>マネージド AKS クラスターを作成する
次のサンプルの Ansible プレイブックでは、リソース グループとその中に存在する AKS クラスターが作成されます。

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

上記の Ansible プレイブックのコードの説明を次に示します。
- **tasks** 内の最初のセクションでは、**eastus** という場所の内部にある **myResourceGroup** という名前のリソース グループが定義されています。 
- **tasks** 内の 2 番目のセクションでは、**myResourceGroup** リソース グループ内にある **myAKSCluster** という名前の AKS クラスターが定義されています。 

Ansible を使用して AKS クラスターを作成するには、上記のサンプルのプレイブックを `azure_create_aks.yml` という名前で保存して、このプレイブックを次のコマンドで実行します。

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

**ansible-playbook* コマンドを実行したときの出力は、次のようになります。これを見ると、AKS クラスターが正常に作成されたことがわかります。

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>AKS ノードのスケーリング

前のセクションで示したサンプルのプレイブックでは、2 つのノードが定義されています。 クラスター上のコンテナー ワークロードを増減する必要がある場合は、ノードの数を簡単に調整できます。 このセクションのサンプルのプレイブックでは、ノードが数が 2 から 3 に増えています。 ノードの数を変更するには、**agent_pool_profiles** ブロック内の **count** 値を変更します。 

**service_principal** ブロック内に独自の `ssh_key`、`client_id`、および `client_secret` を入力します。

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Ansible を使用して Azure Kubernetes Service クラスターをスケーリングするには、上記のプレイブックを *azure_configure_aks.yml* という名前で保存して、このプレイブックを次のように実行します。

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

次の出力は、AKS クラスターが正常に作成されたことを示しています。

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションのスケーリング](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-scale)