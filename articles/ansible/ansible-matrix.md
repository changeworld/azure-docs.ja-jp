---
title: Azure の Ansible モジュールとバージョンのマトリックス | Microsoft Docs
description: Azure の Ansible モジュールとバージョンのマトリックス
keywords: Ansible, ロール, マトリックス, バージョン, Azure, DevOps
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74155993"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible モジュールとバージョンのマトリックス

Ansible には、Azure リソースのプロビジョニングと構成に使用する一連のモジュールが含まれています。 これらのリソースには、仮想マシン、スケール セット、ネットワーク サービス、コンテナー サービスがあります。 この記事では、Azure 用のさまざまな Ansible モジュールと、それらが搭載される Ansible のバージョンについて説明します。

## <a name="ansible-modules-for-azure"></a>Azure 用 Ansible モジュール

次のモジュールは、リモート ホストで直接実行することも、プレイブックを介して実行することもできます。  

これらのモジュールは、Ansible の公式リリースおよび次の Microsoft のプレイブック ロールから入手できます。

> [!NOTE]
> Ansible 2.9 以降では、すべての *_facts モジュールの名前を *_info に変更して、Ansible の名前付け規則に準拠するようにしました。 古いモジュールと名前が変更されたモジュールはリンクされているので、非推奨の警告が表示されるのではなく、すべてのモジュールが以前と同様に機能します。

| Azure 用 Ansible モジュール                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 2.9 | Ansible のロール | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Compute**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_availabilityset_info              | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_deployment                         | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_functionapp                        | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_functionapp_info                  | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_image                              | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_image_info                        | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_resource                           | -            | -                           | はい          | はい          | はい          | はい          | はい          |
| azure_rm_resource_info                     | -            | -                           | はい          | はい          | はい          | はい          | はい          |
| azure_rm_resourcegroup                      | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_resourcegroup_info                | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_virtualmachine                     | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachineextension            | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_virtualmachineimage_info          | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachinescaleset             | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachinescaleset_info       | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | はい          | はい          | はい          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | はい          | はい          | はい          |
| **ネットワーク**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | はい          | はい          | はい          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | はい          | はい          | はい          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | はい          | はい          | はい          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | はい          | はい          | はい          |
| azure_rm_dnsrecordset                       | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_dnsrecordset_info                 | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_dnszone                            | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_dnszone_info                      | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_loadbalancer                       | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_loadbalancer_info                 | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_networkinterface                   | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_networkinterface_info             | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_publicipaddress                    | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_publicipaddress_info              | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_route                              | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_routetable                         | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_routetable_info                   | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_securitygroup                      | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_subnet                             | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_virtualnetwork                     | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_virtualnetwork_info               | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | はい          | はい          | はい          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | はい          | はい          | はい          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | はい          | はい          |
| **Storage**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_manageddisk_info                  | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_storageaccount                     | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_storageaccount_info               | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_storageblob                        | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_webapp                             | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_webapp_info                       | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | はい          | はい          | はい          |
| **Containers**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | はい          | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_aks                                | -            | -                           | はい          | はい          | はい          | はい          | はい          |
| azure_rm_aks_info                          | -            | -                           | はい          | はい          | はい          | はい          | はい          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_containerinstance                  | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_containerregistry                  | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_containerregistry_info            | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | はい          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | はい          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | はい          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | はい          |
| **データベース**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mysqldatabase                      | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_mysqlserver                        | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_postgresqldatabase                 | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_postgresqlserver                   | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_sqldatabase                        | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | はい          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | はい          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | はい          | はい          | はい          | はい          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_sqlserver                          | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_sqlserver_info                    | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| **Analytics**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | はい          | はい          |
| **統合**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | はい          | はい          | はい          |
| **Security**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | はい          | はい          | はい          |
| azure_rm_keyvaultkey                        | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_keyvaultsecret                     | -            | はい                         | はい          | はい          | はい          | はい          | はい          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | はい          | はい          | はい          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | はい          | はい          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | はい          | はい          | はい          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_autoscale_info                    | -            | -                         | -          | はい          | はい          | はい          | はい          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | はい          | はい          | はい          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | はい          | はい          |
| **管理とガバナンス**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | はい        | はい          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | はい        | はい          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | はい        | はい          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | はい        | はい          |
| **モノのインターネット (IoT)**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | はい        | はい          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | はい        | はい          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | はい        | はい          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | はい        | はい          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | はい        | はい          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | はい        | はい          |

## <a name="introduction-to-playbook-role-for-azure"></a>Azure のプレイブック ロールの導入

[azure_preview_module プレイブック ロール](https://galaxy.ansible.com/Azure/azure_preview_modules/)は、最新の Azure モジュールをすべて含んでいます。 更新プログラムとバグ修正は、正式な Ansible リリースよりもタイムリーに行われます。 Azure リソースのプロビジョニングのために Ansible を使用する場合は、`azure_preview_module` プレイブック ロールをインストールすることをお勧めします。

`azure_preview_module` プレイブック ロールは 3 週間ごとにリリースされます。

## <a name="next-steps"></a>次のステップ

プレイブック ロールの詳細については、「[Creating Reusable Playbooks (再利用可能なプレイブックの作成)](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)」を参照してください。 