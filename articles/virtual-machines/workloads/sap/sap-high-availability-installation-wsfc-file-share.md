---
title: "Windows フェールオーバー クラスターと SAP (A)SCS インスタンスのファイル共有を使用した、Azure への SAP NetWeaver HA のインストール | Microsoft Docs"
description: "Windows フェールオーバー クラスターと SAP (A)SCS インスタンスのファイル共有を使用した、SAP NetWeaver HA のインストール"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec7888cfb9b0d288b5b25f580c852ee32306684c
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2017
---
# <a name="sap-netweaver-ha-installation-on-windows-failover-cluster-and-file-share-for-sap-ascs-instance-on-azure"></a>Windows フェールオーバー クラスターと SAP (A)SCS インスタンスのファイル共有を使用した、Azure への SAP NetWeaver HA のインストール

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

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


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

このドキュメントでは、SAP (A)SCS インスタンスをクラスター化するためのオプションとして **Windows フェールオーバー クラスター (WSFC)** と**スケール アウト ファイル共有** を使用し、Azure に高可用性 SAP システムをインストールして構成する方法を説明します。

## <a name="prerequisites"></a>前提条件

インストールを開始する前に、必ず次のドキュメントを確認してください。

* [アーキテクチャ ガイド - **ファイル共有**を使用した **Windows フェールオーバー クラスター**での SAP (A)SCS インスタンスのクラスター化][sap-high-availability-guide-wsfc-file-share]

* [**Windows フェールオーバー クラスター**と SAP (A)SCS インスタンスの**ファイル共有**を使用した SAP HA 向けの Azure インフラストラクチャの準備][sap-high-availability-infrastructure-wsfc-file-share]


次の SAP の実行可能ファイルまたは dll が必要です。
* SAP **Software Provisioning Manager** (**SWPM**) インストール ツール バージョン **SPS21 (またはそれ以降)**。
* 新しい SAP クラスター リソース DLL を使用した、**最新の NTCLUST.SAR** アーカイブのダウンロード。 新しい SAP クラスター dll は、Windows Server フェールオーバー クラスターでのファイル共有を使用した SAP (A)SCS の高可用性をサポートしています。

  新しい SAP クラスター リソース DLL の詳細については、ブログ「[New SAP cluster resource DLL is available! (新しい SAP クラスター リソース DLL が使用可能です!)][sap-blog-new-sap-cluster-resource-dll]」をご覧ください。

DBMS の設定は、使用する DBMS システムによって異なるため、ここでは説明しません。 ただし、さまざまな DBMS ベンダーが Azure でサポートする機能を使用して、DBMS に関する高可用性の問題に対処していることを想定しています。 たとえば、SQL Server の Always On またはデータベース ミラーリング、Oracle データベースの Oracle Data Guard があります。 この記事のシナリオでは、DBMS の保護は強化しませんでした。

異なる DBMS サービスが Azure のこの種のクラスター化された SAP ASCS/SCS 構成と対話する際に、特別な考慮事項はありません。

> [!NOTE]
> SAP NetWeaver ABAP システム、Java システム、ABAP + Java システムのインストール手順はほとんど同じです。 最も重要な違いは、SAP ABAP システムでは 1 つの ASCS インスタンスを使用することです。 SAP Java システムは 1 つの SCS インスタンスを使用します。 SAP ABAP + Java システムでは、1 つの ASCS インスタンスと、同じ Microsoft フェールオーバー クラスター グループで実行する 1 つの SCS インスタンスを使用します。 各 SAP NetWeaver インストール スタックのインストールの違いについて明確に説明します。 他のすべての部分は同じであると考えることができます。  
>
>

## <a name="install-ascs-instance-on-ascs-cluster"></a>(A)SCS クラスターへの (A)SCS インスタンスのインストール

> [!IMPORTANT]
>
>現在、SAP インストール ツールの Software Provisioning Manager (SWPM) では、ファイル共有構成での HA 設定はサポートされていません。 そのため、SAP システムをインストールするには、手動によるインストール方法を採用する必要があります。たとえば、SAP (A)SCS インスタンスをインストールしてクラスター化し、別の SAP GLOBALHOST を構成するなどです。  
>
>DBMS インスタンスと SAP アプリケーション サーバーをインストール (およびクラスター化) するための、その他のインストール手順に変更はありません。
>

### <a name="install-ascs-instance-on-local-drive"></a>ローカル ドライブへの (A)SCS インスタンスのインストール

SAP (A)SCS インスタンスを (A)SCS クラスターの **両方**のノードにインストールします。 次にそれを**ローカル** ドライブにインストールします。 たとえば、ここではローカル ドライブを C:\\ にします。 他のローカル ドライブでもかまいません。  

インストールするには、SAP インストール ツールの SWPM で次のパスに移動します。

&lt;製品&gt; -> &lt;DBMS&gt; -> [Installation]\(インストール\) -> [Application Server ABAP (or Java)]\(アプリケーション サーバー ABAP (または Java)\) -> [Distributed System]\(分散システム\) -> [(A)SCS Instance]\((A)SCS インスタンス\)

> [!IMPORTANT]
>現在、SAP インストール ツールの SWPM ではファイル共有がサポートされていないため、次のインストール パスは**使用できません**。
>
>&lt;製品&gt; -> &lt;DBMS&gt; -> [Installation]\(インストール\) -> [Application Server ABAP (or Java)]\(アプリケーション サーバー ABAP (または Java)\) -> [High-Availability  System]\(高可用性システム\) -> …
>

### <a name="remove-sapmnt-and-create-saploc-file-share"></a>SAPMNT の削除と SAPLOC ファイル共有の作成

SWMP によって C:\\usr\\sap フォルダに SAPMNT ローカル共有が作成されました。

**両方**の (A)SCS クラスター ノードで SAPMNT ファイル共有を削除します。

次の PowerShell スクリプトを実行します。

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

SAPLOC 共有が存在しない場合は、BOTH ASCS クラスター ノードに作成します。

次の PowerShell スクリプトを実行します。

```PowerShell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-sap-global-host-on-sofs-cluster"></a>SOFS クラスターでの SAP GLOBAL HOST の準備

このステップでは、SOFS クラスターに次のボリュームとファイル共有を作成します。

* SOFS クラスター共有ボリューム (CSV) 上の C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS\ 構造に SAP GLOBALHOST ファイル

* SAPMNT ファイル共有を作成する

* 次のものをフル コントロールして、SAPMNT ファイル共有およびフォルダにセキュリティを設定する:
    * **&lt;DOMAIN&gt;\SAP_&lt;SID&gt;_GlobalAdmin** ユーザー グループ
    * SAP (A)SCS クラスター ノード コンピューター **オブジェクト &lt;DOMAIN&gt;\ClusterNode1$ および &lt;DOMAIN&gt;\ClusterNode2$**

「**SAP Prerequisites for SOFS in Azure  - ADD LINK (Azure における SOFS 向けの SAP 前提条件 - リンクの追加)**」の章で定義したように、Mirror の回復性を使用して CSV ボリュームを作成するには、SOFS クラスター ノードのいずれかで次の PowerShell コマンドレットを実行します。


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
SAPMNT を作成してフォルダーと共有セキュリティを設定するには、SOFS クラスター ノードのいずれかで次の PowerShell スクリプトを実行します。

```PowerShell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP (A)SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP (A)SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set files & folder security
$Acl = Get-Acl $UsrSAPFolder

# Add file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascs-instances-and-sap-services"></a>(A)SCS インスタンスと SAP サービスの停止

次の手順を実行します。
* 両方の (A)SCS クラスター ノードで、SAP (A)SCS インスタンスを停止する
* 両方のクラスター ノードで、SAP (A)SCS Windows サービスの **SAP&lt;SID&gt;_&lt;インスタンス番号&gt;** を停止する

## <a name="move-sys-folder-to-sofs-cluster"></a>\SYS\... フォルダーの SOFS クラスターへの移動

次の手順を実行します。
* SYS フォルダー (例: C:\usr\sap\\&lt;SID&gt;\SYS) を (A)SCS クラスター ノードのいずれかから SOFS クラスター (例: C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS) にコピーする
* 両方の (A)SCS クラスター ノードから、C:\usr\sap\\&lt;SID&gt;\SYS フォルダーを削除する

## <a name="update-cluster-security-setting-on-sap-ascs-cluster"></a>SAP (A)SCS クラスターでのクラスター セキュリティ設定の更新

SAP (A)SCS クラスター ノードのいずれかで、次の PowerShell スクリプトを実行します。

```PowerShell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for <DOMAIn>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascs-instance"></a>クラスター化された SAP (A)SCS インスタンスの仮想ホスト名の作成

「[Create a virtual host name for the clustered SAP (A)SCS instance (クラスター化された SAP (A)SCS インスタンスの仮想ホスト名の作成)][sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]」の章で説明したように、SAP (A)SCS クラスター ネットワーク名 (例: **pr1-ascs [10.0.6.7]**) を作成します。

## <a name="update-default-and-sap-ascs-instance-profile"></a>既定値と SAP (A)SCS インスタンス プロファイルの更新

次のホスト名を使用するには、既定値と SAP (A)SCS インスタンス プロファイル &lt;SID&gt;_(A)SCS<Nr>_  <Host>を更新する必要があります。

* 新しい SAP (A)SCS 仮想ホスト名

* 新しい SAP GLOBAL ホスト名


| 古い値 |  |
| --- | --- |
| SAP (A)SCS ホスト名 = SAP グローバル ホスト | ascs-1 |
| SAP (A)SCS インスタンス プロファイル名 | PR1_ASCS00_ascs-1 |

| 新しい値 |  |
| --- | --- |
| SAP (A)SCS ホスト名 | **pr1-ascs** |
| SAP GLOBAL ホスト | **sapglobal** |
| SAP (A)SCS インスタンス プロファイル名 | PR1\_ASCS00\_**pr1-ascs** |

### <a name="update-sap-default-profile"></a>SAP DEFAULT プロファイルの更新


| パラメーター名 | パラメーター値 |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **pr1-ascs** |
| enque/serverhost | **pr1-ascs** |

### <a name="update-sap-ascs-instance-profile"></a>SAP (A)SCS インスタンス プロファイルの更新

| パラメーター名 | パラメーター値 |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\\**sapglobal**\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE)\PR1\_ASCS00_ pr1-ascs |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | **Start**_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | **pr1-ascs** |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | **Start**_Program_03 = local$(_EN) pf=$(_PF) |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

> [!IMPORTANT]
>PowerShell コマンドレットの **Update-SAPASCSSCSProfile** を使用して、プロファイルの更新を自動化できます
>
>PowerShell コマンドレットでは、SAP ABAP ASCS と SAP Java SCS インスタンスの両方がサポートされています。
>

**SAPScripts.ps1** をローカル ドライブの C:\tmp にコピーして、次の PowerShell コマンドレットを実行します。

```PowerShell
Import-Module C:\tmp\SAPScripts.ps1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![図 1: SAPScripts.ps1 の出力][sap-ha-guide-figure-8012]

_**図 1:** SAPScripts.ps1 の出力_

## <a name="update-ltsidgtadm-user-environment-variable"></a>&lt;sid&gt;adm ユーザー環境変数の更新

両方の (A)SCS クラスター ノードで、&lt;sid&gt;adm ユーザー環境変数の新しい GLOBALHOST UNC パスを更新します。
&lt;sid&gt;adm ユーザーとしてログオンし、Regedit.exe ツールを起動します。
**[HKEY_CURRENT_USER]** -> **[Environment]\(環境\)** に移動し、変数を新しい値に更新します。

| 変数 | 値 |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **pr1-ascs** |


## <a name="install-new-saprcdll"></a>新しい SAPRC.DLL のインストール

ファイル共有をサポートする SAP クラスター リソースの新しいバージョンをインストールする必要があります。

SAP サービス マーケットプレイスから最新の **NTCLUST.SAR** パッケージをダウンロードします。

(A)SCS クラスター ノードのいずれかで NTCLUS.SAR を展開し、コマンド プロンプトで次のコマンドを実行して新しい saprc.dll をインストールします。

```
.\NTCLUST\insaprct.exe -yes -install
```

新しい saprc.dll は、両方の (A)SCS クラスター ノードにインストールされます。

詳細については、「[SAP Note 1596496 - How to update SAP Resource Type DLLs for Cluster Resource Monitor (SAP ノート 1596496 - クラスタ リソース モニターの SAP リソース タイプ DLL の更新方法)][1596496]」をご覧ください。

## <a name="create-sap-sid-cluster-group-network-name-and-ip"></a>SAP <SID> クラスター グループ、ネットワーク名、IP の作成

次のものを作成する必要があります。

* SAP &lt;SID&gt; クラスター グループ
* <(A)SCS ネットワーク名>
* 対応する IP アドレス

次の PowerShell コマンドレットを実行します。

```PowerShell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create SAP ASCS instance Virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create SAP ASCS Virtual IP Address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set static IP Address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set Network DNS Name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start SAP <SID> Cluster Group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-sap-start-service-on-both-nodes"></a>両方のノードでの SAP START サービスの登録

新しいプロファイルとプロファイル パスがポイントされるようにするには、SAP (A)SCS sapstart サービスを再登録する必要があります。

両方の (A)SCS クラスター ノードで、これを実行する必要があります。

管理者特権でのコマンド プロンプトで、次のコマンドを実行します。

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![図 2: SAP サービスの再インストール][sap-ha-guide-figure-8013]

_**図 2:** SAP サービスの再インストール_

パラメーターが正しいことを確認し、スタートアップの種類として **[Manual]\(手動\)** を選択します。

## <a name="stop-ascs-service"></a>(A)SCS サービスの停止

両方の (A)SCS クラスター ノードで、SAP (A)SCS サービスの **SAP&lt;SID&gt;_&lt;インスタンス番号&gt;** を停止します。

## <a name="create-new-sap-service-and-sap-instance-resources"></a>新しい SAP サービスと SAP インスタンス リソースの作成

SAP &lt;SID&gt; クラスター グループのリソースの作成を完了する必要があります。たとえば、次のリソースを作成する必要があります。

* **SAP &lt;SID&gt; &lt;インスタンス番号&gt; サービス**と
* **SAP &lt;SID&gt; &lt;インスタンス番号&gt; インスタンス**

次の PowerShell コマンドレットを実行します。

```PowerShell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP Instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP Instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>プローブ ポートの追加

このステップでは、PowerShell を使用して、SAP クラスター リソース SAP-SID-IP プローブ ポートを構成します。 この構成は、[こちら][sap-high-availability-installation-wsfc-shared-disk-add-probe-port]で説明したように、SAP ASCS/SCS クラスター ノードのいずれかで実行します。

## <a name="install-ers-instance-on-both-cluster-nodes"></a>両方のクラスター ノードでの ERS インスタンスのインストール

次のステップでは、(A)SCS クラスターの両方のノードに ERS (Enqueue Replication Server) インスタンスをインストールする必要があります。
インストール オプションは SWPM メニューにあります。

&lt;製品&gt; -> &lt;DBMS&gt; -> [Installation]\(インストール\) -> [Additional SAP System instances]\(追加の SAP システム インスタンス\) -> **[Enqueue Replication Server Instance]\(Enqueue Replication Server インスタンス\)**

## <a name="install-dbms-instance-and-sap-application-servers"></a>DBMS インスタンスと SAP アプリケーション サーバーのインストール

次のものをインストールして、SAP システムのインストールを完了します。
* DBMS インスタンス
* プライマリの SAP アプリケーション サーバー
* 追加の SAP アプリケーション サーバー

## <a name="next-steps"></a>次のステップ

* [フェールオーバー クラスターでの共有ディスクなしでの (A)SCS インスタンスのインストール - HA ファイル共有向け公式 SAP ガイドライン][sap-official-ha-file-share-document]:

* [Windows Server 2016 での記憶域スペース ダイレクト][s2d-in-win-2016]

* [アプリケーション データに対応するスケールアウト ファイル サーバーの概要][sofs-overview]

* [Windows Server 2016 での記憶域の新機能][new-in-win-2016-storage]
