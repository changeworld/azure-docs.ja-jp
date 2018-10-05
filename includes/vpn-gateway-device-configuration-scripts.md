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
ms.openlocfilehash: 00889717e0c22477b9933725bccc7de05c82bc4f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454415"
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
> (*) 必須: NarrowAzureTrafficSelectors (UsePolicyBasedTrafficSelectors オプションを有効にする) と CustomAzurePolicies (IKE/IPsec)
>
