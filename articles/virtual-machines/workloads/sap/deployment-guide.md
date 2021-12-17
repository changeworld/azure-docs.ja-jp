---
title: SAP NetWeaver のための Azure Virtual Machines のデプロイ | Microsoft Docs
description: Azure の Linux 仮想マシンに SAP ソフトウェアをデプロイする方法について説明します。
services: virtual-machines-linux,virtual-machines-windows
author: MSSedusch
manager: juergent
tags: azure-resource-manager
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: 96fdec13ce028f3cac5f42c7092ca9b189923b9f
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577785"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>SAP NetWeaver のための Azure Virtual Machines のデプロイ

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:/cli/azure/install-classic-cli
[azure-cli-2]:/cli/azure/install-azure-cli
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (SAP のための Azure Virtual Machines DBMS のデプロイ)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (VM と VHD のキャッシング)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (ソフトウェア RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS デプロイの構造)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Azure VM の高可用性とディザスター リカバリー)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 以降)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 以前のリリース)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Azure Marketplace の SQL Server イメージを使用する)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Azure での一般的な SAP 用 SQL Server の概要)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS の詳細)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (ストレージの構成)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (バックアップと復元)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (バックアップと復元のパフォーマンスに関する考慮事項)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (その他)
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md (SAP のための Azure Virtual Machines のデプロイ)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP リソース)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (カスタム イメージを使用して VM をデプロイする)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (シナリオ 1: SAP 用 Azure Marketplace から VM をデプロイする)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (シナリオ 3: SAP を含む汎用化されていない Azure VHD を使用してオンプレミスから VM を移動する)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell コマンドレットのデプロイ)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (SAP 関連 PowerShell コマンドレットのダウンロードとインポート)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (オンプレミス ドメインへの VM の参加 [Windows のみ])
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Azure VM エージェントのダウンロード、インストール、有効化)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:vm-extension-for-sap.md (Azure Extension for SAP を構成する)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Azure PowerShell を使用して新しい Azure Extension for SAP を構成する)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Azure CLI を使用して新しい Azure Extension for SAP を構成する)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Azure Extension for SAP の適合性チェック)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (新しい Azure Extension for SAP の適合性チェック)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Azure Extension for SAP の構成の正常性チェック)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (新しい Azure Extension for SAP の構成の正常性チェック)


[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Azure Extension for SAP のトラブルシューティング)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (新しい Azure Extension for SAP のトラブルシューティング)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (VM 拡張機能を構成する)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (プロキシの構成)
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (確認とトラブルシューティング)

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (SAP のための Azure Virtual Machines の計画と実装)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (リソース)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Azure Virtual Machines で実行する SAP NetWeaver の高可用性とディザスター リカバリー)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP アプリケーション サーバーの高可用性)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (SAP インスタンスでの Autostart の使用)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (クラウドのみ - オンプレミスの顧客ネットワークとの依存関係を持たない仮想マシンの Azure へのデプロイ)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (クロスプレミス - オンプレミスのネットワークに完全に統合された 1 つまたは複数の SAP VM の Azure へのデプロイ)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure リージョン)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (障害ドメイン)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (アップグレード ドメイン)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure の可用性セット)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure の仮想マシンの概念)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (汎用化されていないディスクを使用してオンプレミスから Microsoft Azure に VM を移動する)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (顧客固有のイメージを使用する VM のデプロイ)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (オンプレミスから汎用でないディスクを使用する Azure に VM を移動する準備)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (SAP 用の顧客固有のイメージを使用する VM のデプロイの準備)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Azure 用の VM と SAP の準備)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure ディスクと Azure イメージの違い)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (オンプレミスから Azure への VHD のアップロード)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Azure Storage アカウント間でのディスクのコピー)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP デプロイ用の VM/VHD 構造)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (アタッチされたディスクに対する自動マウントの設定)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP)
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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure のネットワーク)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (ストレージ: Microsoft Azure Storage とデータ ディスク)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP 製品の可用性マトリックス)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Azure Resource Manager テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Azure Resource Manager と PowerShell を使用した仮想マシンの管理)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines は、時間のかかる調達サイクルなしに、最短時間でコンピューティング リソースとストレージ リソースを必要とする組織向けのソリューションです。 Azure Virtual Machines を使用して、SAP NetWeaver ベースのアプリケーションなど、従来のアプリケーションを Azure にデプロイできます。 オンプレミスのリソースを追加せずに、アプリケーションの信頼性と可用性を高めます。 Azure Virtual Machines はクロスプレミス接続をサポートしているので、Azure Virtual Machines を組織のオンプレミスのドメイン、プライベート クラウド、SAP システム ランドスケープに統合できます。

この記事では、代替のデプロイ方法やトラブルシューティングも含め、Azure の仮想マシン (VM) に SAP アプリケーションをデプロイする手順について説明します。 この記事は、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の情報に基づいています。 また、この記事は、SAP ソフトウェアをインストールしてデプロイするときの主要リソースである SAP インストール ドキュメントと SAP Note を補完するものです。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

SAP ソフトウェアのデプロイ用の Azure 仮想マシンをセットアップするには、複数の手順とリソースが必要となります。 作業を開始する前に、Azure の仮想マシンにSAP ソフトウェアをインストールするための前提条件を満たしていることを確認してください。

### <a name="local-computer"></a>ローカル コンピューター

Windows VM または Linux VM を管理するには、PowerShell スクリプトと Azure Portal を使用します。 どちらのツールも、Windows 7 以降のバージョンの Windows を実行しているローカル コンピューターが必要です。 Linux VM だけを管理し、この作業に Linux コンピューターを使用する場合は、Azure CLI を使用できます。

### <a name="internet-connection"></a>インターネット接続

SAP ソフトウェアのデプロイに必要なツールとスクリプトをダウンロードして実行するには、インターネットに接続している必要があります。 また、Azure Extension for SAP が実行されている Azure VM でも、インターネットへのアクセスが必要です。 Azure VM が Azure 仮想ネットワークまたはオンプレミス ドメインに属している場合は、「[プロキシの構成][deployment-guide-configure-proxy]」の説明に従って関連するプロキシ設定が構成されていることを確認してください。

### <a name="microsoft-azure-subscription"></a>Microsoft Azure サブスクリプション

アクティブな Azure アカウントが必要です。

### <a name="topology-and-networking"></a>トポロジとネットワーク

Azure における SAP デプロイのトポロジとアーキテクチャを定義する必要があります。

* 使用する Azure ストレージ アカウント
* SAP システムをデプロイする仮想ネットワーク
* SAP システムをデプロイするリソース グループ
* SAP システムをデプロイする Azure リージョン
* SAP 構成 (2 層または 3 層)
* VM サイズと、VM にマウントする追加データ ディスクの数
* SAP 移送/修正システム (CTS) 構成

SAP ソフトウェア デプロイ プロセスを開始する前に、Azure ストレージ アカウントまたは Azure 仮想ネットワークを作成し、構成します (必要な場合)。 これらのリソースを作成し、構成する方法については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」をご覧ください。

### <a name="sap-sizing"></a>SAP のサイズ設定

SAP のサイズ設定を行うために、次の情報を把握しておきます。

* 予想される SAP ワークロード (たとえば、SAP Quick Sizer ツールを使用して予想) と SAPS (SAP Application Performance Standard) 番号
* SAP システムの必要な CPU リソースとメモリ使用量
* 1 秒あたりの必要な入出力 (I/O) 操作
* Azure の VM 間での最終的な通信に必要なネットワーク帯域幅
* オンプレミス資産と Azure にデプロイされた SAP システム間に必要なネットワーク帯域幅

### <a name="resource-groups"></a>リソース グループ

Azure Resource Manager では、リソース グループを使用して Azure サブスクリプション内のすべてのアプリケーション リソースを管理できます。 詳細については、「[Azure Resource Manager の概要][resource-group-overview]」を参照してください。

## <a name="resources"></a>リソース

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP リソース

SAP ソフトウェア デプロイをセットアップするときには、次の SAP リソースが必要です。

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン

* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [1409604]: Azure 上の Windows に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1984787]: SUSE Linux Enterprise Server 12 に関する一般情報が記載されています。
* SAP Note [2002167]: Red Hat Enterprise Linux 7.x に関する一般情報が記載されています。
* SAP Note [2069760]: Oracle Linux 7.x に関する一般情報が記載されています。
* SAP Note [1999351]: Azure Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* SAP Note [1597355]: Linux のスワップ領域に関する一般情報が記載されています。
* [SAP on Azure SCN ページ](https://wiki.scn.sap.com/wiki/x/Pia7Gg): ニュースと便利なリソースのコレクションです。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Azure PowerShell][azure-ps] の一部である SAP 固有の PowerShell コマンドレット。
* [Azure CLI][azure-cli] の一部である SAP 固有の Azure CLI コマンド。

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows リソース

Microsoft の次の記事では、Azure 上の SAP デプロイについて説明しています。

* [SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP NetWeaver のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Azure VM での SAP ソフトウェアのデプロイ シナリオ

VM および関連付けられているディスクを Azure にデプロイする方法が複数用意されています。 選択したデプロイの種類によって、デプロイする VM を準備する手順が異なる場合があるため、デプロイ方法の違いを理解しておくことが重要です。

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>シナリオ 1:SAP 用 Azure Marketplace から VM をデプロイする

Azure Marketplace で Microsoft またはサード パーティが提供するイメージを使用して VM をデプロイできます。 Marketplace には、Windows Server とさまざまな Linux ディストリビューションの標準 OS イメージが用意されています。 Microsoft SQL Server などのデータベース管理システム (DBMS) SKU を含むイメージをデプロイすることもできます。 DBMS SKU を含むイメージの使用方法の詳細については、「[Azure Virtual Machines 上の SAP NetWeaver – DBMS デプロイ ガイド][dbms-guide]」をご覧ください。

次のフローチャートは、Azure Marketplace から VM をデプロイする際の SAP 固有の一連の手順を示しています。

![Azure Marketplace の VM イメージを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure Portal を使用した仮想マシンの作成

Azure Marketplace のイメージを使用して新しい仮想マシンを作成する最も簡単な方法は、Azure Portal を使用することです。

1.  <https://portal.azure.com/#create/hub> にアクセスします。  または、Azure Portal のメニューの **[+ 新規]** をクリックします。
1.  **[コンピューティング]** を選択し、デプロイするオペレーティング システムの種類 (Windows Server 2012 R2、SUSE Linux Enterprise Server 12 (SLES 12)、Red Hat Enterprise Linux 7.2 (RHEL 7.2)、Oracle Linux 7.2 など) を選びます。 既定のリスト ビューには、サポートされているオペレーティング システムがすべて表示されるわけではありません。 完全な一覧を表示するには、 **[すべて表示]** を選択します。 SAP ソフトウェア デプロイのサポートされているオペレーティング システムの詳細については、SAP Note [1928533] を参照してください。
1.  次のページで使用条件を確認します。
1.  **[デプロイ モデルの選択]** で **[Resource Manager]** を選択します。
1.  **［作成］** を選択します

ウィザードの指示に従って、必要なすべてのリソース (ネットワーク インターフェイスやストレージ アカウントなど) だけでなく、仮想マシンを作成するために必要なパラメーターも設定します。 次のようなパラメーターがあります。

1. **[基本]** :
   * **Name**:リソースの名前 (仮想マシン名)。
   * **VM ディスクの種類**: OS ディスクのディスクの種類を選択します。 データ ディスクに Premium Storage を使いたい場合は、OS ディスクにも Premium Storage を使うことをお勧めします。
   * **[ユーザー名とパスワード]** または **[SSH 公開キー]** : プロビジョニング中に作成されるユーザーのユーザー名とパスワードを入力します。 Linux 仮想マシンの場合、マシンへのサインインに使用する Secure Shell (SSH) 公開キーを入力できます。
   * **サブスクリプション**:新しい仮想マシンをプロビジョニングするために使用するサブスクリプションを選択します。
   * **[リソース グループ]** :VM のリソース グループの名前。 新しいリソース グループの名前を入力することも、既存のリソース グループの名前を入力することもできます。
   * **[場所]** :新しい仮想マシンをデプロイする場所。 仮想マシンをオンプレミス ネットワークに接続する場合は、Azure をオンプレミス ネットワークに接続する仮想ネットワークの場所を必ず選択します。 詳しくは、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の「[Microsoft Azure のネットワーク][planning-guide-microsoft-azure-networking]」をご覧ください。
1. **Size**:

   サポートされている VM の種類の一覧については、SAP Note [1928533] を参照してください。 Azure Premium Storage を使用する場合は、適切な VM の種類を選択してください。 Premium Storage ではすべての VM の種類はサポートしていません。 詳細については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の「[ストレージ: Microsoft Azure Storage とデータ ディスク][planning-guide-storage-microsoft-azure-storage-and-data-disks]」と [SAP ワークロード用の Azure Storage](./planning-guide-storage.md) に関する記事をご覧ください。

1. **設定**:
   * **Storage**
     * **ディスクの種類**: OS ディスクのディスクの種類を選択します。 データ ディスクに Premium Storage を使いたい場合は、OS ディスクにも Premium Storage を使うことをお勧めします。
     * **[マネージド ディスクの使用]** : Managed Disks を使用する場合は、[はい] を選択します。 Managed Disks について詳しくは、計画ガイドの [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) の章をご覧ください。
     * **ストレージ アカウント**: 既存のストレージ アカウントを選択するか、または新しいアカウントを作成します。 すべてのストレージの種類が SAP アプリケーションの実行に対応しているわけではありません。 ストレージの種類の詳細については、「[RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)」を参照してください。
   * **Network**
     * **[仮想ネットワーク]** と **[サブネット]** : 仮想マシンをイントラネットと統合するには、オンプレミスのネットワークに接続されている仮想ネットワークを選択します。
     * **[パブリック IP アドレス]** : 使用するパブリック IP アドレスを選択するか、または新しいパブリック IP アドレスを作成するためのパラメーターを入力します。 パブリック IP アドレスを使用して、インターネット経由で仮想マシンにアクセスできます。 仮想マシンへのアクセスをセキュリティで保護するために、ネットワーク セキュリティ グループも作成してください。
     * **[ネットワーク セキュリティ グループ]** : 詳細については、[ネットワーク セキュリティ グループを使用したネットワーク トラフィック フローの制御][virtual-networks-nsg]に関するページを参照してください。
   * **[拡張機能]** : デプロイに追加することによって、仮想マシン拡張機能をインストールできます。 この手順では、拡張機能を追加する必要はありません。 SAP サポートに必要な拡張機能は、後でインストールします。 このガイドの「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の章をご覧ください。
   * **高可用性**: 可用性セットを選択するか、または新しい可用性セットを作成するためのパラメーターを入力します。 詳細については、「[Azure の可用性セット][planning-guide-3.2.3]」をご覧ください。
   * **Monitoring**
     * **[ブート診断]** : ブート診断には **[無効]** を選択できます。
     * **[ゲスト OS の診断]** : 監視診断には **[無効]** を選択できます。

1. **概要**:

   選択内容を確認し、 **[OK]** をクリックします。

選択したリソース グループに仮想マシンがデプロイされます。

#### <a name="create-a-virtual-machine-by-using-a-template"></a>テンプレートを使用した仮想マシンの作成

[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されている SAP テンプレートのいずれかを使用して仮想マシンを作成できます。 また、[Azure portal][virtual-machines-windows-tutorial]、[PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]、または [Azure CLI][virtual-machines-linux-tutorial] を使用して、仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン 1 台のみ) テンプレート** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  仮想マシンを 1 台だけ使用して 2 層システムを作成する場合は、このテンプレートを使用します。
* [**2 層構成 (仮想マシン 1 台のみ) テンプレート - Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  1 台だけの仮想マシンと Managed Disks を使って 2 層システムを作成する場合は、このテンプレートを使います。
* [**3 層構成 (複数の仮想マシン) テンプレート** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  複数の仮想マシンを使用して 3 層システムを作成する場合は、このテンプレートを使用します。
* [**3 層構成 (複数の仮想マシン) テンプレート - Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  複数の仮想マシンと Managed Disks を使って 3 層システムを作成する場合は、このテンプレートを使います。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]** :
   * **サブスクリプション**:テンプレートをデプロイするために使用するサブスクリプション。
   * **[リソース グループ]** :テンプレートをデプロイするために使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
   * **[場所]** :テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。

1. **設定**:
   * **[SAP System ID] (SAP システム ID)** : SAP システム ID (SID)。
   * **[OS の種類]** : デプロイするオペレーティング システム (Windows Server 2012 R2、SUSE Linux Enterprise Server 12 (SLES 12)、Red Hat Enterprise Linux 7.2 (RHEL 7.2)、Oracle Linux 7.2 など)。

     リスト ビューには、サポートされているオペレーティング システムがすべて表示されるわけではありません。 SAP ソフトウェア デプロイのサポートされているオペレーティング システムの詳細については、SAP Note [1928533] を参照してください。
   * **[SAP system size] (SAP システムのサイズ)** : SAP システムのサイズ。

     新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
   * **[System availability] (システムの可用性)** (3 層テンプレートのみ): システムの可用性。

     高可用性インストールに適した構成用の **HA** を選択します。 ABAP SAP Central Services (ASCS) 用の 2 つのデータベース サーバーと 2 つのサーバーが作成されます。
   * **[ストレージの種類]** (2 層テンプレートのみ): 使用するストレージの種類。

     大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)][2367194]
      * [RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
   * **[管理ユーザー名]** と **[管理パスワード]** : ユーザー名とパスワード。
     仮想マシンへのサインインに使用する新しいユーザーが作成されます。
   * **[New or existing subnet] (新規または既存のサブネット)** : 新しい仮想ネットワークとサブネットを作成するか、または既存のサブネットを使用するかを決定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、 **[Existing (既存)]** を選択します。
   * **[Subnet ID]\(サブネット ID\)** :VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、ID は /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名> のようになります。

1. **使用条件**:  
    法律条項を確認し、同意します。

1. **[購入]** を選択します。

Azure Marketplace のイメージを使用すると、Azure VM エージェントが既定でデプロイされます。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成

オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM では、必要な VM 拡張機能をダウンロードしたり、Azure 用 SAP 拡張機能を介して SAP Host Agent 用の Azure インフラストラクチャ情報を収集したりすることができません。 詳細については、「[プロキシの構成][deployment-guide-configure-proxy]」をご覧ください。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)

Azure デプロイが Azure サイト間 VPN 接続または ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 (「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この作業に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>VM 拡張機能を構成する

SAP で現在の環境がサポートされるように、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、Azure Extension for SAP を設定します。 

#### <a name="post-deployment-steps"></a>デプロイ後の手順

VM を作成してデプロイしたら、必要なソフトウェア コンポーネントを VM にインストールする必要があります。 VM のこの種のデプロイでは、デプロイ後にソフトウェアをインストールすることになるため、インストールするソフトウェアが Azure 内または別の VM 上で既に使用可能であるか、接続可能なディスクとして提供できる必要があります。 または、オンプレミスの資産 (インストール共有) への接続を提供するクロスプレミス シナリオを使用することを検討します。

Azure に VM をデプロイしたら、オンプレミス環境の場合と同じガイドラインとツールに従って VM に SAP ソフトウェアをインストールします。 Azure VM に SAP ソフトウェアをインストールする場合、SAP と Microsoft では、SAP インストール メディアを Azure VHD または Managed Disks にアップロードして保存するか、必要なすべての SAP インストール メディアを格納したファイル サーバーとして機能する Azure VM を作成することを推奨しています。

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>シナリオ 2:SAP のカスタム イメージを使用して VM をデプロイする

オペレーティング システムまたは DBMS のバージョンによってパッチ要件がそれぞれ異なるため、Azure Marketplace で提供されるイメージがニーズに合わない場合があります。 このような場合、後で再度デプロイできるように、独自の OS/DBMS の VM イメージを使用して VM を作成できます。
プライベート イメージを作成する手順は、Windows と Linux で異なります。

---
> ![Windows ロゴ。][Logo_Windows] Windows
>
> 複数の仮想マシンをデプロイするために使用できる Windows イメージを準備するには、オンプレミス VM で Windows 設定 (Windows SID やホスト名など) を抽象化または汎用化する必要があります。 これを行うには、[sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) を使用します。
>
> ![Linux ロゴ。][Logo_Linux] Linux
>
> 複数の仮想マシンをデプロイするために使用できる Linux イメージを準備するには、オンプレミス VM で一部の Linux 設定を抽象化または汎用化する必要があります。 これを行うには、`waagent -deprovision` を使用します。 詳細については、[Azure で実行されている Linux 仮想マシンのキャプチャ][virtual-machines-linux-capture-image]に関する記事、および「[Azure Linux エージェント ユーザー ガイド][virtual-machines-linux-agent-user-guide-command-line-options]」をご覧ください。
>
>

---
カスタム イメージを作成し、そのイメージを使用して複数の新しい VM を作成できます。 方法については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」をご覧ください。 SAP Software Provisioning Manager を使って新しい SAP システムをインストールし、仮想マシンに接続されているディスクからデータベース バックアップを復元するか、Azure Storage からデータベース バックアップを直接復元して (DBMS でサポートされている場合)、データベース コンテンツを設定します。 詳細については、「[SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]」を参照してください。 オンプレミス VM (特に 2 層システム) に SAP システムが既にインストールされている場合は、SAP Software Provisioning Manager でサポートされているシステムの名前変更手順に従って、Azure VM のデプロイ後に SAP システム設定を調整できます (SAP Note [1619720])。 それ以外の場合は、Azure VM のデプロイ後に SAP ソフトウェアをインストールできます。

次のフローチャートは、カスタム イメージから VM をデプロイする際の SAP 固有の一連の手順を示しています。

![Private Marketplace の VM イメージを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure Portal を使用した仮想マシンの作成

管理ディスクのイメージから新しい仮想マシンを作成する最も簡単な方法は、Azure Portal を使うことです。 管理ディスクイメージを作成する方法について詳しくは、「[Azure で一般化された VM の管理対象イメージをキャプチャする](../../windows/capture-image-resource.md)」をご覧ください

1.  <https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> にアクセスします。 または、Azure Portal のメニューで **[イメージ]** を選びます。
1.  デプロイする管理ディスク イメージを選び、 **[VM の作成]** をクリックします

ウィザードの指示に従って、必要なすべてのリソース (ネットワーク インターフェイスやストレージ アカウントなど) だけでなく、仮想マシンを作成するために必要なパラメーターも設定します。 次のようなパラメーターがあります。

1. **[基本]** :
   * **Name**:リソースの名前 (仮想マシン名)。
   * **VM ディスクの種類**: OS ディスクのディスクの種類を選択します。 データ ディスクに Premium Storage を使いたい場合は、OS ディスクにも Premium Storage を使うことをお勧めします。
   * **[ユーザー名とパスワード]** または **[SSH 公開キー]** : プロビジョニング中に作成されるユーザーのユーザー名とパスワードを入力します。 Linux 仮想マシンの場合、マシンへのサインインに使用する Secure Shell (SSH) 公開キーを入力できます。
   * **サブスクリプション**:新しい仮想マシンをプロビジョニングするために使用するサブスクリプションを選択します。
   * **[リソース グループ]** :VM のリソース グループの名前。 新しいリソース グループの名前を入力することも、既存のリソース グループの名前を入力することもできます。
   * **[場所]** :新しい仮想マシンをデプロイする場所。 仮想マシンをオンプレミス ネットワークに接続する場合は、Azure をオンプレミス ネットワークに接続する仮想ネットワークの場所を必ず選択します。 詳しくは、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の「[Microsoft Azure のネットワーク][planning-guide-microsoft-azure-networking]」をご覧ください。
1. **Size**:

   サポートされている VM の種類の一覧については、SAP Note [1928533] を参照してください。 Azure Premium Storage を使用する場合は、適切な VM の種類を選択してください。 Premium Storage ではすべての VM の種類はサポートしていません。 詳細については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の「[ストレージ: Microsoft Azure Storage とデータ ディスク][planning-guide-storage-microsoft-azure-storage-and-data-disks]」と [SAP ワークロード用の Azure Storage](./planning-guide-storage.md) に関する記事をご覧ください。

1. **設定**:
   * **Storage**
     * **ディスクの種類**: OS ディスクのディスクの種類を選択します。 データ ディスクに Premium Storage を使いたい場合は、OS ディスクにも Premium Storage を使うことをお勧めします。
     * **[マネージド ディスクの使用]** : Managed Disks を使用する場合は、[はい] を選択します。 Managed Disks について詳しくは、計画ガイドの [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) の章をご覧ください。
   * **Network**
     * **[仮想ネットワーク]** と **[サブネット]** : 仮想マシンをイントラネットと統合するには、オンプレミスのネットワークに接続されている仮想ネットワークを選択します。
     * **[パブリック IP アドレス]** : 使用するパブリック IP アドレスを選択するか、または新しいパブリック IP アドレスを作成するためのパラメーターを入力します。 パブリック IP アドレスを使用して、インターネット経由で仮想マシンにアクセスできます。 仮想マシンへのアクセスをセキュリティで保護するために、ネットワーク セキュリティ グループも作成してください。
     * **[ネットワーク セキュリティ グループ]** : 詳細については、[ネットワーク セキュリティ グループを使用したネットワーク トラフィック フローの制御][virtual-networks-nsg]に関するページを参照してください。
   * **[拡張機能]** : デプロイに追加することによって、仮想マシン拡張機能をインストールできます。 この手順では、拡張機能を追加する必要はありません。 SAP サポートに必要な拡張機能は、後でインストールします。 このガイドの「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の章をご覧ください。
   * **高可用性**: 可用性セットを選択するか、または新しい可用性セットを作成するためのパラメーターを入力します。 詳細については、「[Azure の可用性セット][planning-guide-3.2.3]」をご覧ください。
   * **Monitoring**
     * **[ブート診断]** : ブート診断には **[無効]** を選択できます。
     * **[ゲスト OS の診断]** : 監視診断には **[無効]** を選択できます。

1. **概要**:

   選択内容を確認し、 **[OK]** をクリックします。

選択したリソース グループに仮想マシンがデプロイされます。

#### <a name="create-a-virtual-machine-by-using-a-template"></a>テンプレートを使用した仮想マシンの作成

Azure Portal からプライベート OS イメージを使用してデプロイを作成するには、次のいずれかの SAP テンプレートを使用します。 これらのテンプレートは、[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されています。 また、[PowerShell][virtual-machines-upload-image-windows-resource-manager] を使用して仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン 1 台のみ) テンプレート** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  仮想マシンを 1 台だけ使用して 2 層システムを作成する場合は、このテンプレートを使用します。
* [**2 層構成 (仮想マシン 1 台のみ) テンプレート - マネージド ディスク イメージ** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  1 台だけの仮想マシンと管理ディスク イメージを使って 2 層システムを作成する場合は、このテンプレートを使います。
* [**3 層構成 (複数の仮想マシン) テンプレート** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  複数の仮想マシンまたは独自の OS イメージを使用して 3 層システムを作成する場合は、このテンプレートを使用します。
* [**3 層構成 (複数の仮想マシン) テンプレート - マネージド ディスク イメージ** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  複数の仮想マシンまたは独自の OS イメージと管理ディスク イメージを使って 3 層システムを作成する場合は、このテンプレートを使用します。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]** :
   * **サブスクリプション**:テンプレートをデプロイするために使用するサブスクリプション。
   * **[リソース グループ]** :テンプレートをデプロイするために使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
   * **[場所]** :テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。
1. **設定**:
   * **[SAP System ID] (SAP システム ID)** : SAP システム ID。
   * **[OS の種類]** : デプロイするオペレーティング システムの種類 (Windows または Linux)。
   * **[SAP system size] (SAP システムのサイズ)** : SAP システムのサイズ。

     新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
   * **[System availability] (システムの可用性)** (3 層テンプレートのみ): システムの可用性。

     高可用性インストールに適した構成用の **HA** を選択します。 ASCS 用の 2 つのデータベース サーバーと 2 つのサーバーが作成されます。
   * **[ストレージの種類]** (2 層テンプレートのみ): 使用するストレージの種類。

     大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)][2367194]
      * [RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
   * **[User image VHD URI] (ユーザー イメージ VHD の URI)** (非管理ディスク イメージ テンプレートのみ): プライベート OS イメージ VHD の URI (https://&lt;アカウント名>.blob.core.windows.net/vhds/userimage.vhd など)。
   * **[User image storage account] (ユーザー イメージのストレージ アカウント)** (非管理ディスク イメージ テンプレートのみ): プライベート OS イメージが格納されるストレージ アカウントの名前 (https://&lt;アカウント名>.blob.core.windows.net/vhds/userimage.vhd の &lt;アカウント名> など)。
   * **[userImageId] (ユーザー イメージ ID)** (マネージド ディスク イメージ テンプレートのみ): 使用するマネージド ディスク イメージの ID
   * **[管理ユーザー名]** と **[管理パスワード]** : ユーザー名とパスワード。

     仮想マシンへのサインインに使用する新しいユーザーが作成されます。
   * **[New or existing subnet] (新規または既存のサブネット)** : 新しい仮想ネットワークとサブネットを作成するか、または既存のサブネットを使用するかを決定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、 **[Existing (既存)]** を選択します。
   * **[Subnet ID]\(サブネット ID\)** :VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、ID は /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名> のようになります。

1. **使用条件**:  
    法律条項を確認し、同意します。

1. **[購入]** を選択します。

#### <a name="install-the-vm-agent-linux-only"></a>VM エージェントのインストール (Linux のみ)

前のセクションで説明したテンプレートを使用するには、ユーザー イメージに Linux エージェントが既にインストールされている必要があります。インストールされていない場合、デプロイは失敗します。 「[Azure VM エージェントのダウンロード、インストール、有効化][deployment-guide-4.4]」の説明に従って、VM エージェントをダウンロードし、ユーザー イメージにインストールします。 テンプレートを使用しない場合は、VM エージェントを後でインストールすることもできます。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)

Azure デプロイが Azure サイト間 VPN 接続または Azure ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 (「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この手順に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成

オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM では、必要な VM 拡張機能をダウンロードしたり、Azure 用 SAP 拡張機能を介して SAP Host Agent 用の Azure インフラストラクチャ情報を収集したりすることができません。「[プロキシの構成][deployment-guide-configure-proxy]」を参照してください。

#### <a name="configure-azure-vm-extension-for-sap"></a>Azure VM Extension for SAP を構成する

SAP で現在の環境がサポートされるように、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、Azure Extension for SAP を設定します。 

### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>シナリオ 3:SAP を含む汎用化されていない Azure VHD を使用してオンプレミス VM を移動する

このシナリオでは、特定の SAP システムをオンプレミス環境から Azure に移行します。 これを行うには、OS、SAP バイナリ、最終的な DBMS バイナリを格納している VHD と、DBMS のデータ ファイルとログ ファイルを格納している VHD を Azure にアップロードします。 「[シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする][deployment-guide-3.3]」で説明されたシナリオとは異なり、この場合は、ホスト名、SAP SID、および SAP ユーザー アカウントがオンプレミスの環境で構成されたため、これらを Azure VM に保持します。 OS を汎用化する必要はありません。 ほとんどの場合、このシナリオは、SAP ランドスケープの一部がオンプレミスで実行され、一部が Azure で実行されるクロスプレミス シナリオに適用されます。

このシナリオでは、VM エージェントはデプロイ時に自動的にインストール **されません**。 Azure で SAP NetWeaver を実行するには VM エージェントと Azure Extension for SAP が必要であるため、仮想マシンの作成後に両方のコンポーネントを手動でダウンロードしてインストールし、有効にする必要があります。

Azure VM エージェントの詳細については、次のリソースを参照してください。

---
> ![Windows ロゴ。][Logo_Windows] Windows
>
> [Azure 仮想マシン エージェントの概要][virtual-machines-windows-agent-user-guide]
>
> ![Linux ロゴ。][Logo_Linux] Linux
>
> [Azure Linux エージェント ユーザー ガイド][virtual-machines-linux-agent-user-guide]
>
>

---

次のフローチャートは、汎用化されていない Azure VHD を使用してオンプレミス VM を移行する際の一連の手順を示しています。

![VM ディスクを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-400]

ディスクが既にアップロードされ、Azure で定義されている場合 (「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」を参照)、以下のセクションで説明する作業を行います。

#### <a name="create-a-virtual-machine"></a>仮想マシンの作成

Azure portal からプライベート OS ディスクを使用してデプロイを作成する場合、[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されている SAP テンプレートを使用します。 また、PowerShell を使用して仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン 1 台のみ) テンプレート** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  仮想マシンを 1 台だけ使用して 2 層システムを作成する場合は、このテンプレートを使用します。
* [**2 層構成 (仮想マシン 1 台のみ) テンプレート - マネージド ディスク** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  1 台だけの仮想マシンと管理ディスクを使って 2 層システムを作成する場合は、このテンプレートを使います。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]** :
   * **サブスクリプション**:テンプレートをデプロイするために使用するサブスクリプション。
   * **[リソース グループ]** :テンプレートをデプロイするために使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
   * **[場所]** :テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。
1. **設定**:
   * **[SAP System ID] (SAP システム ID)** : SAP システム ID。
   * **[OS の種類]** : デプロイするオペレーティング システムの種類 (Windows または Linux)。
   * **[SAP system size] (SAP システムのサイズ)** : SAP システムのサイズ。

     新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
   * **[ストレージの種類]** (2 層テンプレートのみ): 使用するストレージの種類。

     大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)][2367194]
      * [RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
   * **[OS disk VHD URI] (OS ディスク VHD の URI)** (非管理ディスク テンプレートのみ): プライベート OS ディスクの URI (https://&lt; アカウント名>.blob.core.windows.net/vhds/osdisk.vhd など)。
   * **[OS disk Managed Disk ID]\(OS ディスクのマネージド ディスク ID\)** (マネージド ディスク テンプレートのみ):マネージド ディスクの OS ディスクの ID (/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN)
   * **[New or existing subnet] (新規または既存のサブネット)** : 新しい仮想ネットワークとサブネットを作成するか、または既存のサブネットを使用するかを決定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、 **[Existing (既存)]** を選択します。
   * **[Subnet ID]\(サブネット ID\)** :VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、ID は /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名> のようになります。

1. **使用条件**:  
    法律条項を確認し、同意します。

1. **[購入]** を選択します。

#### <a name="install-the-vm-agent"></a>VM エージェントのインストール

前のセクションで説明したテンプレートを使用するには、VM エージェントを OS ディスクにインストールする必要があります。インストールしていない場合、デプロイは失敗します。 「[Azure VM エージェントのダウンロード、インストール、有効化][deployment-guide-4.4]」の説明に従って、VM エージェントをダウンロードし、VM にインストールします。

前のセクションで説明したテンプレートを使わない場合は、VM エージェントを後でインストールすることもできます。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)

Azure デプロイが Azure サイト間 VPN 接続または ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 (「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この作業に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成

オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM では、必要な VM 拡張機能をダウンロードしたり、Azure 用 SAP 拡張機能を介して SAP Host Agent 用の Azure インフラストラクチャ情報を収集したりすることができません。「[プロキシの構成][deployment-guide-configure-proxy]」を参照してください。

#### <a name="configure-azure-vm-extension-for-sap"></a>Azure VM Extension for SAP を構成する

SAP で現在の環境がサポートされるように、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、Azure Extension for SAP を設定します。 

## <a name="detailed-tasks-for-sap-software-deployment"></a>SAP ソフトウェアをデプロイするための作業の詳細

このセクションでは、構成およびデプロイ プロセスで個々の作業を実行する手順について詳しく説明します。

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>オンプレミス ドメインへの VM の参加 (Windows のみ)

オンプレミスの Active Directory と DNS が Azure に拡張されるクロスプレミス シナリオで SAP VM をデプロイする場合、VM がオンプレミス ドメインに参加している必要があります。 オンプレミス ドメインに VM を参加させるための詳細な手順と、オンプレミス ドメインのメンバーになるために必要な追加ソフトウェアは、お客様によって異なります。 通常、オンプレミス ドメインに VM を参加させるには、マルウェア対策ソフトウェアや、バックアップまたは監視ソフトウェアなどの追加ソフトウェアをインストールする必要があります。

このシナリオでは、VM が環境内のドメインに参加したときにインターネット プロキシ設定が適用されている場合に、ゲスト VM の Windows ローカル システム アカウント (S-1-5-18) にも同じプロキシ設定が適用されていることを確認する必要があります。 最も簡単な方法は、ドメイン内のシステムに適用されるドメイン グループ ポリシーを使用してプロキシを適用することです。

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Azure VM エージェントのダウンロード、インストール、有効化

汎用化されていない OS イメージ (Windows System Preparation (sysprep ) ツールで作成されていないイメージなど) からデプロイされた仮想マシンの場合、Azure VM エージェントを手動でダウンロードしてインストールし、有効にする必要があります。

Azure Marketplace から VM をデプロイした場合には、この手順は不要です。 Azure Marketplace のイメージには、Azure VM エージェントが既に含まれています。

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Azure VM エージェントのダウンロード:
   1.  [Azure VM エージェント インストーラー パッケージ](https://go.microsoft.com/fwlink/?LinkId=394789)をダウンロードします。
   1.  パーソナル コンピューターまたはサーバーで、VM エージェント MSI パッケージをローカルに保存します。
1. Azure VM エージェントのインストール:
   1.  リモート デスクトップ プロトコル (RDP) を使用して、デプロイした Azure VM に接続します。
   1.  VM で Windows エクスプローラー ウィンドウを開き、VM エージェントの MSI ファイルのターゲット ディレクトリを選択します。
   1.  Azure VM エージェント インストーラー MSI ファイルを、ローカル コンピューター/サーバーから VM 上の VM エージェントのターゲット ディレクトリにドラッグします。
   1.  VM 上の MSI ファイルをダブルクリックします。
1. オンプレミス ドメインに参加している VM の場合、「[プロキシの構成][deployment-guide-configure-proxy]」で説明するように、最終的なインターネット プロキシ設定が VM の Windows ローカル システム アカウント (S-1-5-18) にも適用されていることを確認します。 VM エージェントはこのコンテキストで実行されるので、Azure に接続できる必要があります。

Azure VM エージェントを更新する際にユーザーの操作は不要です。 VM エージェントは自動的に更新され、VM の再起動を必要としません。

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

次のコマンドを使用して、Linux の VM エージェントをインストールします。

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) または Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

エージェントが既にインストールされている場合、Azure Linux エージェントを更新するには、[VM 上の Azure Linux エージェントを GitHub で最新バージョンに更新する方法][virtual-machines-linux-update-agent]に関する記事に記載されている手順を実行します。

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>プロキシの構成

プロキシを構成する手順は、Windows と Linux で異なります。

#### <a name="windows"></a>Windows

インターネットにアクセスするには、ローカル システム アカウントのプロキシ設定を適切に設定する必要があります。 プロキシ設定がグループ ポリシーで設定されていない場合は、ローカル システム アカウントのプロキシ設定を構成できます。

1. **[スタート]** メニューで「**gpedit.msc**」と入力し、**Enter** キーを押します。
1. **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[Internet Explorer]** を選択します。 **[コンピューター別にプロキシを設定する (ユーザー別ではなく)]** が無効になっているか、構成されていないことを確認します。
1. **コントロール パネル** で、 **[ネットワークと共有センター]**  >  **[インターネット オプション]** に移動します。
1. **[接続]** タブの **[LAN の設定]** をクリックします。
1. **[設定を自動的に検出する]** チェック ボックスをオフにします。
1. **[LAN にプロキシ サーバーを使用する]** チェック ボックスをオンにし、プロキシのアドレストとポートを入力します。
1. **[Advanced] \(詳細設定)**  ボタンを選択します。
1. **[例外]** ボックスに、IP アドレスとして「**168.63.129.16**」と入力します。 **[OK]** を選択します。

#### <a name="linux"></a>Linux

Microsoft Azure ゲスト エージェントの構成ファイル (\\etc\\waagent.conf) で適切なプロキシを構成します。

次のパラメーターを設定します。

1. **HTTP プロキシ ホスト**。 たとえば、**proxy.corp.local** に設定します。

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **HTTP プロキシ ポート**。 たとえば、**80** に設定します。

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. エージェントを再起動します。

   ```console
   sudo service waagent restart
   ```

Azure リポジトリを使用する場合は、これらのリポジトリへのトラフィックがオンプレミスのイントラネットを経由しないようにしてください。 強制トンネリングを有効にするためにユーザー定義ルートを作成した場合は、サイト間 VPN 接続を介さずに、リポジトリへのトラフィックをインターネットに直接ルーティングするルートを追加する必要があります。

また、SAP 用の VM 拡張機能は、インターネットにアクセスできる必要があります。 新しい VM Extension for SAP をインストールしていることを確認し、『VM Extension for Sap インストール ガイド』の「[Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する](vm-extension-for-sap-new.md#fa4428b9-bed6-459a-9dfb-74cc27454481)」の手順に従ってプロキシを構成してください。

* **SLES**

  \\etc\\regionserverclnt.cfg に示されている IP アドレスのルートも追加する必要があります。 次の図は例を示しています。

  ![強制トンネリング][deployment-guide-figure-50]


* **RHEL**

  \\etc\\yum.repos.d\\rhui-load-balancers に示されているホストの IP アドレスのルートも追加する必要があります。 例については、前の図を参照してください。

* **Oracle Linux**

  Azure には Oracle Linux 用のリポジトリはありません。 Oracle Linux 用に独自のリポジトリを構成するか、パブリック リポジトリを使う必要があります。

ユーザー定義ルートの詳細については、[ユーザー定義ルートと IP 転送][virtual-networks-udr-overview]に関する記事をご覧ください。

### <a name="azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Azure Extension for SAP

> [!NOTE]
> 一般的なサポートに関する声明:  
> Azure Extension for SAP のサポートは、SAP のサポート チャネルを通じて提供されます。 Azure Extension for SAP に関するサポートが必要な場合は、[SAP サポート](https://support.sap.com/)でサポート ケースをオープンしてください。 

[Azure 上の SAP 用 VM のデプロイ シナリオ][deployment-guide-3]に関するセクションの説明に従って VM を準備すると、Azure VM エージェントが仮想マシンにインストールされます。 次に、Azure グローバル データセンターの Azure 拡張リポジトリで入手できる Azure Extension for SAP をデプロイします。 詳細については、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」を参照してください。

