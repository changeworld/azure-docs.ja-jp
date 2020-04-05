---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77111223"
---
| **ベンダー名** | **デバイス ファミリ** | **ファームウェア バージョン** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (プレビュー)|
|Cisco | ASA | 9\.8 より前の ASA 用 ASA (*) RouteBased (IKEv2- No BGP) |
|Cisco | ASA | 9\.8 以降の ASA 用 ASA RouteBased (IKEv2- No BGP) |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) 必須: NarrowAzureTrafficSelectors (UsePolicyBasedTrafficSelectors オプションを有効にする) と CustomAzurePolicies (IKE/IPsec)
>
