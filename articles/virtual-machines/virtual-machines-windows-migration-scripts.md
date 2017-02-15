---
title: "Azure サービス管理の Azure Resource Manager への移行用のコミュニティ ツール"
description: "この記事には、Azure サービス管理から Azure Resource Manager スタックへの IaaS リソースの移行を支援するコミュニティで提供されているツールが掲載されています。"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 032a7068cce78036caec587bfc53abddf5a36659


---
# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Azure サービス管理の Azure Resource Manager への移行用のコミュニティ ツール
この記事には、Azure サービス管理から Azure Resource Manager スタックへの IaaS リソースの移行を支援するコミュニティで提供されているツールが掲載されています。

> [!NOTE]
> Microsoft サポートは これらのツールを正式にサポートしていません。 そのため、PowerShell スクリプトは Github でオープンソースになっており、修正プログラムや追加シナリオの PR を歓迎しています。 問題をレポートするには、Github の問題の機能を使用します。
> 
> これらのツールによる移行でクラシック仮想マシンにダウンタイムが発生します。 プラットフォームでサポートされている移行については、次を参照してください。 
> 
> * [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
> * [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
これは、 **1 つ** の仮想マシン (VM) を Azure サービス管理スタックから Azure Resource Manager スタックに移行するための PowerShell スクリプトのモジュールです。 

[ツールのドキュメントへのリンク](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
migAz は、一連の Azure サービス管理の IaaS リソースを Azure Resource Manager の IaaS リソースに移行する追加のオプションです。 移行は、同じサブスクリプション内、または別のサブスクリプションとサブスクリプションの種類 (例: CSP サブスクリプション) の間で発生する可能性があります。

[ツールのドキュメントへのリンク](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)




<!--HONumber=Nov16_HO3-->


