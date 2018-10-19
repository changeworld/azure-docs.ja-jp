---
title: Ansible (プレビュー) を使用して Azure Web アプリを作成する
description: Linux 上の App Service で、Ansible を使用して、Java 8 と Tomcat コンテナー ランタイムで Web アプリを作成する方法について学習します。
ms.service: ansible
keywords: ansible、azure、devops、bash、プレイブック、Azure App Service、Web アプリ、Java
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 1899b1fc1e0a38d859fb3a7ce2153585579650f3
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586717"
---
# <a name="create-azure-app-service-web-apps-using-ansible-preview"></a>Ansible (プレビュー) を使用して Azure App Service Web アプリ を作成する
[App Service Web アプリ](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (または単に Web アプリ) は、Web アプリケーション、REST API、モバイル バックエンドをホストするためのサービスです。 開発には、.NET、.NET Core、Java、Ruby、Node.js、PHP、Python のうち、お気に入りの言語をご利用いただけます。

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 この記事では、Ansible を使用して、Java ランタイムで Web アプリを作成する方法を示します。 

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> このチュートリアルでは、次のサンプルのプレイブックを実行するのに Ansible 2.7 が必要です。 `sudo pip install ansible[azure]==2.7.0rc2` を実行すれば、 Ansible 2.7 RC バージョンをインストールすることができます。 Ansible 2.7 は 2018 年 10 月にリリースされます。 その後、既定のバージョンは 2.7 になるため、ここでバージョンを指定する必要があります。 

## <a name="create-a-simple-app-service"></a>簡素な App Service の作成
このセクションでは、次のリソースを定義するサンプルの Ansible プレイブックを示します。
- App Service プランと Web アプリの配備先となるリソース グループ
- Linux 上の App Service で、Java 8 と Tomcat コンテナー ランタイムによる Web アプリ

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myfirstResourceGroup
    webapp_name: myfirstWebApp
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: myappplan
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```
上記のプレイブックを firstwebapp.yml として保存します。

プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook firstwebapp.yml
```

Ansible プレイブックの実行による出力では、Web アプリが正常に作成されていることを示しています。

```
TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ******************************
 [WARNING]: Azure API profile latest does not define an entry for
WebSiteManagementClient
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0   
```

## <a name="create-app-service-with-traffic-manager"></a>Traffic Manager での App Service の作成
[Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) を使用すると、Web クライアントからの要求を Azure App Service 内のアプリに振り分ける方法を制御できます。 App Service のエンドポイントが Azure Traffic Manager のプロファイルに追加されると、Azure Traffic Manager は App Service アプリの状態 (実行中、停止、または削除済み) を追跡して、トラフィックを受信する必要のあるエンドポイントを決定できるようになります。

App Service では、アプリは "[App Service プラン](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
)" で実行されます。 App Service プランでは、Web アプリを実行するための一連のコンピューティング リソースを定義します。 App Service プランと Web アプリを別のグループで管理できます。

このセクションでは、次のリソースを定義するサンプルの Ansible プレイブックを示します。
- App Service プランの配備先となるリソース グループ
- App Service プラン
- Web アプリの配備先となる二次的リソース グループ
- Linux 上の App Service で、Java 8 と Tomcat コンテナー ランタイムによる Web アプリ
- Traffic Manager プロファイル
- 作成した Web サイトを使用する Traffic Manager エンドポイント

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_resource_group: planResourceGroup
    app_name: myLinuxWebApp
    location: eastus
    linux_plan_name: myAppServicePlan
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ plan_resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ plan_resource_group }}"
      name: "{{ linux_plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ app_name }}"
        plan:
          resource_group: "{{ plan_resource_group }}"
          name: "{{ linux_plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ app_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"

```
上記のプレイブックを webapp.yml として保存するか、[プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml)します。

プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook webapp.yml
```

Ansible プレイブックの実行による出力では、App Service プラン、Web アプリ、Traffic Manager プロファイル、エンドポイントが正常に作成されていることを示しています。
```
TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Azure 上の Ansible](https://docs.microsoft.com/azure/ansible/)