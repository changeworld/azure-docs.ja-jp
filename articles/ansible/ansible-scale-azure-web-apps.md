---
title: Ansible を使用して Azure App Service Web アプリをスケーリングする
description: Linux 上の App Service で、Ansible を使用して、Java 8 と Tomcat コンテナー ランタイムを使った Web アプリを作成する方法について学習します。
ms.service: ansible
keywords: ansible, azure, devops, bash, プレイブック, Azure App Service, Web アプリ, スケール, Java
author: tomarcher
manager: jeconnoc
ms.author: kyliel
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 740ff6d6a636377f9d58a5231692c87f935ae6d2
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601867"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Ansible を使用して Azure App Service Web アプリをスケーリングする
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (または単に Web Apps) は、Web アプリケーション、REST API、モバイル バックエンドをホストします。 開発には、.NET、.NET Core、Java、Ruby、Node.js、PHP、Python のうち、お気に入りの言語をご利用いただけます。

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 この記事では、Ansible を使用して Azure App Service でアプリのスケールを変更する方法について説明します。

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service Web Apps** - Azure アプリ サービス Web アプリがまだない場合は、[Ansible を使用して Azure Web アプリを作成](ansible-create-configure-azure-web-apps.md)できます。

## <a name="scale-up-an-app-in-app-service"></a>App Service でのアプリのスケールアップ
スケールアップするには、アプリが属している App Service プランの価格レベルを変更します。 このセクションでは、次の操作を定義するサンプルの Ansible プレイブックを示します。
- 既存の App Service プランのファクトを取得する
- App Service プランを 3 つのワーカーを持つ S2 に更新します。

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App serivce plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

このプレイブックを *webapp_scaleup.yml* として保存します。

プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook webapp_scaleup.yml
```

プレイブックを実行すると、次の例のような出力が表示され、App Service プランが、ワーカーが 3 つある S2 に正常に更新されたことが示されます。
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App serivce plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Azure 上の Ansible](https://docs.microsoft.com/azure/ansible/)