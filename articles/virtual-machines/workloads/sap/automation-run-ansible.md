---
title: Ansible を実行して SAP システムを構成する
description: 環境を構成し、Azure 上の SAP デプロイ自動化フレームワークで Ansible プレイブックを使用して SAP をインストールします。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: a54870554cc1ab1a0c45d62115bbc54f11b41dac
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725211"
---
# <a name="get-started-ansible-configuration"></a>Ansible の構成の概要

Azure で[SAPデプロイ自動化フレームワークを使用する](automation-deployment-framework.md)場合は、[インフラストラクチャの自動デプロイ](automation-get-started.md)を実行することもできます。ただし、必要なオペレーティング システム構成を実行し、リポジトリに用意されている Ansible プレイブックを使用して SAP をインストールすることもできます。 これらのプレイブックは、`/sap-hana/deploy/ansible` フォルダー内のオートメーション フレームワーク リポジトリにあります。

| ファイル名                                   | 説明                                       |
| ------------------------------------------ | ------------------------------------------------- |
| `playbook_01_os_base_config.yaml`          | 基本オペレーティング システム (OS) の構成          |
| `playbook_02_os_sap_specific_config.yaml`  | SAP 固有の OS 構成                     |
| `playbook_03_bom_processing.yaml`          | SAP 部品表 (SAP BoM) 処理        |
| `playbook_04_00_00_hana_db_install`        | SAP HANA データベースのインストール                    |
| `playbook_05_00_00_sap_scs_install.yaml`   | SAP セントラル サービス (SCS) のインストール           |
| `playbook_05_01_sap_dbload.yaml`           | データベース ローダー                                   |
| `playbook_05_02_sap_pas_install.yaml`      | SAP プライマリ アプリケーション サーバー (PAS) のインストール |
| `playbook_05_03_sap_app_install.yaml`      | SAP アプリケーション サーバー のインストール               |
| `playbook_05_04_sap_web_install.yaml`      | SAP Web Dispatcher のインストール                   |
| `playbook_04_00_01_hana_hsr.yaml`          | SAP HANA HA の構成                         |

## <a name="prerequisites"></a>前提条件

Ansible プレイブックでは、ファイル `sap-parameters.yaml` と `SID_host.yaml` が現在のディレクトリに存在する必要があります。

### <a name="configuration-files"></a>構成ファイル

**sap-parameters.yaml** には、Ansible が SAP インフラストラクチャの構成に使用する情報が含まれています

```yaml
---

# bom_base_name is the name of the SAP Application Bill of Materials file
bom_base_name:                 S41909SPS03_v0006ms
# Set to true to instruct Ansible to update all the packages on the virtual machines
upgrade_packages:              false 

# TERRAFORM CREATED
sap_fqdn:                      sap.contoso.net                      
# kv_name is the name of the key vault containing the system credentials
kv_name:                       DEVWEEUSAP01user###
# secret_prefix is the prefix for the name of the secret stored in key vault
secret_prefix:                 DEV-WEEU-SAP01

# sap_sid is the application SID
sap_sid:                       X01
# scs_high_availability is a boolean flag indicating 
# if the SAP Central Services are deployed using high availability 
scs_high_availability:         false
# SCS Instance Number
scs_instance_number:           "00"
# scs_lb_ip is the SCS IP address of the load balancer in 
# from of the SAP Central Services virtual machines
scs_lb_ip:                     10.110.32.26
# ERS Instance Number
ers_instance_number:           "02"
# ecs_lb_ip is the ERS IP address of the load balancer in
# from of the SAP Central Services virtual machines
ers_lb_ip:                     

# sap_sid is the database SID
db_sid:                        XDB
# platform
platform:                      HANA

# db_high_availability is a boolean flag indicating if the 
# SAP database servers are deployed using high availability
db_high_availability:          false
# db_lb_ip is the IP address of the load balancer in from of the database virtual machines
db_lb_ip:                      10.110.96.13

disks:
  - { host: 'x01dxdb00l0538', LUN: 0, type: 'sap' }
  - { host: 'x01dxdb00l0538', LUN: 10, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 11, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 12, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 13, type: 'data' }
  - { host: 'x01dxdb00l0538', LUN: 20, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 21, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 22, type: 'log' }
  - { host: 'x01dxdb00l0538', LUN: 2, type: 'backup' }
  - { host: 'x01app00l538', LUN: 0, type: 'sap' }
  - { host: 'x01app01l538', LUN: 0, type: 'sap' }
  - { host: 'x01scs00l538', LUN: 0, type: 'sap' }

...
```

**`X01_hosts.yaml`** は、Ansible が SAP インフラストラクチャの構成に使用するインベントリ ファイルです。 'X01' はデプロイによって異なる場合があります。

```yaml
X01_DB:
  hosts:
    x01dxdb00l0538:
      ansible_host        : 10.110.96.12
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key
  vars:
    node_tier             : hana

X01_SCS:
  hosts:
    x01scs00l538:
      ansible_host        : 10.110.32.25
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key
  vars:
    node_tier             : scs

X01_ERS:
  hosts:
  vars:
    node_tier             : ers

X01_PAS:
  hosts:
    x01app00l538:
      ansible_host        : 10.110.32.24
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key 

  vars:
    node_tier             : pas

X01_APP:
  hosts:
    x01app01l538:
      ansible_host        : 10.110.32.15
      ansible_user        : azureadm
      ansible_connection  : ssh 
      connection_type     : key 

  vars:
    node_tier             : app

X01_WEB:
  hosts:
  vars:
    node_tier             : web

```

## <a name="run-a-playbook"></a>プレイブックを実行する

この手順を実行する前に、Azure 環境に [SAP ソフトウェアをダウンロード](automation-software.md)済みであることを確認してください。

プレイブックまたは複数のプレイブックを実行するには、次のように コマンド `ansible-playbook` を使用します。 次の例では、オペレーティング システム構成プレイブックを実行します。


```bash

sap_params_file=sap-parameters.yaml

if [[ ! -e "${sap_params_file}" ]]; then
        echo "Error: '${sap_params_file}' file not found!"
        exit 1
fi

# Extract the sap_sid from the sap_params_file, so that we can determine
# the inventory file name to use.
sap_sid="$(awk '$1 == "sap_sid:" {print $2}' ${sap_params_file})"

kv_name="$(awk '$1 == "kv_name:" {print $2}' ${sap_params_file})"

prefix="$(awk '$1 == "secret_prefix:" {print $2}' ${sap_params_file})"
password_secret_name=$prefix-sid-password

password_secret=$(az keyvault secret show --vault-name ${kv_name} --name ${password_secret_name} | jq -r .value)

export           ANSIBLE_PASSWORD=$password_secret
export           ANSIBLE_INVENTORY="${sap_sid}_hosts.yaml"
export           ANSIBLE_PRIVATE_KEY_FILE=sshkey
export           ANSIBLE_COLLECTIONS_PATHS=/opt/ansible/collections${ANSIBLE_COLLECTIONS_PATHS:+${ANSIBLE_COLLECTIONS_PATHS}}
export           ANSIBLE_REMOTE_USER=azureadm

# Ref: https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html
# Silence warnings about Python interpreter discovery
export           ANSIBLE_PYTHON_INTERPRETER=auto_silent

# Set of options that will be passed to the ansible-playbook command
playbook_options=(
        --inventory-file="${sap_sid}_hosts.yaml"
        --private-key=${ANSIBLE_PRIVATE_KEY_FILE}
        --extra-vars="_workspace_directory=`pwd`"
        --extra-vars="@${sap_params_file}"
        -e ansible_ssh_pass='{{ lookup("env", "ANSIBLE_PASSWORD") }}'
        "${@}"
)

ansible-playbook "${playbook_options[@]}" ~/Azure_SAP_Automated_Deployment/sap-hana/deploy/ansible/playbook_01_os_base_config.yaml

```

