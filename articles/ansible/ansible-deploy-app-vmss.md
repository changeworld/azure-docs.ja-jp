---
title: Ansible を使用して Azure 内の仮想マシン スケール セットにアプリケーションをデプロイする
description: Ansible を使用して仮想マシン スケール セットを構成し、Azure 内にあるこの仮想マシン スケール セットにアプリケーションをデプロイする方法について説明します。
ms.service: ansible
keywords: ansible, azure, devops, bash, プレイブック, 仮想マシン, 仮想マシン スケール セット, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: b9c8058606e13c0db4908530e98cddb69d2caf50
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011500"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Ansible を使用して Azure 内の仮想マシン スケール セットにアプリケーションをデプロイする
Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 また、Ansible を使用して、Azure にアプリケーションをデプロイすることもできます。 この記事では、Java アプリケーションを Azure 仮想マシン スケール セット (VMSS) にデプロイする方法について説明します。  

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- **Ansible の構成** - [Azure 資格情報を作成し、Ansible を構成します。](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible および Azure Python SDK モジュール** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **仮想マシン スケール セット** - 仮想マシン スケール セットがまだない場合は、[Ansible を使用して仮想マシン スケール セットを作成](ansible-create-configure-vmss.md)します。 
- **git** - このチュートリアルでは、[git](https://git-scm.com) を使用して Java サンプルをダウンロードします。
- **Java SE Development Kit (JDK)** - JDK は、サンプルの Java プロジェクトを作成する際に使用します。
- **Apache Maven ビルド ツール** - [Apache Maven ビルド ツール](https://maven.apache.org/download.cgi)は、サンプルの Java プロジェクトを作成する際に使用します。

> [!Note]
> このチュートリアルでは、以下のサンプルのプレイブックを実行する際に Ansible 2.6 が必要です。 

## <a name="get-host-information"></a>ホスト情報を取得する

このセクションでは、Ansible を使用して、Azure 仮想マシン グループのホスト情報を取得する方法について説明します。 サンプルの Ansible プレイブックを次に示します。 このコードは、指定されたリソース グループ内のパブリック IP アドレスとロード バランサーを取得し、**saclesethosts** という名前のホスト グループをインベントリ内に作成します。 

このサンプルのプレイブックは、`get-hosts-tasks.yml` という名前で保存します。 

  ```yaml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>デプロイするアプリケーションを準備する  

このセクションでは、git を使用して GitHub から Java サンプル プロジェクトを複製することにより、プロジェクトを作成します。 次のプレイブックを `app.yml` という名前で保存します。

  ```yaml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks: 
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

次のコマンドを使用して、サンプルの Ansible プレイブックを実行します。

  ```bash
  ansible-playbook app.yml
  ```

ansible-playbook コマンドを実行すると、次のような出力が表示されます。これを見ると、作成されたサンプルのアプリは、GitHub から複製されたことがわかります。

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>VMSS にアプリケーションをデプロイする

Ansible プレイブックの次のセクションでは、**saclesethosts** という名前のホスト グループに JRE (Java Runtime Environment) がインストールされ、**saclesethosts** という名前のホスト グループに Java アプリケーションがデプロイされます。 

(`admin_password` は、自分のパスワードに変更してください。)

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
    hosts: scalesethosts
    become: yes
    vars:
      workspace: ~/src/helloworld
      admin_username: azureuser

    tasks:
    - name: Install JRE
      apt:
        name: default-jre
        update_cache: yes

    - name: Copy app to Azure VM
      copy:
        src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
        dest: "/home/{{ admin_username }}/helloworld.jar"
        force: yes
        mode: 0755

    - name: Start the application
      shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
      async: 5000
      poll: 0
  ```

上記のサンプルの Ansible プレイブックを `vmss-setup-deploy.yml` という名前で保存するか、[サンプルのプレイブック全体をダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss)します。 

接続の種類として ssh を使用する場合、パスワードを併用するには、sshpass プログラムをインストールする必要があります。 
  - Ubunto 16.04 の場合は、コマンド `apt-get install sshpass` を実行します。
  - CentOS 7.4 の場合は、コマンド `yum install sshpass` を実行します。

**[Using an SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.Add this host's fingerprint to your known_hosts file to manage this host.]\(ホスト キーのチェックが有効になっていますが、sshpass ではこれがサポートされません。そのため、キーの代わりに SSH パスワードを使用することはできません。このホストを管理するには、ホストのフィンガープリントを既知のホスト ファイルに追加してください。\)** このエラーが発生した場合は、ホスト キーの検査を無効にするために、`/etc/ansible/ansible.cfg` ファイルまたは `~/.ansible.cfg` ファイルに次の行を追加します。
  ```bash
  [defaults]
  host_key_checking = False
  ```

次のコマンドを使用して、プレイブックを実行します。

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

ansible-playbook コマンドを実行して出力された内容を見ると、サンプルの Java アプリケーションが仮想マシン スケール セットのホスト グループにインストールされていることがわかります。

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

おめでとうございます。 アプリケーションが Azure 内で実行されています。 仮想マシン スケール セットのロード バランサーの URL に移動できるようになりました。

![Azure 内の仮想マシン スケール セットで実行中の Java アプリ。](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [VMSS 用の Ansible サンプル プレイブック](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)