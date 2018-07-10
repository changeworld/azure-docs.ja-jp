---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9cdb608505e594e0020eb33abc869c6bf4b6b263
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2018
ms.locfileid: "30326515"
---
| **ベンダー名** | **デバイス ファミリ** | **ファームウェア バージョン** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (プレビュー)|
|Cisco | ASA | 9.8 より前の ASA 用 ASA (*) RouteBased (IKEv2- No BGP) |
|Cisco | ASA | 9.8 以降の ASA 用 ASA RouteBased (IKEv2- No BGP) |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) 次が必要です: NarrowAzureTrafficSelectors および CustomAzurePolicies (IKE/IPsec)
>