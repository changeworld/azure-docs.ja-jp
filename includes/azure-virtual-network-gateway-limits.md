---
title: インクルード ファイル
description: インクルード ファイル
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557310"
---
| リソース                                | 制限        |
|-----------------------------------------|------------------------------|
| VNet アドレス プレフィックス                   | VPN ゲートウェイごとに 600          |
| BGP ルートの集約                    | VPN ゲートウェイごとに 4,000        |
| ローカル ネットワーク ゲートウェイのアドレス プレフィックス  | ローカル ネットワーク ゲートウェイごとに 1,000               |
| S2S 接続                         | [ゲートウェイ SKU によって異なります](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| P2S 接続                         | [ゲートウェイ SKU によって異なります](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| P2S ルートの制限 - IKEv2                 | Windows 以外の場合は 256 **/** Windows の場合は 25           |
| P2S ルートの制限 - OpenVPN               | 1000                         |
| 最大 flows                              | VpnGw1/AZ の場合は 100K  **/**  VpnGw2-4/AZ の場合は 512K|