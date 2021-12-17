---
title: SAP ソリューション用 Azure VM 拡張機能を実装する | Microsoft Docs
description: SAP 用の VM 拡張機能をデプロイする方法について説明します。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: bc85e68ed643e8a39f22343cfb939ab803fcdaef
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067355"
---
# <a name="implement-the-azure-vm-extension-for-sap-solutions"></a>SAP ソリューション用 Azure VM 拡張機能を実装する

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[sap-resources]:vm-extension-for-sap.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP リソース)
[new-monitoring]:vm-extension-for-sap.md#38d9f33f-d0af-4b8f-8134-f1f97d656fb6 (SAP 用 VM 拡張機能の新しいバージョン)
[std-extension]:vm-extension-for-sap-standard.md (SAP ソリューション用 Azure VM 拡張機能の標準バージョン)
[new-extension]:vm-extension-for-sap-new.md (SAP ソリューション用 Azure VM 拡張機能の新しいバージョン)
[azure-ps]:/powershell/azure/
[azure-cli]:/cli/azure/install-classic-cli
[azure-cli-2]:/cli/azure/install-azure-cli

[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP)

> [!NOTE]
> 一般的なサポートに関する声明: Azure Extension for SAP のサポートは、SAP のサポート チャネルを通じて提供されます。 SAP ソリューション用 Azure VM 拡張機能に関するサポートが必要な場合は、SAP サポートでサポート ケースを開いてください。
 
[Azure 上の SAP 用 VM のデプロイ シナリオ][deployment-guide-3]に関するセクションの説明に従って VM を準備すると、Azure VM エージェントが仮想マシンにインストールされます。 次に、Azure グローバル データセンターの Azure 拡張リポジトリで入手できる Azure Extension for SAP をデプロイします。 詳しくは、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide-9.1]」をご覧ください。
 
SAP で現在の環境がサポートされるように、Azure Extension for SAP を構成する方法に関するページの説明に従って、SAP ソリューション用 Azure VM 拡張機能を有効にします。

## <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP リソース

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
* SAP Note [1999351]: Azure Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [Azure PowerShell][azure-ps] の一部である SAP 固有の PowerShell コマンドレット。
* [Azure CLI][azure-cli-2] の一部である SAP 固有の Azure CLI コマンド。
 
## <a name="differences-between-the-two-versions-of-the-azure-vm-extension-for-sap-solutions"></a>SAP ソリューション用 Azure VM 拡張機能の 2 つのバージョンの違い

SAP 用 VM 拡張機能には 2 つのバージョンがあります。 「[SAP リソース][sap-resources]」で示されているリソースの一覧で、SAP の前提条件と、SAP カーネルおよび SAP Host Agent の必要最小バージョンを確認してください。

### <a name="standard-version-of-vm-extension-for-sap"></a>SAP 用 VM 拡張機能の標準バージョン

このバージョンは、現在の標準的な SAP 用 VM 拡張機能です。 例外的に Microsoft が新しい SAP 用 VM 拡張機能のインストールを推奨する場合があります。 また、サポート ケースを開く際に、SAP サポートは新しい VM 拡張機能のインストールを要求することができます。 新バージョンの SAP 用 VM 拡張機能を使用する場合の詳細については、「[SAP 用 VM 拡張機能の新しいバージョン][new-monitoring]」のチャプターを参照してください
 
### <a name="new-version-of-vm-extension-for-sap"></a><a name="38d9f33f-d0af-4b8f-8134-f1f97d656fb6"></a>SAP 用 VM 拡張機能の新しいバージョン

このバージョンは、新しい SAP ソリューション用 Azure VM 拡張機能です。 さらなる機能強化と新しい Azure のオファリングにより、この新しい拡張機能は、仮想マシンのすべての Azure リソースを監視できるように構築されました。 この拡張機能には、URL "management.azure.com" へのインターネット アクセスが必要です。 Standard Disks やオペレーティング システムなど、追加のストレージ オプションがサポートされています。 次のいずれかが該当する場合は、新しいバージョンの VM 拡張機能を選択してください。
 
* Terraform、Azure Resource Manager テンプレート、または Azure CLI、Azure PowerShell 以外のその他の方法を使用して VM 拡張機能をインストールする場合
* SUSE SLES 15 以降に拡張機能をインストールする場合。
* Red Hat Enterprise Linux 8.1 に拡張機能をインストールする場合。
* Azure Ultra Disk または Standard Managed Disks を使用する場合
* Microsoft または SAP サポートから新しい拡張機能をインストールするように求められた場合
 
## <a name="recommendation"></a>推奨

現在、新バージョンの拡張機能のユース ケースに該当しないインストールには、標準バージョンの拡張機能を使用することを推奨しています。 現在、新しいバージョンの VM 拡張機能を改善してこれを既定にし、標準バージョンの拡張機能を非推奨にするよう取り組んでいます。 この期間中は、新しいバージョンを使用することができます。 ただし、VM 拡張機能から management.azure.com にアクセスできることを確認する必要があります。
 
> [!NOTE]
> 2 つのバージョンを切り替える際は、必ず前もって VM 拡張機能をアンインストールするようにしてください。

## <a name="next-steps"></a>次のステップ
* [SAP ソリューション用 Azure VM 拡張機能の標準バージョン][std-extension]
* [SAP ソリューション用 Azure VM 拡張機能の新しいバージョン][new-extension]

