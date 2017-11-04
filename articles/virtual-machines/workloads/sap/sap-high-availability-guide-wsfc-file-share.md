---
title: "Azure 上のファイル共有を使用した Windows フェールオーバー クラスターでの SAP (A)SCS インスタンスのクラスタリング | Microsoft Docs"
description: "ファイル共有を使用した Windows フェールオーバー クラスターでの SAP (A)SCS インスタンスのクラスター化"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 472c3f35e2ae32550be62826407689f93101041f
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#ds-series

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

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

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

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


# <a name="clustering-sap-ascs-instance-on-windows-failover-cluster-using-file-share-on-azure"></a>Azure 上のファイル共有を使用した Windows フェールオーバー クラスターでの SAP (A)SCS インスタンスのクラスター化

> ![Windows][Logo_Windows] Windows
>

Windows Server フェールオーバー クラスタリングは、Windows での高可用性の SAP ASCS/SCS インストールと DBMS の基盤です。

フェールオーバー クラスターとは、アプリケーションとサービスの可用性を高めるために連携する、1 + n 台の独立したサーバー (ノード) のグループです。 ノード障害が発生した場合、Windows Server フェールオーバー クラスタリングは、アプリケーションとサービスを提供するクラスターを正常な状態で維持するうえで許容できるエラーの数を計算します。 フェールオーバー クラスタリングを実現するために、さまざまなクォーラム モードを選択できます。

## <a name="prerequisite"></a>前提条件
このドキュメントで開始する前に、以下のドキュメントを確認してください。

* [SAP NetWeaver のための Azure Virtual Machines 高可用性のアーキテクチャとシナリオ][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
>ファイル共有がある SAP (A)SCS インスタンスのクラスタリングは、**SAP Kernel 7.49 以降**を含む、**SAP NetWeaver 7.40 以降** の製品でサポートされています。
>


## <a name="windows-server-failover-clustering-in-azure"></a>Azure での Windows Server フェールオーバー クラスタリング

ベア メタル デプロイやプライベート クラウド デプロイと比較すると、Azure Virtual Machines では Windows Server フェールオーバー クラスタリングを構成するための追加手順が必要となります。 クラスターを構築するときは、SAP ASCS/SCS インスタンスに複数の IP アドレスと仮想ホスト名を設定する必要があります。

### <a name="name-resolution-in-azure-and-cluster-virtual-host-name"></a>Azure での名前解決とクラスターの仮想ホスト名

Azure クラウド プラットフォームには、フローティング IP アドレスのような仮想 IP アドレスを構成するオプションは用意されていません。 クラウド内のクラスター リソースに到達するために仮想 IP アドレスを設定する別のソリューションが必要となります。 Azure Load Balancer サービスには**内部ロード バランサー**があります。 内部ロード バランサーでは、クライアントはクラスターの仮想 IP アドレスを使用してクラスターにアクセスします。 クラスター ノードを含むリソース グループに、内部ロード バランサーをデプロイする必要があります。 その後、内部ロード バランサーのプローブ ポートで必要なすべてのポート フォワーディング規則を構成します。 クライアントは仮想ホスト名を使用して接続できます。 DNS サーバーがクラスター IP アドレスを解決し、内部ロード バランサーがクラスターのアクティブ ノードへのポート フォワーディングを処理します。

![図 1: 共有ディスクを使用しない Azure の Windows Server フェールオーバー クラスタリング構成][sap-ha-guide-figure-1001]

_**図 1:** 共有ディスクを使用しない Azure の Windows Server フェールオーバー クラスタリング構成_

## <a name="sap-ascs-ha-with-file-share"></a>ファイル共有を使用する SAP (A)SCS HA

SAP によって、共有ディスクをクラスターする方法に代わり、Windows フェールオーバー クラスター上で SAP (A)SCS インスタンスをクラスターするという新しい方法が開発されました。

ここでは、**SAP GLOBAL HOST ファイル**をデプロイするオプションである **SMB ファイル共有**が使用されます。

> [!NOTE]
>SMB ファイル共有は、SAP (A)SCS インスタンスのクラスタリング用に共有ディスクをクラスターする追加オプションです。  
>

このアーキテクチャに固有の新機能は次のとおりです。

* **SAP セントラル サービス (独自のファイル構造、およびメッセージとエンキュー プロセスを含む) は、SAP GLOBAL HOST ファイルから分離される**
* **SAP セントラル サービスが SAP (A)SCS インスタンスで実行される**
* SAP (A)SCS インスタンスがクラスターされ、仮想ホスト名 **<(A)SCSVirtualHostName>** を使用してアクセスできる
* SAP GLOBAL ファイルは SMB ファイル共有に配置され、<SAPGLOBALHost>ホスト名 \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\... を使用してアクセスされる
* SAP (A)SCS インスタンスは両方のクラスター ノード上のローカル ディスクにインストールされる
* **<(A)SCSVirtualHostName>** ネットワーク名は **<SAPGLOBALHost>** とは異なる

![図 2: SMB ファイル共有を使用した新しい SAP (A)SCS HA のアーキテクチャ][sap-ha-guide-figure-8004]

_**図 2:** SMB ファイル共有を使用した新しい SAP (A)SCS HA のアーキテクチャ_

SMB ファイル共有の前提条件:

* SMB 3.0 (またはそれ以降) のプロトコル
* **AD ユーザー グループ**と**コンピューター オブジェクト コンピューターcomputer$** に Active Directory (AD) のアクセス制御リスト (ACL) を設定する機能
* ファイル共有では、HA が有効になっている必要があります。
    * ファイルを格納するために使用するディスクを単一障害点に指定することはできません
    * サーバー/VM のダウンタイムによって、ファイル共有のダウンタイムが発生していないことを確認する必要があります

これで、 **SAP &lt;SID&gt;** クラスターの役割には、クラスター共有ディスクまたは汎用ファイル共有クラスター リソースが含まれません。


![図 3: ファイル共有を使用した SAP <SID> クラスター ロール リソース][sap-ha-guide-figure-8005]

_**図 3:** ファイル共有を使用した **SAP &lt;SID&gt;** クラスター ロール リソース_


## <a name="scale-out-file-share-sofs-with-storage-spaces-direct-s2d-on-azure-as-sapmnt-file-share"></a>Azure での SAPMNT ファイル共有としての記憶域スペース ダイレクト (S2D) を使用したスケールアウト ファイル共有 (SOFS)

SOFS を使用して、SAP GLOBAL Host ファイルをホストおよび保護し、高可用性の SAPMNT ファイル共有サービスを提供することができます。

![図 4: SAP GLOBAL Host ファイルを保護するために使用される SOFS ファイル共有][sap-ha-guide-figure-8006]

_**図 4:** SAP GLOBAL Host ファイルを保護するために使用される SOFS ファイル共有_

> [!IMPORTANT]
>SOFS ファイル共有は、オンプレミス環境と同様に、Microsoft Azure クラウドでも完全にサポートされています。
>

**SOFS** は、高可用性および水平方向にスケーラブルな SAPMNT ファイル共有を提供します。

**記憶域スペース ダイレクト (S2D)** は、SOFS では**共有ディスク**として使用され、ローカル記憶域を含むサーバーを使用して、高可用性とスケーラブルな記憶域を構築できます。 したがって、SAP GLOBALHOST ファイルなど、SOFS で使用される共有記憶域は、単一障害点 (SPOF) にはなりません。

> [!IMPORTANT]
>ディザスター リカバリーをセットアップする場合は、Azure での高可用性ファイル共有のソリューションとして SOFS をお勧めします。
>

### <a name="sap-prerequisites-for-sofs-in-azure"></a>Azure での SOFS の SAP の前提条件

SOFS では次のものが必要です。

* SOFS 用の少なくとも 2 つのクラスター ノード

* 各ノードには、少なくとも 2 つのローカル ディスクが必要です

* パフォーマンス上の理由で、**ミラーリング回復性**を使用する必要があります。
    * 2 個のクラスター ノードを含む SOFS 用の **2 方向**のミラーリング
    * 3 個 (またはそれ以上) のクラスター ノードを含む SOFS 用の **3 方向**のミラーリング


* **SOFS には 3 個 (またはそれ以上) のクラスター ノードで 3 方向ミラーリングを構成することをお勧めします**。
このセットアップにより、2 つのクラスター ノードと 2 方向のミラーリングを使用した SOFS のセットアップよりも、拡張性や回復性が向上します。

* **Azure Premium ディスク**を使用する必要があります

* **Azure Managed Disks** を使用することを**お勧めします**

* 新しい **Resilient File System (ReFS)** でボリュームをフォーマットすることを**お勧めします**
    * [SAP ノート 1869038 - ReFs ファイルシステムの SAP サポート][1869038]
    * 記憶域スペース ダイレクトでのボリュームの計画の「[ファイル システムの選択する][planning-volumes-s2d-choosing-filesystem]」の章を参照してください。
    * この [MS **KB4025334** 累積的な更新プログラム][kb4025334]をインストールしてください。


* **DS シリーズ**または **DSv2-Series** Azure VM のサイズを使用できます

* 記憶域スペース ダイレクトのディスク同期に必要な内部 VM ネットワーク パフォーマンスを得るには、少なくとも **「高」ネットワーク帯域幅**を含む VM タイプを使用する必要があります。
詳細については、[DSv2 シリーズ][dv2-series]と [DS シリーズ][ds-series]の仕様を参照してください。

* **記憶域プールの一部の容量を未割り振りのままにして予約する**ことを**お勧めします**。 これにより、ドライブが失敗した場合に「インプレース」を修復するためのスペースのボリュームが提供され、データの安全性とパフォーマンスが向上します。

 詳細については、「[ボリュームのサイズの選択][choosing-the-size-of-volumes-s2d]」を参照してください


* SOFS Azure VM は**独自の Azure 可用性セット** にデプロイする必要があります

* これは、SAP (A)SCS インスタンスの <(A)SCSVirtualHostname> または DBMS に対して実行されるため、Azure Internal Load Balancer を SOFS のファイル共有ネットワーク名 (たとえば、<SAPGlobalHostName>) に構成する必要はありません。 SOFS はすべてのクラスター ノードで負荷をスケールアウトするため、<SAPGlobalHostName> はすべてのクラスター ノードのローカル IP を使用します。


> [!IMPORTANT]
>SAPGLOBALHOST を指す SAPMNT ファイル共有は変更できません。 SAP は sapmnt 以外の共有名をサポートしていません。
>[SAP Note 2492395 - 共有名 sapmnt は変更できますか][2492395]

### <a name="configuring-sap-ascs-instances-and-sofs-in-two-clusters"></a>2 つのクラスターで SAP (A)SCS インスタンスと SOFS を構成する

SAP (A)SCS インスタンスを、そのインスタンスの SAP <SID>クラスターの 役割とともに 1 つのクラスターにデプロイすることができます。 SOFS ファイル共有は、別のクラスター の役割とともに別のクラスターで構成されます。

> [!IMPORTANT]
>このシナリオでは、SAP (A)SCS インスタンスは、UNC パス \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\. を使用して SAP GLOBALHost にアクセスするように構成されています。
>

![図 5: 2 つのクラスターにデプロイされた SAP (A)SCS インスタンスと SOFS][sap-ha-guide-figure-8007]

_**図 5:** 2 つのクラスターにデプロイされた SAP (A)SCS インスタンスと SOFS_

> [!IMPORTANT]
>Azure クラウドでは、SAP と SOFS ファイル共有で使用される各クラスターは、それらのクラスター VM が基本的な Azure インフラストラクチャ全体に分散して配置されるようにするため、自身の Azure 可用性セットにデプロイされる必要があります。
>

## <a name="generic-file-share-with-sios-as-cluster-shared-disks"></a>SIOS を共有クラスター ディスクとして使用する汎用のファイル共有


> [!IMPORTANT]
>高可用性のファイル共有のソリューションには、SOFS を使用することをお勧めします。
>
>しかし、高可用性のファイル共有に**ディザスター リカバリー**も設定する場合は、クラスター共有 ディスクのテクノロジとして汎用のファイル共有と SISO を使用する必要があります。
>

汎用のファイル共有は、高可用性ファイル共有を実現するためのもう 1 つのオプションです。

ここでは、クラスター共有ディスクとして、サード パーティの SIOS ソリューションを使用できます。

## <a name="next-steps"></a>次のステップ

* [Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用した SAP HA 向けの Azure インフラストラクチャの準備][sap-high-availability-infrastructure-wsfc-file-share]

* [Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有上への SAP NetWeaver HA のインストール][sap-high-availability-installation-wsfc-shared-disk]

* [Azure での UPD 記憶域の 2 ノードの記憶域スペース ダイレクト スケール アウト ファイル サーバーのデプロイ][deploy-sofs-s2d-in-azure]

* [Windows Server 2016 での記憶域スペース ダイレクト][s2d-in-win-2016]

* [詳細: 記憶域スペース ダイレクトのボリューム][deep-dive-volumes-in-s2d]
