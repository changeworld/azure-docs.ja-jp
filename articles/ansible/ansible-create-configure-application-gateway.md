---
title: Ansible (プレビュー)を使用して Azure アプリケーション ゲートウェイで Web トラフィックを管理する
description: Ansible 作成の使用と Azure アプリケーション ゲートウェイ を構成し Web トラフィックを管理する方法について説明する
ms.service: ansible
keywords: Ansible、Azure、DevOps、Bash、プレイブック、Azure アプリケーションゲートウェイ、ロード バランサー、Web トラフィック
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 02b98cb22d897fc9599f6e44ddc57ef4211b0893
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410842"
---
# <a name="manage-web-traffic-with-azure-application-gateway-using-ansible-preview"></a>Ansible (プレビュー)を使用して Azure アプリケーション ゲートウェイで Web トラフィックを管理する
[Azure アプリケーション ゲートウェイ](https://docs.microsoft.com/azure/application-gateway/)は、アプリケーションに対するトラフィックを管理できる Web トラフィック ロードバランサーです。 

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 このアーティクルでは、Ansible を使用した Azure アプリケーション ゲートウェイの作成と それを使用して Azure コンテナ― インスタンスで実行されている 2 つの Web サーバーのトラフィックを管理する方法を説明します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ネットワークのセットアップ
> * httpd イメージで 2 つの Azure コンテナー インスタンスを作成します
> * バックエンド プール内の上の Azure コンテナー インスタンスでアプリケーション ゲートウェイを作成します


## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> このチュートリアルでは、次のサンプルのプレイブックを実行するのに Ansible 2.7 が必要です。 `sudo pip install ansible[azure]==2.7.0rc2` を実行すれば、 Ansible 2.7 RC バージョンをインストールすることができます。 Ansible 2.7 は 2018 年 10 月にリリースされます。 その後、既定のバージョンは 2.7 になるため、ここでバージョンを指定する必要があります。 

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。  

次の例では、**myResourceGroup** という名前のリソース グループを **eastus** に作成します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

上記のプレイブックを *rg.yml* として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する 
アプリケーション ゲートウェイが他のリソースと通信できるように仮想ネットワークを作成する必要があります。 

次の例では、**myVNet** という名前の仮想ネットワーク、**myAGSubnet** という名前のサブネット、そして **mydomain** というドメイン名の **myAGPublicIPAddress** という名前のパブリック IP アドレスを作成します。 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

上記のプレイブックを *vnet_create.yml* として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook vnet_create.yml
```

## <a name="create-backend-servers"></a>バックエンド サーバーの作成
この例では、 httpd images で 2 つの Azure コンテナ― インスタンスを作成し、アプリケーション ゲートウェイのバックエンドサーバーとして使用します。  

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

上記のプレイブックを *aci_create.yml* として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

これで、アプリケーション ゲートウェイを作成してみましょう。 次の例では、**myAppGateway** という名前のバックエンド、フロントエンド、そして http 構成付きのアプリケーション ゲートウェイを作成します。  

> [!div class="checklist"]
> * **gateway_ip_configurations** ブロックで定義されている **appGatewayIP**　‐　サブネット参照は、ゲートウェイの IP 構成に必要です。 
> * **backend_address_pools** ブロックで定義されている **appGatewayBackendPool** - アプリケーション ゲートウェイには、少なくとも 1 つのバックエンド アドレス プールが必要です。 
> * **backend_http_settings_collection**ブロックで定義されている**appGatewayBackendHttpSettings** - ポート 80 と HTTP プロトコルが通信に使用されることを指定します。 
> * **backend_http_settings_collection** ブロックで定義されている **appGatewayHttpListener** - appGatewayBackendPool に関連付けられている既定のリスナー。 
> * **frontend_ip_configurations** ブロックで定義されている **appGatewayFrontendIP** - appGatewayHttpListener に myAGPublicIPAddres を割り当てます。 
> * **request_routing_rules** ブロックで定義されている **ルール1** - appGatewayHttpListener に関連付けられている既定のルーティング規則。 

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

上記のプレイブックを *appgw_create.yml* として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook appgw_create.yml
```

アプリケーション ゲートウェイの作成には数分かかる場合があります。 

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

上のネットワークリソースのサンプルプレイブックでは、**mydomain** という名前のドメインが **eastus** で作成されました。 ブラウザー、タイプ `http://mydomain.eastus.cloudapp.azure.com` に移動することができ、次のページではアプリケーション ゲートウェイが正しく動作していることを確認することができます。

![アプリケーション ゲートウェイにアクセスする](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

これらのリソースが不要な場合は、下記の例を実行することでこれらを削除できます。 これは **myResourceGroup** という名前のリソース グループを削除します。 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

上記のプレイブックを *rg_delete*.yml として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Azure 上の Ansible](https://docs.microsoft.com/azure/ansible/)
