---
title: "SAP NetWeaver のための Azure Virtual Machines 高可用性のアーキテクチャとシナリオ | Microsoft Docs"
description: "SAP NetWeaver のための Azure Virtual Machines 高可用性 (HA) のアーキテクチャとシナリオ"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 867fe2835418a48e4e616d8137ba9fa4182c8fb7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2017
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>SAP NetWeaver のための高可用性のアーキテクチャとシナリオ

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="definition-of-terminologies"></a>用語の定義

**高可用性 (HA)** とは IT の中断を最小限に抑える一連のテクノロジに関連する用語で、**同じ**データ センター内の冗長性、フォールト トレランス、またはフェールオーバーで保護されたコンポーネントを通じて IT サービスのビジネス継続性を提供することで実現されます。 ここでは、Azure リージョンは 1 つです。

**ディザスター リカバリー (DR)** も IT サービスの中断を最小限に抑えることおよびその復旧を目的としていますが、数百キロメートル離れた場所にある**異なる**データ センターが対象です。 通常、同じ地理的リージョン内の、またはお客様が独自に確立したさまざまな Azure リージョンを対象とします。


## <a name="overview-of-high-availability"></a>高可用性の概要
Azure における SAP 高可用性の説明は、3 つの要素に分けることができます。

* **Azure インフラストラクチャの高可用性**。コンピューティング (VM)、ネットワーク、ストレージなどの高可用性、SAP アプリケーションの可用性を高めるための利点など。

* **Azure インフラストラクチャ VM Restart を利用した SAP アプリケーションの "高可用性" の実現**

  Windows Server フェールオーバー クラスタリング (WSFC) または Linux 上の Pacemaker などの機能を使用しない場合、Azure VM Restart を使用して、Azure の物理サーバー インフラストラクチャや基盤となる Azure プラットフォーム全体の計画的なダウンタイムまたは計画外のダウンタイムから SAP システムを保護します。


* **SAP アプリケーションの高可用性**

  SAP システム全体の高可用性を実現するには、たとえば次に示すような、SAP システムの重要なコンポーネントをすべて保護する必要があります。
  * **SAP アプリケーション サーバー**の冗長性、および
  * 次のような独自のコンポーネント (**単一障害点 (SPOF) など**)
    * **SAP (A)SCS インスタンス**、および
    *  **DBMS**。


Azure における SAP の高可用性は、オンプレミスの物理環境または仮想環境内の SAP 高可用性と比較した場合、いくつかの相違があります。 「[SAP NetWeaver High Availability and Business Continuity in Virtual Environments with VMware and Hyper-V on Microsoft Windows (Microsoft Windows で VMware や Hyper-V を使用した仮想環境での、SAP NetWeaver の高可用性とビジネス継続性)][sap-ha-bc-virtual-env-hyperv-vmware-white-paper]」のホワイト ペーパーでは、Windows の仮想環境での SAP 高可用性の標準的な構成について説明しています。

Windows 向けはありますが、Linux 向けの sapinst-integrated SAP-HA 構成は用意されていません。 Linux 向け SAP HA オンプレミスの詳細については、[高可用性パートナー情報][sap-ha-partner-information]をご覧ください。

## <a name="azure-infrastructure-high-availability"></a>Azure インフラストラクチャの高可用性

### <a name="single-instance-virtual-machine-sla"></a>単一インスタンス仮想マシンの SLA

現在 Premium Storage では、99.9% の単一 VM SLA があります。 単一 VM の可用性について理解すると、さまざまな [Azure サービス レベル アグリーメント][azure-sla]を利用できる製品を構築できます。

計算の基準は、1 か月あたり 30 日間または 43,200 分です。 したがって、0.05% のダウンタイムは 21.6 分に相当します。 通常どおり、異なるサービスの可用性は次の方法で乗算されます。

(可用性サービス #1/100) * (可用性サービス #2/100) * (可用性サービス #3/100) \*…

次のようになります。

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975、つまり 99.75% の全体的な可用性。

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>同じ可用性セット内の仮想マシンの複数のインスタンス
同じ**可用性セット**にデプロイされた 2 つ以上のインスタンスがあるすべての仮想マシンについては、仮想マシンが 99.95% 以上の時間において少なくとも 1 つのインスタンスに接続されることを保証します。

2 つ以上の VM が同じ可用性セットに含まれている場合、基盤となる Azure プラットフォームによって、可用性セット内の各仮想マシンに**更新ドメイン**と**障害ドメイン**が割り当てられます。

**障害ドメイン**では、共通の電源とネットワーク スイッチを共有していない、異なるハードウェアに VM がデプロイされることが保証されます。 サーバー、ネットワーク スイッチ、または電源の計画外のダウンタイム時には、1 つの VM のみが影響を受けます。

**更新ドメイン**では、Azure インフラストラクチャの計画的なメンテナンス時に、異なる VM が同時に再起動せず、一度に 1 つの VM のみが再起動することが保証されます。

詳細については、「[Azure での Windows 仮想マシンの可用性の管理][azure-virtual-machines-manage-availability]」をご覧ください。

可用性セットは、次の高可用性を実現するために使用されます。

* SAP アプリケーション サーバーの冗長性  

* SAP (A)SCS インスタンスや DBMS などの SPOF を保護する、2 つ以上のノード (VM など) が存在するクラスター

### <a name="virtual-machine-vm-planned-and-unplanned-maintenance"></a>仮想マシン (VM) の計画的なメンテナンスと計画外のメンテナンス

仮想マシンの可用性に影響を与える可能性のある Azure プラットフォーム イベントには、計画的なメンテナンスと計画外のメンテナンスの 2 種類があります。

* **計画的なメンテナンス** イベントは、基盤となる Azure プラットフォームに対して Microsoft が定期的に行う更新作業です。このメンテナンスは全体の信頼性、パフォーマンス、仮想マシンが実行されるプラットフォームのインフラストラクチャのセキュリティを向上させるために行われます。

* **計画外メンテナンス** イベントは、仮想マシンの基盤となっているハードウェアや物理インフラストラクチャに何らかの不具合が生じた場合に発生します。 こうした不具合には、ネットワーク障害、ローカル ディスク障害、またはその他のラック レベルでの障害が挙げられます。 そのような障害が検知されると、Azure プラットフォームは、仮想マシンをホストしている異常な物理サーバーから正常な物理サーバーへと、仮想マシンを自動的に移行します。 このような例が発生することはまれですが、この場合も仮想マシンの再起動が求められる場合があります。

詳細については、「[Azure での Windows 仮想マシンの可用性の管理][azure-virtual-machines-manage-availability]」をご覧ください。

### <a name="azure-storage-redundancy"></a>Azure Storage の冗長性
Microsoft Azure Storage アカウント内のデータは、持続性と高可用性を確保するために常にレプリケートされており、一時的なハードウェア障害が発生した場合でも Azure Storage SLA が満たされます。

Azure Storage は既定で 3 つのイメージのデータを保持するため、複数の Azure ディスク全体での RAID5 または RAID1 は必要ありません。

詳細については、「[Azure Storage のレプリケーション][azure-storage-redundancy]」をご覧ください。

### <a name="azure-managed-disks"></a>Azure Managed Disks
Managed Disks は Azure Resource Manager の新しいリソースの種類で、Azure Storage アカウントに格納されている VHD の代わりに使用できます。 Managed Disks は接続されている仮想マシンの可用性セットに自動的に配置され、仮想マシンと仮想マシンで実行されているサービスの可用性を向上させます。
詳細については、「[Azure Managed Disks の概要][azure-storage-managed-disks-overview]」をご覧ください。

仮想マシンのデプロイと管理が簡単なので、管理ディスクを使うことをお勧めします。
**SAP は現在、Premium Managed Disks のみをサポートします**。 詳細については、SAP Note [1928533] を参照してください。

## <a name="utilizing-azure-infrastructure-ha-to-achieve-sap-application-higher-availability"></a>Azure インフラストラクチャ HA を利用した SAP アプリケーションの "高" 可用性の実現

Windows Server フェールオーバー クラスタリング (WSFC) または Linux 上の Pacemaker (現在、SLES 12 以降についてのみサポートされています) などの機能を使用しない場合、Azure VM Restart を使用して、Azure の物理サーバー インフラストラクチャや基になる Azure プラットフォーム全体の計画したまたは計画外のダウンタイムから SAP システムを保護します。

この方法については、「[Utilizing Azure Infrastructure VM Restart to Achieve “Higher Availability” of SAP System (SAP システムで "さらに高い可用性" を実現するために Azure インフラストラクチャ VM Restart を利用する)][sap-higher-availability]」で詳しく説明されています。

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Azure IaaS での SAP アプリケーションの高可用性

SAP システム全体の高可用性を実現するには、たとえば次に示すような、SAP システムの重要なコンポーネントをすべて保護する必要があります。

* **SAP アプリケーション サーバー**の冗長性、および

* 次のような独自のコンポーネント (**単一障害点 (SPOF) など**)
  * **SAP (A)SCS インスタンス**、および
  *  **DBMS**。

次に、SAP システムの 3 つの重要なコンポーネントすべてにおいて、高可用性を実現する方法について詳しく説明します。

### <a name="high-availability-for-sap-application-servers"></a>SAP アプリケーション サーバーの高可用性

> この章は、次の両方に当てはまります。
>
> ![Windows][Logo_Windows] Windows および ![Linux][Logo_Linux] Linux
>

SAP アプリケーション サーバーおよびダイアログ インスタンスについては、通常、特定の高可用性ソリューションは不要です。 高可用性は冗長性によって実現し、Azure Virtual Machines のさまざまなインスタンスで複数のダイアログ インスタンスを構成します。 Azure Virtual Machines の 2 つのインスタンスに少なくとも 2 つの SAP アプリケーション インスタンスをインストールする必要があります。

![図 1: 高可用性の SAP アプリケーション サーバー][sap-ha-guide-figure-2000]

_**図 1:** 高可用性の SAP アプリケーション サーバー_

SAP アプリケーション サーバー インスタンスをホストするすべての仮想マシンを、同じ Azure 可用性セットに配置する必要があります。 Azure 可用性セットでは次のことが保証されます。

* すべての仮想マシンは、同じ**アップグレード ドメイン**の一部になります。 たとえば、アップグレード ドメインでは、計画的メンテナンス ダウンタイムの間に仮想マシンが同時に更新されないことが保証されます。
Azure スケール ユニット内のさまざまなアップグレード ドメインと障害ドメインに組み込まれた基本的な機能については、「[アップグレード ドメイン][planning-guide-3.2.2]」の章で既に紹介しました。

* すべての仮想マシンは、同じ**障害ドメイン**の一部になります。 たとえば、障害ドメインでは、単一障害点によってすべての仮想マシンの可用性が影響を受けないように仮想マシンがデプロイされることが保証されます。

Azure スケール ユニット内の Azure 可用性セットが使用できる障害ドメインとアップグレード ドメインの数に制限はありません。 これは、多数の VM を 1 つの可用性セットにデプロイした場合、遅かれ早かれ 1 つ以上の VM が同じ障害またはアップグレード ドメインに配置されることになることを意味します。

専用の VM 内に少数の SAP アプリケーション サーバー インスタンスをデプロイし、5 つのアップグレード ドメインがあるとした場合、最終的には次のような状態になります。 可用性セット内の障害ドメインとアップグレード ドメインの実際の最大数は、後で変わる可能性があります。

![図 2: Azure 可用性セット内の SAP アプリケーション サーバーの HA][planning-guide-figure-3000]
_**図 2:** Azure 可用性セット内の SAP アプリケーション サーバーの HA_ 詳細については、[仮想マシンの可用性の管理][virtual-machines-manage-availability]に関する記事をご覧ください。


Azure 可用性セットについては、「SAP NetWeaver のための Azure Virtual Machines の計画と実装」のドキュメントの「[Azure の可用性セット][planning-guide-3.2.3]」の章で説明されています。


**非管理対象ディスクのみ:** Azure ストレージ アカウントは潜在的な単一障害点であるため、少なくとも 2 つの仮想マシンが分散された Azure ストレージ アカウントを少なくとも 2 つ用意することが重要です。 理想的な設定としては、SAP ダイアログ インスタンスを実行する各仮想マシンのディスクを、異なるストレージ アカウントにデプロイします。

> [!IMPORTANT]
>
> Managed Disks は接続されている仮想マシンの可用性セットに自動的に配置され、仮想マシンと仮想マシンで実行されているサービスの可用性を向上させるため、SAP HA のインストールに Azure Managed Disks を使用することを強くお勧めします。  
>


### <a name="high-availability-architecture-for-the-sap-ascs-instance"></a>SAP (A)SCS インスタンスの高可用性のアーキテクチャ

### <a name="high-availability-architecture-for-the-sap-ascs-instance-on-windows"></a>Windows での SAP (A)SCS インスタンスの高可用性のアーキテクチャ


> ![Windows][Logo_Windows] Windows
>

SAP (A)SCS インスタンスを保護するために、**Windows Server フェールオーバー クラスタリング** (**WSFC**) のソリューションを使用できます。 ソリューションには、次の 2 つの種類があります。

* **クラスター化された共有ディスク**を使用した SAP (A)SCS インスタンスのクラスタリング

   クラスター化された共有ディスクを使用した HA アーキテクチャの詳細については、[クラスター化された共有ディスクを使用した、Windows フェールオーバー クラスターでの SAP (A)SCS インスタンスのクラスタリング][sap-high-availability-guide-wsfc-shared-disk]に関する記事をご覧ください。   

* **ファイル共有**を使用した SAP (A)SCS インスタンスのクラスタリング

  ファイル共有を使用した HA アーキテクチャの詳細については、[ファイル共有を使用した、Windows フェールオーバー クラスターでの SAP (A)SCS インスタンスのクラスタリング][sap-high-availability-guide-wsfc-file-share]に関する記事をご覧ください。

### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Linux での SAP (A)SCS インスタンスの高可用性


> ![Linux][Logo_Linux] Linux
>

SUSE Linux Enterprise Server クラスター フレームワークを使用した SAP (A)SCS インスタンスのクラスタリングの詳細については、「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性][sap-suse-ascs-ha]」をご覧ください。

### <a name="sap-netweaver-multi-sid-configuration-for-clustered-sap-ascs-instance"></a>クラスター化された SAP (A)SCS インスタンスのための SAP NetWeaver マルチ SID の構成

> ![Windows][Logo_Windows] Windows
>
>現時点では、マルチ SID は、**Windows Server フェールオーバー クラスター (WSFC)** でのみサポートされています。 マルチ SID では、**ファイル共有**と**共有ディスク**の使用がサポートされています。
>

マルチ SID の HA アーキテクチャの詳細については、次のアーキテクチャ ドキュメントをご覧ください。

* [Windows Server フェールオーバー クラスタリングとファイル共有を使用する SAP (A)SCS インスタンス マルチ SID の高可用性][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Windows Server フェールオーバー クラスタリングと共有ディスクを使用する SAP (A)SCS インスタンス マルチ SID の高可用性][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>高可用性の DBMS インスタンス

DBMS は、SAP システムでの単一接続点でもあります。 高可用性ソリューションを使用して保護する必要があります。 次の図では、Azure の SQL Server Always On 高可用性ソリューションと、Windows Server フェールオーバー クラスタリングおよび Azure 内部ロード バランサーを示します。 SQL Server Always On は、独自の DBMS レプリケーションを使用して、DBMS のデータとログ ファイルをレプリケートします。 この場合、クラスター共有ディスクは必要なく、セットアップ全体が簡単になります。

![図 3: 高可用性の SAP DBMS と SQL Server Always On の例][sap-ha-guide-figure-2003]

_**図 3:** 高可用性の SAP DBMS と SQL Server Always On の例_

Azure Resource Manager デプロイメント モデルを使用した Azure での **SQL Server DBMS** のクラスタリングの詳細については、次の記事をご覧ください。

* [Azure VM での AlwaysOn 可用性グループの手動構成 - Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Azure の Always On 可用性グループに使用する Azure 内部ロード バランサーの構成][virtual-machines-windows-portal-sql-alwayson-int-listener]

Azure Resource Manager デプロイメント モデルを使用した Azure での **SAP HANA DBMS** のクラスタリングの詳細については、次の記事をご覧ください。

* [Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]
