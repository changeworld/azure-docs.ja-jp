---
title: 可用性ゾーンの概要 | Microsoft Docs
description: この記事では、Azure の可用性ゾーンの概要を紹介します。
services: ''
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2018
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: b4db442a54b4360b75df40156ca0d4e4ee1eb0d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Azure の可用性ゾーン (プレビュー) の概要

可用性ゾーンは、データセンターレベルの障害からの保護に役立ちます。 1 つの Azure リージョンに配置され、それぞれが独自の電源、ネットワーク、冷却装置を備えています。 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。 可用性ゾーンは 1 リージョン内で物理的にも論理的にも分離されているため、ゾーンレベルで障害が発生した場合でもアプリケーションとデータを保護できます。 

![1 リージョン内でゾーンが 1 つダウンした場合の概念図](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>可用性ゾーンをサポートしているリージョン

- 米国東部 2
- 米国中部
- 西ヨーロッパ
- フランス中部
- 東南アジア

## <a name="services-that-support-availability-zones"></a>可用性ゾーンをサポートしているサービス

可用性ゾーンをサポートしている Azure サービスは、次のとおりです:

- Linux 仮想マシン
- Windows 仮想マシン
- Virtual Machine Scale Sets
- Managed Disks
- Load Balancer
- パブリック IP アドレス
- ゾーン冗長ストレージ
- SQL Database

## <a name="get-started-with-the-availability-zones-preview"></a>可用性ゾーン (プレビュー) の使用を開始する

可用性ゾーン (プレビュー) は、米国東部 2、米国中部、西ヨーロッパ、フランス中部のリージョンで特定の Azure サービスでのみ利用できます。 

1. [可用性ゾーン (プレビュー) にサインアップします](http://aka.ms/azenroll) 
2. Azure サブスクリプションにサインインします。
3. 可用性ゾーンをサポートしているリージョンを選択します。
4. 以下のいずれかのリンクを使って、利用中のサービスで可用性ゾーンの使用を開始します。 
    - [仮想マシンの作成](../virtual-machines/windows/create-portal-availability-zone.md)
    - [仮想マシン スケール セットを作成する](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
    - [PowerShell を使った管理ディスクの追加](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Load Balancer](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>次の手順
- [クイック スタート テンプレート](http://aka.ms/azqs)
